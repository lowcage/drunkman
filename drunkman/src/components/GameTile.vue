<script setup>
import {computed, ref, watch, defineEmits, defineProps} from 'vue'
import NormalTile from './NormalTile.vue'
import SkullTile from './SkullTile.vue'
import ChestTile from './ChestTile.vue'
import ResetTile from './ResetTile.vue'

/* ---------- props ---------- */
const props = defineProps({
  tile: {type: Object, required: true},
  size: {type: Number, required: true},
  spacing: {type: Number, default: 1},
  active: {type: Boolean, default: false}     // 👈 NEW
})

/* ---------- emit skeleton events upward ---------- */
const emit = defineEmits(['skull-landed', 'reset-triggered'])

/* ---------- SVG positioning ---------- */
const transform = computed(() => {
  const x = props.tile.x + props.size / 2 + props.spacing / 2
  const y = props.tile.y + props.size / 2 + props.spacing / 2
  return `translate(${x}, ${y})`
})

/* ---------- watch `active` to relay events ---------- */
watch(
    () => props.active,
    (isActive) => {
      if (!isActive) return
      if (props.tile.type === 'skull') emit('skull-landed', props.tile)
      if (props.tile.type === 'reset') emit('reset-triggered', props.tile)
    }
)
</script>

<template>
  <g :transform="transform" class="game-tile">
    <ResetTile
        v-if="tile.type === 'reset'"
        :tile="tile"
        :size="size"
        :active="active"
    />

    <SkullTile
        v-else-if="tile.type === 'skull'"
        :tile="tile"
        :size="size"
        :active="active"
    />

    <ChestTile
        v-else-if="tile.type === 'chest'"
        :tile="tile"
        :size="size"
        :active="active"
    />

    <NormalTile
        v-else
        :tile="tile"
        :size="size"
    />
  </g>
</template>
