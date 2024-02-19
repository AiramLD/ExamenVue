<template>
  <div>
    <h2>Destinos Disponibles</h2>
    <ul>
      <li v-for="destination in destinationsList" :key="destination.id" @click="selectDestination(destination)" :class="getDestinationClass(destination.cost)">
        {{ destination.name }}
      </li>
    </ul>
    <p v-if="selectedDestination">Seleccionado: {{ selectedDestination.name }}</p>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  props: ['selectedDestination'],
  setup() {
    const destinationsList = ref([]);

    const getDestinationClass = (cost) => {
      if (cost === 'expensive') {
        return 'expensive';
      } else if (cost === 'moderate') {
        return 'moderate';
      } else {
        return 'cheap';
      }
    };

    const selectDestination = (destination) => {
      selectedDestination.value = destination;
      console.log(selectedDestination.value);
    };

    return { destinationsList, getDestinationClass, selectDestination };
  }
};
</script>

<style scoped>
.expensive {
  color: red;
}

.moderate {
  color: orange;
}

.cheap {
  color: green;
}
</style>
