<template>
  <div>
    <label for="budget">Indica tu presupuesto </label>
    <input type="number" name="budget" :value="budget" :min="0" @input="$emit('update-budget', $event)" id="">

    <p v-if="emptyBudget"> Se debe insertar un presupuesto mayor que 0.</p>
    <h1 v-else> Su Presupuesto es: <b :class ="alertBudget">{{ budget }}</b></h1>
  </div>
</template>
<script setup>

defineProps({
  budget: Number,
  emptyBudget: Boolean,
  alertBudget: String
})

</script>
<style scoped>

.expensive {
  color: red;
}

.cheap {
  color: green;
}

.moderate {
  color: orange;
}
</style>

