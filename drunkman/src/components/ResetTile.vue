<script setup>
import { ref, computed, watch } from 'vue'

/* props */
const props = defineProps({
  tile  : { type: Object, required: true },
  size  : { type: Number, required: true },
  active: { type: Boolean, default : false }
})

/* reactive rotation angles */
const outerAngle = ref(0)          // main gear (and arrow)
const busy       = ref(false)

/* inner gear counter-rotates at half speed */
const innerAngle = computed(() => -outerAngle.value * 0.5)

/* cubic-out easing */
const ease = t => 1 - Math.pow(1 - t, 3)

/* tween helper */
function tween (from, to, dur) {
  return new Promise(res => {
    const s = performance.now()
    const step = n => {
      const t = Math.min((n - s) / dur, 1)
      outerAngle.value = from + (to - from) * ease(t)
      t < 1 ? requestAnimationFrame(step) : res()
    }
    requestAnimationFrame(step)
  })
}

/* one spin + mechanical bounce */
async function spinOnce () {
  await tween(   0, 360, 600)  // full spin
  await tween( 360, 385, 120)  // overshoot
  await tween( 385, 350, 180)  // recoil back
  await tween( 350, 368, 120)  // small forward bounce
  await tween( 368, 360, 100)  // settle
}

/* master: two spins with 200 ms gap */
async function playResetAnimation () {
  if (busy.value) return
  busy.value = true

  await spinOnce()
  await new Promise(r => setTimeout(r, 200))
  await spinOnce()

  busy.value = false
}

/* trigger on :active */
watch(() => props.active, a => { if (a) playResetAnimation() })
</script>

<template>
  <g class="reset-tile">
    <!-- ───── defs: steel gradient + bevel shine ───── -->
    <defs>
      <!-- brushed-steel look for the arrow -->
      <radialGradient id="arrowSteelGrad" cx="50%" cy="50%" r="70%">
        <stop offset="0%"   stop-color="#c0c5c8"/>
        <stop offset="60%"  stop-color="#8c9297"/>
        <stop offset="100%" stop-color="#52575c"/>
      </radialGradient>

      <!-- subtle specular highlight -->
      <filter id="arrowBevel" x="-40%" y="-40%" width="180%" height="180%">
        <feGaussianBlur in="SourceAlpha" stdDeviation="1.5" result="blur"/>
        <feSpecularLighting in="blur" surfaceScale="3" specularConstant="0.6"
                            specularExponent="20" lighting-color="#ffffff" result="spec"/>
        <feComposite in="spec" in2="SourceAlpha" operator="in" result="specTrim"/>
        <feMerge>
          <feMergeNode in="SourceGraphic"/>
          <feMergeNode in="specTrim"/>
        </feMerge>
      </filter>
    </defs>

    <!-- ░░░ OUTER GEAR ░░░ -->
    <g :transform="`rotate(${outerAngle})`">
      <!-- teeth -->
      <g
          v-for="n in 12"
          :key="'tooth-'+n"
          :transform="`rotate(${(n - 1) * 30}) translate(${size / 2 - 6}, 0)`"
      >
        <rect x="-3" y="-4" width="6" height="8" fill="#666" />
      </g>

      <!-- gear body -->
      <circle
          :r="size / 2 - 6"
          fill="url(#arrowBackgroundGradient)"
          stroke="#4a4a4a"
          stroke-width="3"
      />
    </g>

    <!-- ░░░ INNER COUNTER-GEAR ░░░ -->
    <g :transform="`rotate(${innerAngle})`">
      <circle
          :r="size / 4"
          fill="#2a2a2a"
          stroke="#4a4a4a"
          stroke-width="2"
      />
      <g
          v-for="n in 8"
          :key="'inner-'+n"
          :transform="`rotate(${(n - 1) * 45}) translate(${size / 4}, 0)`"
      >
        <rect x="-2" y="-3" width="4" height="6" fill="#555" />
      </g>
    </g>

    <!-- ░░░ STEEL ARROW (rides outer gear) ░░░ -->
    <g :transform="`translate(${size / 2 - 32.5}, ${size / 2 - 32}) rotate(${outerAngle})`">
      <g transform="scale(0.35) translate(-45, -45)">
        <path
            d="M 75.702 53.014 c -2.142 7.995 -7.27 14.678 -14.439 18.816 c -7.168 4.138 -15.519 5.239 -23.514 3.095 c -16.505 -4.423 -26.335 -21.448 -21.913 -37.953 C 20.258 20.467 37.286 10.64 53.79 15.06 c 4.213 1.129 8.076 3.118 11.413 5.809 l -8.349 8.35 h 26.654 V 2.565 l -8.354 8.354 c -5.1 -4.405 -11.133 -7.61 -17.74 -9.381 C 33.451 -4.882 8.735 9.389 2.314 33.35 c -6.42 23.961 7.851 48.678 31.811 55.098 C 38.001 89.486 41.934 90 45.842 90 c 7.795 0 15.488 -2.044 22.42 -6.046 c 10.407 -6.008 17.851 -15.709 20.962 -27.317 L 75.702 53.014 z"
            fill="url(#arrowSteelGrad)"
            stroke="#222"
            stroke-width="2"
            filter="url(#arrowBevel)"
        />
      </g>
    </g>
  </g>
</template>

