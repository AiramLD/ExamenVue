<template>
  <div>
        <label :for="name" v-if="name !== 'days'"> Numero de personas: </label>
        <label :for="name" v-if="name === 'days'"> Numero de dias: </label>
        <input type="number" :name="name" :min="min" :max="max" :value ="value" @input="$emit ('update-input', $event)">
        <input type="range" :name="name" :min="min" :max="max" :value ="value" @input="$emit ('update-input', $event)">
    </div>
</template>
<script setup>
defineProps({
  name : String,
  value : Number,
  min : Number,
  max : Number
})
</script>
