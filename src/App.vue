<script setup lang="ts">
import { ref, computed } from 'vue'
import type { Player } from './types/Player'
import type { Board } from './types/Board'
import type { Cell } from './types/Cell'

function emptyBoard(): Board {
  return [
    ['', '', ''],
    ['', '', ''],
    ['', '', ''],
  ]
}

const player = ref<Player>('X')
const board = ref<Board>(emptyBoard())

function calculateWinner(squares: Board): Cell {
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

  const _squares = squares.flat()

  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i]

    if (_squares[a] && _squares[a] === _squares[b] && _squares[a] === _squares[c]) {
      return _squares[a]
    }
  }

  return ''
}

const winner = computed(() => {
  return calculateWinner(board.value)
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

function resetGame() {
  board.value = emptyBoard();
}

// 'close' in material fonts mean 'X'
// 'circle' means 'O'
function playerIcon(player: Cell) {
  let icon = '';

  switch (player) {
    case 'X':
      icon = 'close'
      break

    case 'O':
      icon = 'circle'
      break

    default:
      break
  }
  return icon
}

// palyer 'X' plays as blue-600 color
// palyer 'O' plays as red-600 color
function playerColor(cell: Cell, bg: Boolean = false) {
  if (bg) {
    return cell === 'X' ? 'bg-blue-600' : 'bg-red-600';
  }
  return cell === 'X' ? 'text-blue-600' : 'text-red-600'
}

</script>

<template>
  <main class="text-center flex flex-col justify-center items-center min-h-screen gap-8">
    <div class="space-y-1">
      <h1 class="text-xl font-bold uppercase text-slate-400">Tic Tac Toe</h1>
      <h3>
        Player <b :class="playerColor(player)">
          {{ player }}</b>'s turn
      </h3>
    </div>

    <div class="flex flex-col items-center outline outline-1 outline-slate-500">
      <div v-for="(row, x) in board" :key="x" class="flex">

        <div v-for="(cell, y) in row" :key="y" class="w-20 h-20 md:w-24 md:h-24 border border-slate-500 hover:bg-slate-700 cursor-pointer flex justify-center items-center select-none transition hover:scale-90 active:scale-110" :class="[playerColor(cell), { 'hover:bg-red-600/10': cell === 'O' }, { 'hover:bg-blue-600/10': cell === 'X' }]" @click="makeMove(x, y)">

          <svg v-if="playerIcon(cell) === 'close'" class="md:w-16 md:h-16" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img" width="48" height="48" preserveAspectRatio="xMidYMid meet" viewBox="0 0 24 24">
            <path fill="currentColor" d="M19 6.41L17.59 5L12 10.59L6.41 5L5 6.41L10.59 12L5 17.59L6.41 19L12 13.41L17.59 19L19 17.59L13.41 12L19 6.41Z"></path>
          </svg>

          <svg v-if="playerIcon(cell) === 'circle'" class="md:w-16 md:h-16" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img" width="48" height="48" preserveAspectRatio="xMidYMid meet" viewBox="0 0 24 24">
            <path fill="currentColor" d="M12 20a8 8 0 0 1-8-8a8 8 0 0 1 8-8a8 8 0 0 1 8 8a8 8 0 0 1-8 8m0-18A10 10 0 0 0 2 12a10 10 0 0 0 10 10a10 10 0 0 0 10-10A10 10 0 0 0 12 2Z"></path>
          </svg>

        </div>
      </div>
    </div>

    <button class="px-4 py-2 rounded-md hover:bg-slate-700 transition duration-300" :class="winner ? playerColor(winner, true) : 'bg-slate-800'" @click="resetGame">
      Reset Game
    </button>

    <div class="relative w-full">
      <Transition name="scale-in">
        <h2 class="text-3xl absolute w-full" v-if="winner">
          Player
          <span class="font-bold" :class="playerColor(winner)">
            {{ winner }}
          </span>
          wins
        </h2>
      </Transition>
    </div>
  </main>
</template>

<style>
.scale-in-enter-active,
.scale-in-leave-active {
  transition: transform 0.3s;
}

.scale-in-enter-from,
.scale-in-leave-to {
  transform: scale(0);
}
</style>
