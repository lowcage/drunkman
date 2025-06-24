<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const finalNumber = ref(1)
const currentNumber = ref(1)
const spinning = ref(false)
const leverPulled = ref(false)
const leverPosition = ref(0) // 0 = top, 1 = bottom
const rotationAngle = ref(0)

// All possible numbers for the drum
const drumNumbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]

// Calculate which numbers are visible (both during spinning and when stopped)
const visibleNumbers = computed(() => {
  const degreesPerNumber = 40
  const currentPosition = rotationAngle.value % (9 * degreesPerNumber)
  const baseIndex = Math.floor(currentPosition / degreesPerNumber)
  const offset = currentPosition % degreesPerNumber

  const numbers = []

  // Show 5 numbers for smoother transitions
  for (let i = -2; i <= 2; i++) {
    const index = (baseIndex + i + 9) % 9
    const angleOffset = (i * degreesPerNumber) - offset
    const distance = Math.abs(angleOffset)

    // Different opacity and blur for spinning vs stopped
    let opacity, blur
    if (spinning.value) {
      opacity = Math.max(0.1, 1 - distance / 80)
      blur = distance > 20 ? Math.min(distance / 10, 6) : 0
    } else {
      // When stopped, show center number clearly and adjacent numbers blurred
      if (distance < 5) {
        opacity = 1
        blur = 0
      } else if (distance < 45) {
        opacity = Math.max(0.3, 1 - distance / 60)
        blur = Math.min(distance / 8, 4)
      } else {
        opacity = 0
        blur = 0
      }
    }

    numbers.push({
      number: drumNumbers[index],
      angle: angleOffset,
      opacity,
      blur
    })
  }

  return numbers.filter(n => n.opacity > 0 && Math.abs(n.angle) < 120)
})

// Easing functions
function easeOutQuad(t) {
  return t * (2 - t)
}

function easeInOutCubic(t) {
  return t < 0.5 ? 4 * t * t * t : 1 - Math.pow(-2 * t + 2, 3) / 2
}

function easeOutQuart(t) {
  return 1 - Math.pow(1 - t, 4)
}

function pullLever() {
  if (spinning.value) return

  leverPulled.value = true
  spinning.value = true

  // Generate final result
  finalNumber.value = Math.floor(Math.random() * 9) + 1

  // Lever animation: smooth down then back up
  const leverAnimation = () => {
    const durationDown = 200
    const durationUp = 600
    const totalDuration = durationDown + durationUp
    const startTime = Date.now()

    const animateLever = () => {
      const elapsed = Date.now() - startTime

      if (elapsed < durationDown) {
        const t = elapsed / durationDown
        leverPosition.value = easeOutQuad(t)
      } else if (elapsed < totalDuration) {
        const t = (elapsed - durationDown) / durationUp
        leverPosition.value = 1 - easeInOutCubic(t)
      } else {
        leverPosition.value = 0
        leverPulled.value = false
        return
      }

      requestAnimationFrame(animateLever)
    }

    requestAnimationFrame(animateLever)
  }

  // Drum spinning animation - always goes to target perfectly
  const drumAnimation = () => {
    const totalSpinTime = 2500
    const startTime = Date.now()
    const startRotation = rotationAngle.value

    // Calculate target position (always perfect alignment)
    const targetPosition = (finalNumber.value - 1) * 40
    const totalRotations = 8

    // Calculate the shortest path to target, accounting for current position
    const currentPosition = startRotation % 360
    const targetPos = targetPosition % 360
    let shortestPath = targetPos - currentPosition

    if (shortestPath > 180) shortestPath -= 360
    if (shortestPath < -180) shortestPath += 360

    const targetRotation = startRotation + (totalRotations * 360) + shortestPath

    const animateDrum = () => {
      const elapsed = Date.now() - startTime
      const progress = Math.min(elapsed / totalSpinTime, 1)
      const easedProgress = easeOutQuart(progress)

      rotationAngle.value = startRotation + (targetRotation - startRotation) * easedProgress

      if (progress < 1) {
        requestAnimationFrame(animateDrum)
      } else {
        // Ensure exact final position and update current number immediately
        rotationAngle.value = targetRotation
        currentNumber.value = finalNumber.value
        setTimeout(() => {
          spinning.value = false
        }, 100)
      }
    }

    requestAnimationFrame(animateDrum)
  }

  // Start animations
  leverAnimation()
  setTimeout(drumAnimation, 150)
}

onMounted(() => {
  window.addEventListener('keydown', handleKeydown)
})

onBeforeUnmount(() => {
  window.removeEventListener('keydown', handleKeydown)
})

function handleKeydown(e) {
  if (e.code === 'Space') {
    e.preventDefault() // Megakadályozza az oldal scrollozását
    pullLever()
  }
}

</script>

<template>
  <div class="flex flex-col items-center justify-center w-full h-full relative">

    <!-- Slot Machine Body -->
    <div class="relative bg-gradient-to-b from-red-800 via-red-900 to-red-950 rounded-2xl p-6 shadow-2xl border-4 border-yellow-500">

      <!-- Machine Top -->
      <div class="absolute -top-4 left-1/2 -translate-x-1/2 w-12 h-8 bg-gradient-to-b from-red-700 to-red-800 rounded-t-xl border-2 border-yellow-500"></div>

      <!-- Display Window Frame -->
      <div class="relative mb-6 p-2 bg-gradient-to-b from-gray-800 to-gray-900 rounded-xl shadow-inner">

        <!-- Inner Display -->
        <div class="relative w-24 h-24 md:w-28 md:h-28 bg-black rounded-lg border-2 border-gray-600 shadow-inner overflow-hidden">

          <!-- 3D Drum Effect -->
          <div class="absolute inset-0 flex items-center justify-center perspective-500">
            <div
              class="relative w-full h-full"
              style="transform-style: preserve-3d;"
            >

              <!-- All Numbers (spinning and static with blur effect) -->
              <div
                v-for="(item, index) in visibleNumbers"
                :key="`number-${index}-${item.number}`"
                class="absolute inset-0 flex items-center justify-center text-4xl md:text-5xl font-bold text-yellow-400 transition-all duration-75"
                :class="{ 'text-shadow-glow': !spinning && item.blur === 0 }"
                :style="{
                  transform: `rotateX(${item.angle}deg) translateZ(50px)`,
                  opacity: item.opacity,
                  filter: item.blur > 0 ? `blur(${item.blur}px)` : 'none'
                }"
              >
                {{ item.number }}
              </div>

            </div>
          </div>



          <!-- Display Glass Reflection -->
          <div class="absolute inset-0 bg-gradient-to-br from-white to-transparent opacity-20 rounded-lg pointer-events-none"></div>

        </div>

        <!-- Frame Decorations -->
        <div class="absolute -top-1 -left-1 w-4 h-4 bg-yellow-400 rounded-full shadow-lg"
             :class="{ 'animate-pulse': !spinning }"></div>
        <div class="absolute -top-1 -right-1 w-4 h-4 bg-yellow-400 rounded-full shadow-lg"
             :class="{ 'animate-pulse': !spinning }"></div>
        <div class="absolute -bottom-1 -left-1 w-4 h-4 bg-yellow-400 rounded-full shadow-lg"
             :class="{ 'animate-pulse': !spinning }"></div>
        <div class="absolute -bottom-1 -right-1 w-4 h-4 bg-yellow-400 rounded-full shadow-lg"
             :class="{ 'animate-pulse': !spinning }"></div>
      </div>

      <!-- Machine Details -->
      <div class="flex justify-center space-x-3 mb-4">
        <div class="w-3 h-3 bg-yellow-400 rounded-full shadow-lg"></div>
        <div class="w-3 h-3 bg-red-500 rounded-full shadow-lg"></div>
        <div class="w-3 h-3 bg-green-500 rounded-full shadow-lg"></div>
      </div>

      <!-- Machine Base -->
      <div class="h-4 bg-gradient-to-b from-red-900 to-red-950 rounded-b-xl -mx-2 -mb-2"></div>
    </div>

    <!-- Lever Mechanism -->
    <div class="absolute right-6 top-8 translate-x-2 flex flex-col items-center">

      <!-- Lever Track/Housing -->
      <div class="relative w-6 h-28 bg-gradient-to-b from-gray-600 to-gray-800 rounded-full shadow-lg border-2 border-gray-500">
        <!-- Track groove -->
        <div class="absolute inset-x-1 top-2 bottom-2 bg-gray-900 rounded-full shadow-inner"></div>

        <!-- Track guides -->
        <div class="absolute top-3 left-1/2 -translate-x-1/2 w-1 h-1 bg-gray-400 rounded-full"></div>
        <div class="absolute bottom-3 left-1/2 -translate-x-1/2 w-1 h-1 bg-gray-400 rounded-full"></div>
      </div>

      <!-- Lever Handle -->
      <div
        @click="pullLever"
        class="absolute w-10 h-10 cursor-pointer transition-all duration-100 z-10 lever-handle"
        :class="{
          'cursor-not-allowed': spinning,
          'scale-95': leverPulled
        }"
        :style="{
          top: `${leverPosition * 280}px`,
        }"
      >
        <!-- Handle Shadow -->
        <div class="absolute inset-0 bg-black opacity-40 rounded-full blur-sm translate-x-0.5 translate-y-0.5"></div>

        <!-- Handle Body -->
        <div class="relative w-full h-full bg-gradient-to-b from-red-400 via-red-500 to-red-700 rounded-full border-2 border-white shadow-lg">
          <!-- Handle Shine -->
          <div class="absolute top-1.5 left-2 w-4 h-2 bg-white opacity-70 rounded-full blur-sm"></div>

          <!-- Handle Grip Lines -->
          <div class="absolute inset-2 flex flex-col justify-center space-y-0.5">
            <div class="h-0.5 bg-red-800 rounded-full opacity-60"></div>
            <div class="h-0.5 bg-red-800 rounded-full opacity-60"></div>
            <div class="h-0.5 bg-red-800 rounded-full opacity-60"></div>
            <div class="h-0.5 bg-red-800 rounded-full opacity-60"></div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<style scoped>
.text-shadow-glow {
  text-shadow:
    0 0 15px currentColor,
    0 0 30px currentColor,
    0 0 45px currentColor;
}

.perspective-500 {
  perspective: 500px;
}



/* Lever hover effects */
.lever-handle:not(.cursor-not-allowed):hover {
  filter: brightness(1.1);
  transform: scale(1.05);
}

.lever-handle:not(.cursor-not-allowed):active {
  filter: brightness(0.9);
}

/* Prevent text selection */
.lever-handle {
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
</style>
