<script>
  export default {
    name: "ModalWinner",
    props: {
      reset: Function
    }
  }
</script>

<template>
  <div class="modal">
    <div>
      <h1>Has ganado!</h1>
      <button class="reset-button" @click="reset">Reset Game</button>
    </div>
  </div>
</template>

<style>
.modal {
  position: absolute;
  width: 100%;
  height: 100%;
  backdrop-filter: blur(2px);
  display: grid;
  place-content: center;
  z-index: 1;
}

.modal div {
  width: 280px;
  aspect-ratio: 1/1;
  background: var(--clr-base-bg);
  color: var(--clr-base-txt);
  display: grid;
  place-content: center;
  gap: 20px;
  border: 3px solid var(--clr-base-txt);
  border-radius: 10px;
}
</style>
