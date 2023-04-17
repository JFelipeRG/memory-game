<script>
import BoardCell from "./components/BoardCell.vue"
import ModalWinner from "./components/ModalWinner.vue"

const ITEMS = [
  "🍍",
  "🎮",
  "💥",
  "🍕",
  "🍙",
  "🧠"
]

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
      winner: false
    }
  },
  beforeMount() {
    this.randomizeBoard()
  },
  watch: {
    randomly: function () {
      if(this.$refs.cells) {
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
      this.winner = false
      this.numberFindPairs = 0
    },
    cardRotated(cardItem) {
      this.selectedPairs.push(cardItem)

      if (this.selectedPairs.length === 2) setTimeout(() => this.checkPairs(),550)
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
      if (this.numberFindPairs === ITEMS.length) this.winner = true
    }
  }
}
</script>

<template>
  <ModalWinner v-if="winner" :reset="randomizeBoard"></ModalWinner>
  <h1>Memory Game</h1>
  <div class="board">
    <BoardCell ref="cells" @cardRotated="cardRotated" v-for="(value, index) in randomly" :key="index" :item="ITEMS[value]" :rotatedCards="selectedPairs.length">
    </BoardCell>
  </div>
  <button @click="randomizeBoard" class="reset-button">Reset Game</button>
</template>

<style>
h1 {
  text-align: center;
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
