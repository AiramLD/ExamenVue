<script setup>
import { ref } from 'vue';

const numDays = ref(1);

const checknumDays = () => {
  if (numDays.value <= 0) {
    numDays.value = 1;
  } else if (numDays.value > 10) {
    numDays.value = 10;
  }
};


</script>

<template>
  <div>
    <label for="numDays">Número de Dias:</label>
    <input type="number" id="numDays" v-model.number="numDays" min="1" max="10" @input="checknumDays">
    <input type="range" v-model.number="numDays" min="1" max="10">
  </div>

</template>


<style scoped>

</style>