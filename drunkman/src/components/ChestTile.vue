<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  tile  : { type: Object,  required: true },
  size  : { type: Number,  required: true },
  active: { type: Boolean, default : false }
})

/* ---- animation state ---- */
const lidOffset   = ref(0)   // 0 → -12  (lid lift)
const raysOpacity = ref(0)   // 0 → 1    (burst fade-in)
const busy        = ref(false)

/* helper to tween values */
const tween = (from, to, dur, set) =>
    new Promise(res => {
      const start = performance.now()
      const step  = now => {
        const t = Math.min((now - start) / dur, 1)
        set(from + (to - from) * t)
        t < 1 ? requestAnimationFrame(step) : res()
      }
      requestAnimationFrame(step)
    })

function playChestAnimation () {
  if (busy.value) return
  busy.value = true

  const LIFT  = -12
  const D_UP  = 350
  const D_HOLD= 2000
  const D_DOWN= 350

  ;(async () => {
    await Promise.all([
      tween(0,    LIFT, D_UP,  v => lidOffset.value   = v),
      tween(0,      1, D_UP,   v => raysOpacity.value = v)
    ])
    await new Promise(r => setTimeout(r, D_HOLD))
    await Promise.all([
      tween(LIFT,  0,  D_DOWN, v => lidOffset.value   = v),
      tween(1,     0,  D_DOWN, v => raysOpacity.value = v)
    ])
    busy.value = false
  })()
}

watch(() => props.active, a => { if (a) playChestAnimation() })
</script>

<template>
  <g class="chest-tile casino-chest" filter="url(#shadow3d)">
    <!--  ░░░  defs for glow/rays  ░░░ -->
    <defs>
      <!-- bright radial halo -->
      <radialGradient id="haloGrad" cx="50%" cy="60%" r="70%">
        <stop offset="0%"   stop-color="#fffdb3"/>
        <stop offset="40%"  stop-color="#ffe65b"/>
        <stop offset="100%" stop-color="#ffe65b" stop-opacity="0"/>
      </radialGradient>

      <!-- golden rays that fade out -->
      <linearGradient id="rayGrad" x1="0" y1="0" x2="0" y2="1">
        <stop offset="0%"   stop-color="#ffec62"/>
        <stop offset="100%" stop-color="#ffec62" stop-opacity="0"/>
      </linearGradient>

      <!-- slight blur so rays look soft -->
      <filter id="softBlur" x="-60%" y="-60%" width="220%" height="220%">
        <feGaussianBlur stdDeviation="1.6"/>
      </filter>
    </defs>

    <!-- ✨ treasure burst (behind lid & body) -->
    <g :opacity="raysOpacity" filter="url(#softBlur)">
      <!-- halo -->
      <ellipse cx="0" cy="-4" rx="22" ry="10" fill="url(#haloGrad)"/>
      <!-- 12 rays, every 30° -->
      <g v-for="n in 12" :key="n" :transform="`rotate(${(n-1)*30})`">
        <rect x="-1" y="-22" width="2" height="22" fill="url(#rayGrad)"/>
      </g>
    </g>

    <!-- ─── lid (slides up) ─── -->
    <g :transform="`translate(0, ${lidOffset})`">
      <path
          d="M-24 2 Q-24 -16 0 -18 Q24 -16 24 2 Z"
          fill="url(#goldGradient)"
          stroke="#8b6914"
          stroke-width="2"
      />
      <!-- diamond -->
      <path
          d="M0,-12 L3,-8 L0,-4 L-3,-8 Z"
          fill="#4da6ff"
          stroke="#0080ff"
          stroke-width="0.5"
      />
    </g>

    <!-- ─── main body ─── -->
    <rect
        x="-24" y="2" width="48" height="26" rx="3"
        fill="url(#darkGoldGradient)"
        stroke="#654321"
        stroke-width="2"
    />

    <!-- lock -->
    <rect  x="-7"  y="12" width="14" height="12" rx="3"
           fill="#2c2c2c" stroke="#1a1a1a" stroke-width="1.5"/>
    <circle cx="0"  cy="17" r="3" fill="#000"/>
    <rect   x="-1.5" y="17" width="3" height="5" fill="#000"/>

    <!-- corner rivets -->
    <g stroke="#5c4710" stroke-width="0.7" fill="#e5c55b">
      <circle cx="-20" cy="6"  r="2"/>
      <circle cx="20"  cy="6"  r="2"/>
      <circle cx="-20" cy="24" r="2"/>
      <circle cx="20"  cy="24" r="2"/>
    </g>
  </g>
</template>
