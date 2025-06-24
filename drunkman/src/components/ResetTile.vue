<script setup>
import {ref, defineExpose} from 'vue'

const props = defineProps({
  tile: {
    type: Object,
    required: true
  },
  size: {
    type: Number,
    required: true
  }
})

const isAnimating = ref(false)
const rotationAngle = ref(0)

// Animation function that can be called from outside
function playResetAnimation() {
  console.log('Animation triggered')

  if (isAnimating.value) return // Prevent multiple animations at once

  isAnimating.value = true
  rotationAngle.value = 0

  const duration = 2000 // 1 second
  const startTime = Date.now()
  const totalRotation = 360 // Full rotation

  const animate = () => {
    const elapsed = Date.now() - startTime
    const progress = Math.min(elapsed / duration, 1)

    // Easing function for smooth animation (ease-out)
    const easedProgress = 1 - Math.pow(1 - progress, 3)

    rotationAngle.value = totalRotation * easedProgress

    if (progress < 1) {
      requestAnimationFrame(animate)
    } else {
      // Animation complete
      rotationAngle.value = 0 // Reset to original position
      isAnimating.value = false
    }
  }

  requestAnimationFrame(animate)
}

// Expose the animation function so parent components can call it
defineExpose({
  playResetAnimation
})
</script>
<template>
  <g class="reset-tile reset-arrow">
    <!-- Base circle background -->
    <circle
        :r="size / 2 - 6"
        fill="url(#innerGreenGradient)"
        stroke="#e74c3c"
        stroke-width="3"
        filter="url(#shadow3d)"
    />

    <!-- Arrow group -->
    <g :transform="`translate(${size / 2-31.5}, ${size / 2-31.5})`">
      <g :transform="`rotate(${rotationAngle}) scale(0.35) translate(-45, -45)`">
        <path
            d="M 75.702 53.014 c -2.142 7.995 -7.27 14.678 -14.439 18.816 c -7.168 4.138 -15.519 5.239 -23.514 3.095 c -16.505 -4.423 -26.335 -21.448 -21.913 -37.953 C 20.258 20.467 37.286 10.64 53.79 15.06 c 4.213 1.129 8.076 3.118 11.413 5.809 l -8.349 8.35 h 26.654 V 2.565 l -8.354 8.354 c -5.1 -4.405 -11.133 -7.61 -17.74 -9.381 C 33.451 -4.882 8.735 9.389 2.314 33.35 c -6.42 23.961 7.851 48.678 31.811 55.098 C 38.001 89.486 41.934 90 45.842 90 c 7.795 0 15.488 -2.044 22.42 -6.046 c 10.407 -6.008 17.851 -15.709 20.962 -27.317 L 75.702 53.014 z"
            fill="#e74c3c"
            stroke="none"
        />
      </g>
    </g>
  </g>
</template>





