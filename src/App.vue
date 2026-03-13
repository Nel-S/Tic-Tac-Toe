<script setup>
import BoardWrapper from "./components/BoardWrapper.vue";
</script>

<template>
  <header>
    <h1>Tic-Tac-Toe</h1>
    <!-- <h2>By Nel. S.</h2> -->
  </header>

  <main>
      <BoardWrapper />
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
  font-weight: bold;
  font-size: 2.5rem;
  margin-top: 1%;
  margin-bottom: 1%;
}
h2 {
  text-align: center;
}
</style>
