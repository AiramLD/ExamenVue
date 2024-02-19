<script setup>
import { ref } from 'vue';

const numPeople = ref(1);

const checkNumPeople = () => {
  if (numPeople.value <= 0) {
    numPeople.value = 1;
  } else if (numPeople.value > 10) {
    numPeople.value = 10;
  }
};


</script>

<template>
  <div>
    <label for="numPeople">Número de personas:</label>
    <input type="number" id="numPeople" v-model.number="numPeople" min="1" max="10" @input="checkNumPeople">
    <input type="range" v-model.number="numPeople" min="1" max="10">
  </div>

</template>


<style scoped>

</style>