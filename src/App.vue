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

</script>

<template>

</template>

<style>
</style>
