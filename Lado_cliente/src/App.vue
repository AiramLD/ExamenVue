<template>
  <div id="app" class="container">
    <div class="section">
      <div class="container-summary">
        <h1>Destinos turísticos</h1>
        <p>El viaje a " {{ destination.name }} " durante {{ day }} dia(s) hecho por {{ person }} persona(s) y hospedándose en un {{ accommodation }} </p>
        <p v-if="selectDestination"> Su precio es de : {{ calculateCost }} €</p>
      </div>
    </div>

    <!-- Espacio entre secciones -->
    <div style="margin-top: 20px;"></div>

    <div class="section">
      <div class="container-budget">
        <PresupuestoComponente :budget="budget" :empty-budget="emptyBudget" :alert-budget="alertBudget" @update-budget="validateBudget"/>
      </div>
      
      <div class="container-days">
        <RangoComponente :value="day" name="days" min="1" :max="30" @update-input="updateDays"/>
      </div>

      <div class="container-persons">
        <RangoComponente :value="person" name="persons" min="1" max="10" @update-input="updatePersons"/>
      </div>

      <div class="container-accommodation">
        <label for="accommodation">Tipo de alojamiento</label>
        <select name="accommodations" id="accommodation" @change="setAccommodation($event.target.value)">
          <option v-for="accommodation in accommodationTypes" :key="accommodation">{{ accommodation }}</option>
        </select>
      </div>
    </div>

    <!-- Espacio entre secciones -->
    <div style="margin-top: 20px;"></div>

    <div class="section">
      <div class="container-map" v-if="selectDestination">
        <MapDestination :lon="destination.coordinates.longitude" :lat="destination.coordinates.latitude" :zoom="2"/>
      </div>

      <div class="container-destinations">
        <label for="destinations">Selecciona un destino:</label>
        <ul>
          <li v-for="destination in destinations" @click="setDestination(destination)" :key="destination.id" :class="destination.economic_level">{{ destination.name }}</li>
        </ul>
      </div>
    </div>
  </div>
</template>


<script setup>

import { ref, onMounted, computed, watch, watchEffect } from 'vue'
import PresupuestoComponente from './components/PresupuestoComponente.vue'
import RangoComponente from './components/RangoComponente.vue'
import MapDestination from './components/MapDestination.vue'

const budget = ref(0)
const emptyBudget = ref(false)
const person = ref(1)
const day = ref(1)
const accommodationTypes = ["Albergue", "Hostal", "Bed and Breakfast", "Hotel 3*", "Hotel Superior" ];
const destinations = ref([])
const destination = ref({})
const accommodation = ref(accommodationTypes[0])
const selectDestination = ref(false)
const alertBudget = ref('')

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

async function listDestinations () {
  const url = 'http://localhost:3000/api/destinations'
  const response = await fetch(url)
  if (!response.ok) {
    throw new Error('Error al obtener los destinos')
  }
  const data = await response.json()
  destinations.value = data.sort((a, b) => a.name.localeCompare(b.name))
}

function setDestination(dest) {
  destination.value = dest
  selectDestination.value = true
}

function setAccommodation(accom) {
  accommodation.value = accom
}

function validateBudget(event) {
  const value = Number(event.target.value) // Convertir a número
  if (value <= 0) {
    budget.value = 0
    emptyBudget.value = true
  } else {
    budget.value = value
    emptyBudget.value = false
  }
}

function updatePersons(event) {
  if (event.target.value <= 0) {
    person.value = 0
  } else if (event.target.value > 10) {
    person.value = 10
  } else {
    person.value = event.target.value
  }
}

function updateDays(event) {
  if (event.target.value <= 0) {
    day.value = 0
  } else if (event.target.value > 30) {
    day.value = 30
  } else {
    day.value = event.target.value
  }
}

const calculateCost = computed(() => {
  return (costsDestinations[destination.value.economic_level] * costsAccommodations[accommodation.value] * person.value * day.value).toFixed(2)
});

onMounted(() => {
  listDestinations();

  if (localStorage.getItem('budget') && localStorage.getItem('budget') > 0) {
    budget.value = Number(localStorage.getItem('budget')) // Convertir a número
  } else {
    localStorage.removeItem('budget')
    emptyBudget.value = true
  }
});

watch (budget, () => {
  if (budget.value <= 0) {
    emptyBudget.value = true
    localStorage.removeItem('budget')
  } else {
    emptyBudget.value = false
    localStorage.setItem('budget', budget.value)
  }
})

watchEffect(() => {
  if (calculateCost.value < budget.value) {
    alertBudget.value = 'cheap'
  } else if((calculateCost.value * 90 / 100) < budget.value) {
    alertBudget.value = 'moderate'
  } else if (calculateCost.value > budget.value) {
    alertBudget.value = 'expensive'
  }
})

</script>


<style scoped>


.container-summary,
.container-budget,
.container-days,
.container-persons,
.container-accommodation,
.container-map,
.container-destinations {
  margin-top: 20px;
}

.container-dias {
  margin-bottom: 20px;
}


.expensive {
  color: red;
}

.cheap {
  color: green;
}

.moderate {
  color: orange;
}


#app {
  font-family: Arial, Helvetica, sans-serif;
  color: #ffffff;
}

ul {
  list-style-type: none;
}


.container-destinations li {
  cursor: pointer;
  padding: 5px 0;
  transition: color 0.3s ease;
}

.container-destinations li:hover {
  color: #fff;
}

</style>
