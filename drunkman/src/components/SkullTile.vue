<script setup>
import { ref, computed, watch } from 'vue'

/* ---------- props ---------- */
const props = defineProps({
  tile: { type: Object, required: true },
  size: { type: Number, required: true },
  active: { type: Boolean, default: false }  // 👈 NEW — handed down from GameTile
})

/* ---------- geometry ---------- */
const lineLength = computed(() => (props.size - 16) * Math.SQRT2)

/* ---------- reactive state ---------- */
const dashOffset = ref(0)        // 0  ⇒ line fully visible

/* ---------- animation ---------- */
function playProhibitionAnimation() {
  // 1️⃣ erase the line for a single frame
  dashOffset.value = lineLength.value

  const start = performance.now()
  const duration = 600          // ms

  function step(now) {
    const t = Math.min((now - start) / duration, 1)
    dashOffset.value = lineLength.value * (1 - t)   // 1 ➜ 0

    if (t < 1) requestAnimationFrame(step)
  }

  requestAnimationFrame(step)
}

/* trigger whenever active goes from false ➜ true */
watch(() => props.active, (newActive) => {
  if (newActive) playProhibitionAnimation()
})

// No more defineExpose needed!
</script>

<template>
  <g class="skull-tile no-alcohol-prohibition" filter="url(#shadow3d)">
    <!-- Background -->
    <circle :r="size / 2 - 4" fill="#1a1a1a" stroke="#333" stroke-width="2" />

    <!-- Wine glass -->
    <g transform="translate(0, -3)">
      <path d="M-8 -5 Q-8 5 -3 10 L3 10 Q8 5 8 -5 L8 -10 L-8 -10 Z" fill="white" />
      <rect x="-1" y="10" width="2" height="8" fill="white" />
      <rect x="-6" y="18" width="12" height="2" rx="1" fill="white" />
    </g>

    <!-- Circle ring -->
    <circle
        :r="size / 2 - 4"
        fill="none"
        stroke="url(#prohibitionGradient)"
        stroke-width="4"
    />

    <!-- Animated red line -->
    <line
        :x1="`${-(size / 2 - 8) * 0.707}`"
        :y1="`${-(size / 2 - 8) * 0.707}`"
        :x2="`${ (size / 2 - 8) * 0.707}`"
        :y2="`${ (size / 2 - 8) * 0.707}`"
        stroke="url(#prohibitionGradient)"
        stroke-width="4"
        stroke-linecap="round"
        :stroke-dasharray="lineLength"
        :stroke-dashoffset="dashOffset"
    />
  </g>
</template>