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
  <g class="chest-tile casino-chest" filter="url(#shadow3d)">
    <!-- Main chest body - bigger, fills more tile space -->
    <rect
        x="-24" y="2" width="48" height="26" rx="3"
        fill="url(#darkGoldGradient)"
        stroke="#654321"
        stroke-width="2"
    />

    <!-- Chest lid - bigger curved top -->
    <path
        d="M-24 2 Q-24 -16 0 -18 Q24 -16 24 2 Z"
        fill="url(#goldGradient)"
        stroke="#8b6914"
        stroke-width="2"
    />

    <!-- Premium treasure glow inside -->
    <ellipse
        cx="0" cy="-8" rx="18" ry="6"
        fill="#ffed4e"
        opacity="0.9"
    />

    <!-- Stylish background for diamond -->
    <circle cx="0" cy="-8" r="6" fill="#ffaa00" opacity="0.4"/>
    <circle cx="0" cy="-8" r="5" fill="#ff8800" opacity="0.3"/>

    <!-- Simple diamond in treasure glow -->
    <path
        d="M0,-12 L3,-8 L0,-4 L-3,-8 Z"
        fill="#4da6ff"
        opacity="1"
        stroke="#0080ff"
        stroke-width="0.5"
    />

    <!-- Diamond glow effect -->
    <path
        d="M0,-12 L3,-8 L0,-4 L-3,-8 Z"
        fill="none"
        stroke="#66b3ff"
        stroke-width="2"
        opacity="0.6"
    />

    <path
        d="M0,-12 L3,-8 L0,-4 L-3,-8 Z"
        fill="none"
        stroke="#99ccff"
        stroke-width="3"
        opacity="0.3"
    />

    <!-- Large central lock - more prominent -->
    <rect
        x="-7" y="12" width="14" height="12" rx="3"
        fill="#2c2c2c"
        stroke="#1a1a1a"
        stroke-width="1.5"
    />

    <!-- Big keyhole with more detail -->
    <circle cx="0" cy="17" r="3" fill="#000"/>
    <rect x="-1.5" y="17" width="3" height="5" fill="#000"/>

    <!-- Corner reinforcements for premium look -->
    <circle cx="-20" cy="6" r="2" fill="#8b6914"/>
    <circle cx="20" cy="6" r="2" fill="#8b6914"/>
    <circle cx="-20" cy="24" r="2" fill="#8b6914"/>
    <circle cx="20" cy="24" r="2" fill="#8b6914"/>
  </g>
</template>