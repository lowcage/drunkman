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

// Generate crimped edge points for beer cap
function getBeerCapPath(radius) {
  const points = []
  const crimpCount = 24 // Number of crimps around the edge
  const innerRadius = radius - 6
  const outerRadius = radius - 2

  for (let i = 0; i < crimpCount; i++) {
    const angle = (i / crimpCount) * 2 * Math.PI
    const isOuter = i % 2 === 0
    const r = isOuter ? outerRadius : innerRadius
    const x = Math.cos(angle) * r
    const y = Math.sin(angle) * r
    points.push(`${x},${y}`)
  }

  return `M ${points.join(' L ')} Z`
}
</script>

<template>
  <g class="normal-tile">
    <!-- Crimped edge with metallic gradient -->
    <path
        :d="getBeerCapPath(size / 2 - 2)"
        fill="url(#greenMetallicGradient)"
        stroke="#1b5e20"
        stroke-width="2"
        filter="url(#shadow3d)"
        class="transition-all"
    />

    <!-- Inner circle with metallic shine -->
    <circle
        :r="size / 2 - 12"
        fill="url(#innerGreenGradient)"
        stroke="#2e7d32"
        stroke-width="1.5"
    />

    <!-- Metallic highlight for 3D effect -->
    <ellipse
        :rx="size / 2 - 18"
        :ry="size / 2 - 22"
        cx="-3"
        cy="-3"
        fill="url(#metallicShine)"
        opacity="0.7"
    />

    <!-- Center rim highlight -->
    <circle
        :r="size / 2 - 20"
        fill="none"
        stroke="#a5d6a7"
        stroke-width="1"
        opacity="0.8"
    />

    <!-- Tile number -->
    <text
        text-anchor="middle"
        dy="7"
        font-size="16"
        fill="white"
        font-weight="bold"
        style="text-shadow: 2px 2px 4px rgba(0,0,0,0.9)"
    >
      {{ tile.id }}
    </text>
  </g>
</template>