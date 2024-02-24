<script setup>

import { ref, onMounted, computed, watch, watchEffect } from 'vue'
import BudgetComponent from './components/BudgetComponent.vue'
import RangeComponent from './components/RangeComponent.vue'
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

async function sendHttpRequest(method, url, data) {
  return await fetch(url, {
    method: method,
    body: JSON.stringify(data),
    headers: data ? { 'Content-Type': 'application/json' } : {}
  }).then((response) => {
    if (response.status >= 400) {
      console.log('Error status code:', response.status)
      console.log('Error URL:', url)
      return response.json().then((errResData) => {
        const error = new Error('Something went wrong!')
        error.data = errResData
        throw error
      })
    }
    return response.json()
  })
}

async function listDestinations () {
  const url = 'http://localhost:3000/api/destinations'
  const response = await sendHttpRequest('GET', url)
  destinations.value = response.sort((a, b) => a.name.localeCompare(b.name))
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

<template>
  <div id="app">
    <div class="container-summary">
      <h1>Destinos turísticos</h1>
      <p>Para {{ person }} durante {{ day }} en {{ destination.name }} alojándose en {{ accommodation }} </p>
      <p v-if="selectDestination"> Se estima: {{ calculateCost }} €</p>
    </div>

    <div class="container-budget">
      <BudgetComponent :budget="budget" :empty-budget="emptyBudget" :alert-budget="alertBudget" @update-budget="validateBudget"/>
    </div>

    <div class="container-days">
      <RangeComponent :value="day" name="days" min="1" :max="30" @update-input="updateDays"/>
    </div>

    <div class="container-persons">
      <RangeComponent :value="person" name="persons" min="1" max="10" @update-input="updatePersons"/>
    </div>

    <div class="container-accommodation">
      <label for="accommodation">Tipo de alojamiento</label>
      <select name="accommodations" id="accommodation" @change="setAccommodation($event.target.value)">
        <option v-for="accommodation in accommodationTypes" :key="accommodation">{{ accommodation }}</option>
      </select>
    </div>

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
</template>


<style scoped>

.container-budget {
  margin-top: 20px;
}
.container-dias {
  margin-top: 20px;
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

.container-personas {
  margin-top: 20px;
  margin-bottom: 20px;
}

.container-map {
  margin-top: 20px;
  margin-bottom: 20px;
}

.container-summary {
  margin-top: 20px;
}

#app {
  font-family: Arial, Helvetica, sans-serif;
  color: #ffffff;
}

.container-accommodation {
  margin-top: 20px;
}

.container-destinos {
  margin-top: 20px;
}

ul {
  list-style-type: none;
}

</style>
