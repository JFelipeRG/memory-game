<script>
import BoardCell from "./components/BoardCell.vue"
import ModalWinner from "./components/ModalWinner.vue"

import { ITEMS } from "./constants/items"

export default {
  components: {
    BoardCell, ModalWinner
  },
  data() {
    return {
      ITEMS,
      randomly: [],
      selectedPairs: [],
      numberFindPairs: 0,
      winner: false,
      start: true,
      timmerInterval: "",
      timmer: 0,
      record: 0,
      newRecord: false
    }
  },
  beforeMount() {
    this.randomizeBoard()
    this.record = parseInt(localStorage.record) || 0
  },
  watch: {
    randomly: function () {
      if (this.$refs.cells) {
        for (const cell of this.$refs.cells) {
          cell.reset()
        }
      }
    }
  },
  methods: {
    randomizeBoard() {
      const randomIndex = []

      for (let i = 0; i < ITEMS.length * 2; i++) {
        const position = Math.floor(Math.random() * ITEMS.length)
        const isHavePairs = randomIndex.filter(elem => elem === position).length === 2
        if (isHavePairs) {
          i--
        } else {
          randomIndex.push(position)
        }
      }

      this.randomly = randomIndex
      setTimeout(() => this.winner = false, 100)
      this.numberFindPairs = 0
      clearInterval(this.timmerInterval)
      this.timmer = 0
      this.start = true
      this.newRecord = false

    },
    cardRotated(cardItem) {
      if (this.start) {
        this.start = false
        this.startTimmer()
      }

      this.selectedPairs.push(cardItem)

      if (this.selectedPairs.length === 2) setTimeout(() => this.checkPairs(), 550)
    },
    checkPairs() {
      if (this.selectedPairs[0].value === this.selectedPairs[1].value) {
        for (const card of this.selectedPairs) {
          card.findPair()
        }

        this.numberFindPairs++
      } else {
        for (const card of this.selectedPairs) {
          card.goBack()
        }
      }

      this.selectedPairs = []
      this.checkWinner()
    },
    checkWinner() {
      if (!(this.numberFindPairs === ITEMS.length)) return

      this.newRecord = (this.record > this.timmer) || this.record === 0

      this.winner = true
      clearInterval(this.timmerInterval)
      if(this.newRecord) localStorage.setItem("record", this.timmer)

      this.record = this.newRecord ? this.timmer : this.record

      this.start = true
    },
    resetCells() {
      if (this.$refs.cells) {
        for (const cell of this.$refs.cells) {
          cell.reset()
        }
      }

      setTimeout(this.randomizeBoard, 600)
    },
    startTimmer() {
      this.timmerInterval = setInterval(() => {
        this.timmer += 1000
      }, 1000)
    },
    parseTimmer(timmer) {
      let seconds = timmer / 1000;

      const minutes = parseInt(seconds / 60)
      seconds = seconds % 60;

      return String(minutes).padStart(2, 0) + ":" + String(seconds).padStart(2, 0)
    }
  }
}
</script>

<template>
  <ModalWinner v-if="winner" :reset="randomizeBoard" :timmer="parseTimmer(timmer)" :record="parseTimmer(record)" :newRecord="this.newRecord"></ModalWinner>
  <h1>Memory Game</h1>
  <div class="timer-container">
    <span>👑 {{ parseTimmer(record) }}</span>
    <span>{{ parseTimmer(timmer) }} ⌛</span>
  </div>
  <div class="board">
    <BoardCell ref="cells" @cardRotated="cardRotated" v-for="(value, index) in randomly" :key="index" :item="ITEMS[value]"
      :rotatedCards="selectedPairs.length">
    </BoardCell>
  </div>
  <button @click="resetCells" class="reset-button">Reset Game</button>
</template>

<style>
h1 {
  text-align: center;
}

.timer-container {
  display: flex;
  justify-content: space-between;
}

.board {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 5px;
}

.reset-button {
  width: fit-content;
  padding: 10px;
  border-radius: 7px;
  outline: none;
  margin: auto;
  font-size: 1.1rem;
  font-weight: 700;

  border: none;
  background-color: var(--clr-secondary);
}
</style>
