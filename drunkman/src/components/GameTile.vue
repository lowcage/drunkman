<script setup>
import { computed, ref, onMounted, watch, defineExpose } from 'vue'
import NormalTile from './NormalTile.vue'
import SkullTile from './SkullTile.vue'
import ChestTile from './ChestTile.vue'
import ResetTile from './ResetTile.vue'

const props = defineProps({
  tile: { type: Object, required: true },
  size: { type: Number, required: true },
  spacing: { type: Number, default: 1 }
})

// Map tile types to components
const tileComponents = {
  normal: NormalTile,
  skull: SkullTile,
  chest: ChestTile,
  reset: ResetTile
}

const tileComponent = computed(() => tileComponents[props.tile.type] || NormalTile)

const transform = computed(() => {
  const x = props.tile.x + props.size / 2 + props.spacing / 2
  const y = props.tile.y + props.size / 2 + props.spacing / 2
  return `translate(${x}, ${y})`
})

const resetTileRef = ref(null)

defineExpose({
  playResetAnimation: () => {
    resetTileRef.value?.playResetAnimation?.()
  }
})
</script>

<template>
  <g :transform="transform" class="game-tile">
    <component
        :is="tileComponent"
        :tile="tile"
        :size="size"
        v-if="tile.type === 'reset'"
        ref="resetTileRef"
    />
    <component
        :is="tileComponent"
        :tile="tile"
        :size="size"
        v-else
    />
  </g>
</template>
