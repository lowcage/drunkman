<script setup>
import { ref, computed, nextTick } from 'vue'

const players = ref([''])
const colors = ref([])
const emit = defineEmits(['start'])


const COLOR_POOL = [
  '#e6194B', '#3cb44b',
  '#ffe119', '#4363d8', '#f58231',
  '#911eb4', '#46f0f0',
  '#f032e6', '#bcf60c', '#fabebe',
]

function getRandomUnusedColor() {
  const used = new Set(colors.value)
  const available = COLOR_POOL.filter(c => !used.has(c))
  const randomIndex = Math.floor(Math.random() * available.length)
  return available[randomIndex] || '#888'
}

function addPlayer() {
  if (canAddPlayer.value) {
    players.value.splice(players.value.length - 1, 0, players.value.at(-1))
    colors.value.push(getRandomUnusedColor())
    players.value[players.value.length - 1] = '' // új üres input mező
  }
}


function removePlayer(index) {
  players.value.splice(index, 1)
  colors.value.splice(index, 1)
}

const canAddPlayer = computed(() =>
  confirmedPlayers.value.length < 10 &&
  players.value.at(-1).trim().length > 0
)


const canStartGame = computed(() =>
  confirmedPlayers.value.filter(name => name.trim().length > 0).length >= 2
)

const maxPlayersReached = computed(() =>
  confirmedPlayers.value.length === 10
)

const confirmedPlayers = computed(() =>
  players.value.slice(0, players.value.length - 1).filter(name => name.trim().length > 0)
)

const visiblePlayers = computed(() => {
  if (confirmedPlayers.value.length === 10) {
    return players.value.slice(0, 10) // csak a 10 végleges
  } else {
    return players.value // véglegesek + aktív input
  }
})


</script>

<template>
  <main class="max-w-xl mx-auto bg-white/20 backdrop-blur-md p-6 rounded-2xl shadow-xl relative z-10">
    <p class="text-center text-lg mb-4 font-semibold">Add meg a játékosok neveit:</p>

    <transition-group name="fade" tag="div" class="space-y-4">
      <div
        v-for="(name, index) in visiblePlayers"
        :key="index"
        class="flex items-center gap-2"
      >
        <!-- Színkör -->
        <div
          class="w-5 h-5 min-w-[20px] min-h-[20px] rounded-full border border-white/40 shadow"
          :style="{ backgroundColor: colors[index] || 'transparent' }"
        ></div>

        <!-- Input mező -->
        <input
          v-model="players[index]"
          type="text"
          :maxlength="8"
          :disabled="index < players.length - 1 || maxPlayersReached"
          @keyup.enter="index === players.length - 1 && addPlayer()"
          class="w-full px-4 py-2 rounded-xl transition placeholder-gray-500 focus:outline-none focus:ring-2 focus:ring-yellow-300"
          :class="[
    'placeholder:text-sm',
    (index === players.length - 1 && !maxPlayersReached)
      ? 'bg-white/70 text-gray-900'
      : 'bg-yellow-100/60 text-gray-800 opacity-70 cursor-not-allowed'
  ]"
          :placeholder="`Játékos ${index + 1}`"
        />




        <!-- Törlés gomb (csak hozzáadott játékosoknál) -->
        <button
          v-if="players.length > 1 && index < players.length - 1"
          @click="removePlayer(index)"
          class="text-red-500 hover:text-red-700 transition text-xl"
        >
          ✖
        </button>
      </div>
    </transition-group>

    <div class="mt-6 grid grid-cols-1 sm:grid-cols-2 gap-4 items-start">
      <!-- Bal oldal: Gombok -->
      <div class="flex flex-col gap-3">
        <button
          @click="addPlayer"
          :disabled="!canAddPlayer"
          class="w-full px-3 py-2 bg-yellow-400 text-gray-900 text-sm font-semibold rounded-xl hover:bg-yellow-300 transition disabled:opacity-40 disabled:cursor-not-allowed"
        >
          ➕ Játékos hozzáadása
        </button>

        <button
          @click="emit('start', confirmedPlayers, colors)"
          :disabled="!canStartGame"
          class="w-full px-3 py-2 bg-green-500 text-white text-sm font-semibold rounded-xl hover:bg-green-400 transition disabled:opacity-30 disabled:cursor-not-allowed"
        >
          ▶️ Start Game
        </button>
      </div>

      <!-- Jobb oldal: Státusz -->
      <div class="bg-yellow-100/20 p-4 rounded-xl border border-yellow-200 text-white text-sm text-center">
        <p class="font-semibold">🍻 Party: {{ confirmedPlayers.length }}/10</p>
        <p v-if="confirmedPlayers.length < 2" class="text-red-300 mt-2">Legalább 2 játékos kell!</p>
        <p v-else class="text-green-300 mt-2">Készen állsz a játékra? 🎲</p>
      </div>
    </div>

  </main>
</template>
