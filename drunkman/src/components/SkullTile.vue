<script setup>
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
</script>

<template>
  <g class="skull-tile no-alcohol-prohibition" filter="url(#shadow3d)">
    <!-- Black background circle -->
    <circle
        :r="size / 2 - 4"
        fill="#1a1a1a"
        stroke="#333"
        stroke-width="2"
    />

    <!-- Wine glass centered and moved up slightly -->
    <g transform="translate(0, -3)">
      <!-- Glass bowl -->
      <path
          d="M-8 -5 Q-8 5 -3 10 L3 10 Q8 5 8 -5 L8 -10 L-8 -10 Z"
          fill="white"
          stroke="none"
      />
      <!-- Glass stem -->
      <rect x="-1" y="10" width="2" height="8" fill="white"/>
      <!-- Glass base -->
      <rect x="-6" y="18" width="12" height="2" rx="1" fill="white"/>
    </g>

    <!-- Prohibition circle and line as one cohesive element -->
    <g>
      <!-- Prohibition circle -->
      <circle
          :r="size / 2 - 4"
          fill="none"
          stroke="url(#prohibitionGradient)"
          stroke-width="4"
      />

      <!-- Diagonal prohibition line - starts and ends on inner edge of ring -->
      <line
          :x1="`${-(size / 2 - 8) * 0.707}`"
          :y1="`${-(size / 2 - 8) * 0.707}`"
          :x2="`${(size / 2 - 8) * 0.707}`"
          :y2="`${(size / 2 - 8) * 0.707}`"
          stroke="url(#prohibitionGradient)"
          stroke-width="4"
      />
    </g>
  </g>
</template>