<script setup lang="ts">
import { ref, computed } from 'vue'
import type { Player } from './types/Player'
import type { Board } from './types/Board'

function emptyBoard(): Board {
  return [
    ['', '', ''],
    ['', '', ''],
    ['', '', ''],
  ]
}

const player = ref<Player>('X')
const board = ref<Board>(emptyBoard())

function calculateWinner(squares: Board): '' | Player {
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
function playerIcon(player: '' | Player) {
  return player === 'X' ? 'close' : player === 'O' ? 'circle' : ''
}

// palyer 'X' plays as blue-600 color
// palyer 'O' plays as red-600 color
function playerColor(cell: '' | Player) {
  return cell === 'X' ? 'text-blue-600' : 'text-red-600'
}

</script>

<template>
  <main class="text-center flex flex-col justify-center items-center min-h-screen gap-8">
    <div class="space-y-1">
      <h1 class="text-xl font-bold uppercase">Tic Tac Toe</h1>
      <h3>
        Player
        <b :class="playerColor(player)">
          {{ player }}
        </b>
        's turn
      </h3>
    </div>

    <div class="flex flex-col items-center">
      <div v-for="(row, x) in board" :key="x" class="flex">

        <div v-for="(cell, y) in row" :key="y" class="w-20 h-20 border border-slate-500 hover:bg-slate-700 cursor-pointer flex justify-center items-center select-none" :class="playerColor(cell)" @click="makeMove(x, y)">

          <i class="material-icons-outlined" style="font-size: 2.25rem; font-weight: 900;">
            {{ playerIcon(cell) }}
          </i>

        </div>
      </div>
    </div>

    <Transition name="scale-in">
      <h2 class="text-3xl" v-if="winner">
        Player
        <span class="font-bold" :class="playerColor(winner)">
          {{ winner }}
        </span>
        wins
      </h2>
    </Transition>

    <button class="bg-slate-800 px-4 py-2 rounded-md hover:bg-slate-700" @click="resetGame">
      Reset Game
    </button>
  </main>
</template>

<style>
.scale-in-enter-active,
.scale-in-leave-active {
  transition: transform 0.5s;
}

.scale-in-enter-from,
.scale-in-leave-to {
  transform: scale(0);
}
</style>
