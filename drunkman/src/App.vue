<script setup>
import { ref } from 'vue'
import PlayerInput from './components/PlayerInput.vue'
import GameBoard from './components/GameBoard.vue'
import SlotMachine from './components/SlotMachine.vue'
import PathEditor from "./components/PathEditor.vue";

const started = ref(false)
const players = ref([])

function startGame(confirmedPlayers, assignedColors) {
  players.value = confirmedPlayers.map((name, index) => ({
    name,
    color: assignedColors[index]
  }))
  started.value = true
}
</script>
<template>
  <div class="min-h-screen bg-gradient-to-b from-amber-800 via-amber-700 to-yellow-900 text-stone-100 py-16 px-4 relative overflow-hidden">
    <!-- Buborék animációk -->
    <span
        v-for="n in 30"
        :key="n"
        class="bubble"
        :style="{
        left: `${Math.random() * 100}%`,
        width: `${Math.random() * 20 + 10}px`,
        animationDelay: `${Math.random() * 12}s`,
      }"
    />

    <!-- Kezdőképernyő -->
    <template v-if="!started">
      <header class="text-center mb-12 relative z-10">
        <h1 class="text-5xl md:text-6xl font-drunkman flex justify-center items-center gap-3 text-white drop-shadow-xl">
          <span class="emoji-glow text-6xl">🍻</span>
          <span class="text-shimmer">DrunkMan</span>
          <span class="emoji-glow text-6xl">🍻</span>
        </h1>
      </header>
      <PlayerInput @start="startGame" />
    </template>

    <!-- Játék képernyő -->
    <template v-else>
      <!-- Oldalsáv fixen balra -->
      <aside class="fixed top-0 left-0 h-full w-80 bg-black bg-opacity-50 p-6 pt-16 shadow-2xl z-20 overflow-y-auto flex flex-col justify-between overflow-hidden">
        <!-- Fejléc és játékosok -->
        <div>
          <h1 class="text-3xl font-drunkman flex items-center gap-2 mb-6">
            <span class="emoji-glow text-4xl">🍻</span>
            <span class="text-shimmer">DrunkMan</span>
            <span class="emoji-glow text-4xl">🍻</span>
          </h1>

          <div class="mb-8">
            <h2 class="text-xl font-bold mb-4">Játékosok</h2>
            <ul class="space-y-4">
              <li
                  v-for="player in players"
                  :key="player.name"
                  class="flex items-center gap-2 text-lg"
              >
          <span
              :style="{ backgroundColor: player.color }"
              class="w-5 h-5 rounded-full inline-block"
          ></span>
                <span>{{ player.name }}</span>
              </li>
            </ul>
          </div>
        </div>

        <!-- Szerencsekerék alul -->
        <div class="mt-8">
          <div class="flex justify-center">
            <SlotMachine />
          </div>
        </div>
      </aside>


      <!-- Játéktábla: középre igazítva a viewportban -->
      <main class="relative z-10 pl-[20rem] flex items-center justify-center  overflow-hidden">
        <GameBoard />
      </main>
    </template>
  </div>
</template>