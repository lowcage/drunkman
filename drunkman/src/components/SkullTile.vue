<script setup>
import { ref, computed, watch } from 'vue'

/* ─── props ─── */
const props = defineProps({
  tile  : { type: Object, required: true },
  size  : { type: Number, required: true },
  active: { type: Boolean, default: false }
})

/* ─── geometry ─── */
const lineLength = computed(() => (props.size - 16) * Math.SQRT2)

/* ─── reactive state ─── */
const dashOffset = ref(0)      // 0  → slash fully drawn
const glow       = ref(0)      // 0-1 pulse strength
const busy       = ref(false)

/* ─── timing constants ─── */
const WAIT          = 1000   // ms  pause before each cross-out
const CROSS         = 1200   // ms  duration of each stroke
const PULSE_PERIOD  = 500    // ms  2 Hz red pulse

/* ─── helpers ─── */
function acceleratingSlash () {
  /* ease-in cubic (t³): slow start → steady finish */
  return new Promise(done => {
    dashOffset.value = lineLength.value
    const start = performance.now()

    const step = now => {
      const raw = Math.min((now - start) / CROSS, 1)
      const t   = raw * raw * raw                     // ease-in cubic
      dashOffset.value = lineLength.value * (1 - t)
      raw < 1 ? requestAnimationFrame(step) : done()
    }
    requestAnimationFrame(step)
  })
}

/* continuous 2 Hz pulse while seq runs */
let pulsing    = false
let pulseStart = 0
function pulseLoop (now) {
  if (!pulsing) { glow.value = 0; return }
  const phase = ((now - pulseStart) / PULSE_PERIOD) * 2 * Math.PI
  glow.value  = 0.5 + 0.5 * Math.sin(phase)          // vivid 0 → 1
  requestAnimationFrame(pulseLoop)
}

/* ─── master sequence ─── */
async function runSequence () {
  if (busy.value) return
  busy.value  = true

  /* start pulse */
  pulsing    = true
  pulseStart = performance.now()
  requestAnimationFrame(pulseLoop)

  /* 1-second warning pulse, then first cross-out */
  await new Promise(r => setTimeout(r, WAIT))
  await acceleratingSlash()

  /* pause & second cross-out */
  await new Promise(r => setTimeout(r, WAIT))
  await acceleratingSlash()

  /* fade pulse out (400 ms) */
  const start = performance.now()
  const FADE  = 400
  const g0    = glow.value
  const fade  = now => {
    const t = Math.min((now - start) / FADE, 1)
    glow.value = g0 * (1 - t)
    t < 1 ? requestAnimationFrame(fade) : (pulsing = false)
  }
  requestAnimationFrame(fade)

  busy.value = false
}

/* trigger whenever parent flips :active="true" */
watch(() => props.active, a => { if (a) runSequence() })
</script>


<template>
  <g class="skull-tile no-alcohol-prohibition" filter="url(#shadow3d)">
    <!-- ——— defs for red glow ——— -->
    <defs>
      <filter id="skullPulseGlow" x="-60%" y="-60%" width="220%" height="220%">
        <feDropShadow dx="0" dy="0" stdDeviation="3" flood-color="#ff0000"/>
      </filter>
    </defs>

    <!-- 🔥 pulsing outline -->
    <g :opacity="glow" filter="url(#skullPulseGlow)">
      <circle
          :r="size / 2 - 4"
          fill="none" stroke="#ff0000" stroke-width="6"
      />
      <line
          :x1="`${-(size / 2 - 8) * 0.707}`"
          :y1="`${-(size / 2 - 8) * 0.707}`"
          :x2="`${ (size / 2 - 8) * 0.707}`"
          :y2="`${ (size / 2 - 8) * 0.707}`"
          stroke="#ff0000" stroke-width="6" stroke-linecap="round"
      />
    </g>

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

    <!-- Animated red slash -->
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
