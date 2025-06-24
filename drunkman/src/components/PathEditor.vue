<script setup>
import { reactive, computed } from 'vue'

const gridSize = 9
const activeTileIndexes = [
  0, 1, 2, 3, 4, 5, 6, 7, 8,
  9, 17, 26, 35, 34,
  18, 15, 24, 33, 42, 51,
  27, 69, 78, 77, 76, 75, 66,
  36,
  45,
  54, 55, 56, 57, 58, 59, 60,
]

const tileTypes = ['normal', 'chest', 'skull']

const tiles = reactive(
    activeTileIndexes.map((index) => {
      const x = index % gridSize
      const y = Math.floor(index / gridSize)
      return {
        id: index,
        x: x * 60,
        y: y * 60,
        type: 'normal'
      }
    })
)

function toggleType(tile) {
  const currentIndex = tileTypes.indexOf(tile.type)
  tile.type = tileTypes[(currentIndex + 1) % tileTypes.length]
}

const exportJson = computed(() => {
  return JSON.stringify(tiles, null, 2)
})
</script>

<template>
  <div class="flex gap-8">
    <!-- SVG pálya -->
    <svg :width="gridSize * 60" :height="gridSize * 60" class="rounded-xl bg-amber-900">
      <g v-for="tile in tiles" :key="tile.id" @click="toggleType(tile)" :transform="`translate(${tile.x + 10}, ${tile.y + 10})`" class="cursor-pointer">
        <rect
            width="40"
            height="40"
            rx="8"
            :fill="tile.type === 'normal' ? '#e3b653' : tile.type === 'chest' ? '#8f5' : '#f55'"
            stroke="white"
            stroke-width="2"
        />
        <text x="20" y="25" text-anchor="middle" fill="black" font-size="14" font-weight="bold">
          {{ tile.type === 'normal' ? tile.id : tile.type === 'chest' ? '🪙' : '💥' }}
        </text>
      </g>
    </svg>

    <!-- JSON kimenet -->
    <div class="bg-white/10 p-4 rounded-xl w-[400px] overflow-auto text-sm">
      <h2 class="font-bold mb-2 text-white">tiles[] JSON</h2>
      <pre class="text-white whitespace-pre-wrap">{{ exportJson }}</pre>
    </div>
  </div>
</template>

<style scoped>
svg {
  background: radial-gradient(circle at center, #f0c04022, #00000033);
}
</style>