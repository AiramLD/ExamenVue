<script setup>

import { ref } from 'vue';

const budget = ref(0);

const checkBudget = () => {
  if (budget.value <= 0) {
    budget.value = 0;
  }
};



</script>
<template>
  <div>
    <h1>Destinos turisticos </h1>
    <label for="budget">Presupuesto:</label>
    <input type="number" id="budget" v-model="budget" @input="checkBudget">
    <div v-if="budget <= 0">
      <p>Se debe insertar un presupuesto mayor que 0</p>
    </div>
    <div v-else>
      <h2>Su presupuesto es de {{ budget }}€</h2>
    </div>
  </div>
</template>


<style scoped>


</style>
