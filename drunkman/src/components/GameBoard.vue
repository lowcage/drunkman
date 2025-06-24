<script setup>
import { computed, ref } from 'vue'
import SVGDefinitions from './SVGDefinitions.vue'
import GameTile from './GameTile.vue'

const TILE_SIZE = 65
const TILE_SPACING = 1
const padding = 1

const tiles = [
  {"id": 0, "x": 0, "y": 0, "type": "normal"},
  {"id": 1, "x": 60, "y": 0, "type": "skull"},
  {"id": 2, "x": 120, "y": 0, "type": "normal"},
  {"id": 3, "x": 180, "y": 0, "type": "chest"},
  {"id": 4, "x": 240, "y": 0, "type": "normal"},
  {"id": 5, "x": 300, "y": 0, "type": "normal"},
  {"id": 6, "x": 360, "y": 0, "type": "normal"},
  {"id": 7, "x": 420, "y": 0, "type": "chest"},
  {"id": 8, "x": 480, "y": 0, "type": "normal"},
  {"id": 9, "x": 0, "y": 60, "type": "normal"},
  {"id": 17, "x": 480, "y": 60, "type": "normal"},
  {"id": 26, "x": 480, "y": 120, "type": "chest"},
  {"id": 35, "x": 480, "y": 180, "type": "normal"},
  {"id": 34, "x": 420, "y": 180, "type": "normal"},
  {"id": 18, "x": 0, "y": 120, "type": "chest"},
  {"id": 15, "x": 360, "y": 60, "type": "normal"},
  {"id": 24, "x": 360, "y": 120, "type": "normal"},
  {"id": 33, "x": 360, "y": 180, "type": "normal"},
  {"id": 42, "x": 360, "y": 240, "type": "skull"},
  {"id": 51, "x": 360, "y": 300, "type": "normal"},
  {"id": 27, "x": 0, "y": 180, "type": "normal"},
  {"id": 69, "x": 360, "y": 420, "type": "normal"},
  {"id": 78, "x": 360, "y": 480, "type": "chest"},
  {"id": 77, "x": 300, "y": 480, "type": "normal"},
  {"id": 76, "x": 240, "y": 480, "type": "normal"},
  {"id": 75, "x": 180, "y": 480, "type": "chest"},
  {"id": 66, "x": 180, "y": 420, "type": "normal"},
  {"id": 36, "x": 0, "y": 240, "type": "normal"},
  {"id": 45, "x": 0, "y": 300, "type": "normal"},
  {"id": 54, "x": 0, "y": 360, "type": "reset"},
  {"id": 55, "x": 60, "y": 360, "type": "normal"},
  {"id": 56, "x": 120, "y": 360, "type": "chest"},
  {"id": 57, "x": 180, "y": 360, "type": "skull"},
  {"id": 58, "x": 240, "y": 360, "type": "normal"},
  {"id": 59, "x": 300, "y": 360, "type": "normal"},
  {"id": 60, "x": 360, "y": 360, "type": "normal"}
]

const viewBox = computed(() => {
  const maxX = Math.max(...tiles.map(t => t.x)) + TILE_SIZE + TILE_SPACING
  const maxY = Math.max(...tiles.map(t => t.y)) + TILE_SIZE + TILE_SPACING
  return `-${padding} -${padding} ${maxX + padding * 2} ${maxY + padding * 2}`
})

const resetTileRef = ref(null)

function setResetTileRef(el, tile) {
  if (tile.type === 'reset' && el) {
    resetTileRef.value = el
  }
}

function playResetAnimation() {
  resetTileRef.value?.playResetAnimation?.()
}

</script>

<template>
  <svg :viewBox="viewBox" preserveAspectRatio="xMidYMid meet" class="w-full max-w-[80vmin] h-auto game-board">
    <SVGDefinitions/>
    <GameTile
        v-for="tile in tiles"
        :key="tile.id"
        :tile="tile"
        :size="TILE_SIZE"
        :spacing="TILE_SPACING"
        :ref="el => setResetTileRef(el, tile)"
    />

  </svg>

  <div class="mt-4 text-center">
    <button @click="playResetAnimation" class="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600">
      🔄 Spin Reset Arrow
    </button>
  </div>
</template>

<style scoped>
.game-board {
  background: transparent;
}
</style>
