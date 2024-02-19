<script setup>
import Presupuesto from './components/Presupuesto.vue';
import Personas from './components/Personas.vue';
import Dias from './components/Dias.vue';
import DestinationList from './components/destinationList.vue';
import MapDestination from './components/MapDestination.vue';
import { ref, defineProps, computed, onMounted } from 'vue';

const selectedAccommodation = ref(null);
const accommodationTypes = ["Albergue", "Hostal", "Bed and Breakfast", "Hotel 3*", "Hotel Superior"];
const destinationsList = ref([]);
const destinyStyle = ref(null);
const selectedDestination = ref(null);


const costsDestinations = {
  cheap: 0.7,
  moderate: 1,
  expensive: 1.3
}

const costsAccommodations = {
  "Albergue": 25,
  "Hostal": 40,
  "Bed and Breakfast": 65,
  "Hotel 3*": 100,
  "Hotel Superior": 200,
}

const dominio = "http://localhost:3000";

function showAcommodationType(type) {
  return accommodationTypes.includes(type);
}

function getAccommodationsType(type) {
  return accommodationTypes.includes(type);
}
async function getDestinationsList() {
  try {
    const response = await fetch(dominio + '/api/destinations');
    destinationsList.value = await response.json();
  } catch (error) {
    console.error('Error al recuperar los destinos:',error);
  }
}


onMounted(() => {
  getDestinationsList();
})


const getDestinationClass = (cost) => {
  return `destiny-${cost}`
}

async function selectDestination() {
  try{
    const response = await fetch(dominio + `/api/destinations/${selectedDestination.value.id}`);
    const data = await response.json();
    selectedDestination.value = data;
  } catch (error) {
    console.error('Error al recuperar los destinos:',error);
  }
  }

  onMounted(() => {
    selectDestination();
  })
  props: ['budget', 'numPeople', 'numDays', 'selectedAccommodation', 'selectedDestination']


</script>




<template>
  <Presupuesto />
  <br>
  <Personas />
  <br>
  <Dias />
  <br>
  <MapDestination />
  <br>
  <DestinationList />
  <br>
  <SummaryVue />
  <br>
  <div>
    <label for="accommodation">Tipo de Alojamiento:</label>
    <select id="accommodation" v-model="selectedAccommodation">
      <option v-for="type in accommodationTypes" :key="type" :value="type">
        {{ type }}
      </option>
    </select>
    
    
  </div>
  <br>
  <div>
    <label for="destinations">Destinos:</label>
      <ul>
        <li v-for="destination in destinationsList" :key="destination.id">
          {{ destination.name }}
        </li>
      </ul>
    </div>


    <br>
    <div>
    <h2>Destinos Disponibles</h2>
    <ul>
      <li v-for="destination in destinations" :key="destination.id" @click="selectDestination(destination)" :class="getDestinationClass(destination.cost)">
        {{ destination.name }}
      </li>
    </ul>
    <p v-if="selectedDestination">Seleccionado: {{ selectedDestination.name }}</p>
  </div>
  <br>
  
  <div>
    <h2>Resumen</h2>
    <p>Presupuesto: {{ budget }}€</p>
    <p>Número de Personas: {{ numPeople }}</p>
    <p>Número de Días: {{ numDays }}</p>
    <p>Tipo de Alojamiento: {{ selectedAccommodation }}</p>
    <p>Destino Seleccionado: {{ selectedDestination ? selectedDestination.name : 'No seleccionado' }}</p>
  </div>
</template>




<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
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
