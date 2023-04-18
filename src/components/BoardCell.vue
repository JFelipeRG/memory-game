<script>
export default {
  name: "BoardCell",
  props: {
    item: String,
    rotatedCards: Number
  },
  methods: {
    handleClick() {
      if(this.$refs.card.classList.contains("rotated") || this.rotatedCards === 2) return

      this.$refs.card.classList.add("rotated")

      this.$emit("cardRotated", {
        value: this.item,
        findPair: this.findPair,
        goBack: this.reset
      })
    },
    findPair() {
      this.$refs.card.classList.add("hasPair")
    },
    reset() {
      this.$refs.card.classList = "cell"
    }
  }
}
</script>

<template>
  <div ref="card" @click="handleClick" class="cell">
    <div class="card-back"></div>
    <div>
      {{ item }}
    </div>
  </div>
</template>

<style scoped>
.cell {
  aspect-ratio: 1/1.7;
  border-radius: 7px;
  display: grid;
  place-content: center;
  font-size: 1.5rem;
  padding: 10px;
  background: var(--clr-base-txt);
  position: relative;
  transition: transform 0.6s;
  transform-style: preserve-3d;
}

.cell.rotated {
  transform: rotateY(180deg);
}

.card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  background-size: cover;
  border-radius: 7px;
  backface-visibility: hidden;
  background-color: #333;
  border: 2px solid var(--clr-base-txt);
}

.hasPair {
  background: var(--clr-primary);
}
</style>
