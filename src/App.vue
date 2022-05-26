<script setup lang="ts">
import { ref, computed } from 'vue'
import type { Player } from './types/Player'
import type { Board } from './types/Board'

const player = ref<Player>('X')
const board = ref<Board>([
  ['', '', ''],
  ['', '', ''],
  ['', '', ''],
])

function calculateWinner(squares): null | Player {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ]

  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i]

    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a]
    }
  }

  return null
}

const winner = computed(() => {
  return calculateWinner(board.value.flat())
})

function switchPlayers(player: Player): Player {
  let _player = ref<Player>(player);

  if (player === 'X') {
    _player.value = 'O'
  } else {
    _player.value = 'X'
  }

  return _player.value
}

function makeMove(x: number, y: number) {
  // no move when game is over
  if (winner.value) return

  // no move when already occupied
  if (board.value[x][y] !== '') return

  // place player on board
  board.value[x][y] = player.value

  // switch turns
  player.value = switchPlayers(player.value)
}

function emptyBoard(): Board {
  return [
    ['', '', ''],
    ['', '', ''],
    ['', '', ''],
  ]
}

function resetGame() {
  board.value = emptyBoard();
}


function playerIcon(cell: '' | Player) {
  return cell === 'X' ? 'close' : cell === 'O' ? 'circle' : ''
}

</script>

<template>
  <main class="text-center">
    <h1 class="text-2xl font-bold uppercase">Tic Tac Toe</h1>
    <h3>Player <b>{{ player }}</b>'s turn</h3>

    <div class="flex flex-col items-center">
      <div v-for="(row, x) in board" :key="x" class="flex">
        <div v-for="(cell, y) in row" :key="y" class="w-20 h-20 border border-slate-500 hover:bg-slate-700 cursor-pointer flex justify-center items-center text-4xl font-bold material-icons-outlined" :class="cell === 'X' ? 'text-blue-600' : 'text-red-600'" @click="makeMove(x, y)">
          {{ playerIcon(cell) }}
        </div>
      </div>
    </div>

    <h2 class="text-3xl" v-if="winner">Player <span class="font-bold">{{ winner }}</span> wins</h2>

    <button class="bg-slate-800 px-4 py-2 rounded-lg hover:bg-slate-700" @click="resetGame">Reset Game</button>
  </main>
</template>

<style>
</style>
