<script setup>
import { ref } from 'vue';

const numPeople = ref(1);

const checkNumPeople = () => {
  if (numPeople.value <= 0) {
    numPeople.value = 1;
  } else if (numPeople.value > 30) {
    numPeople.value = 30;
  }
};


</script>

<template>
  <div>
    <label for="numPeople">Número de personas:</label>
    <input type="number" id="numPeople" v-model.number="numPeople" min="1" max="30" @input="checkNumPeople">
    <input type="range" v-model.number="numPeople" min="1" max="30">
  </div>

</template>


<style scoped>

</style>