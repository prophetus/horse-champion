<template>
  <div class="stable-view">
    <h2>🐴 Mein Stall</h2>
    
    <div class="card">
      <button class="btn btn-primary" @click="showNewHorseModal = true">+ Neues Pferd kaufen</button>
    </div>

    <div class="grid grid-2">
      <div class="card" v-for="horse in horses" :key="horse.id">
        <div style="text-align: center; font-size: 48px; margin-bottom: 15px;">🐴</div>
        <h3>{{ horse.name }}</h3>
        
        <div class="stat-box" style="margin-bottom: 15px;">
          <p><strong>Rasse:</strong> {{ horse.breed }}</p>
          <p><strong>Alter:</strong> {{ horse.age }} Jahre</p>
          <p><strong>Level:</strong> {{ horse.level }}</p>
        </div>

        <h4>Fähigkeiten:</h4>
        <div class="stats">
          <div class="stat-box">
            <h3>Geschwindigkeit</h3>
            <div class="value">{{ horse.speed }}</div>
          </div>
          <div class="stat-box">
            <h3>Ausdauer</h3>
            <div class="value">{{ horse.endurance }}</div>
          </div>
          <div class="stat-box">
            <h3>Sprung</h3>
            <div class="value">{{ horse.jumping }}</div>
          </div>
        </div>

        <h4 style="margin-top: 15px;">Zustand:</h4>
        <p><strong>Gesundheit:</strong> {{ horse.health }}/{{ horse.maxHealth }}</p>
        <div class="progress">
          <div class="progress-bar" :style="{ width: (horse.health / horse.maxHealth * 100) + '%' }"></div>
        </div>

        <p><strong>Energie:</strong> {{ horse.energy }}/{{ horse.maxEnergy }}</p>
        <div class="progress">
          <div class="progress-bar" :style="{ width: (horse.energy / horse.maxEnergy * 100) + '%' }"></div>
        </div>

        <div style="margin-top: 15px; display: flex; gap: 10px;">
          <button class="btn btn-secondary" @click="selectHorse(horse)">Pflegen</button>
          <button class="btn btn-primary" @click="selectHorse(horse)">Details</button>
        </div>
      </div>
    </div>

    <!-- New Horse Modal -->
    <div class="modal" v-if="showNewHorseModal" @click.self="showNewHorseModal = false">
      <div class="modal-content">
        <h3>🐴 Neues Pferd kaufen</h3>
        <input v-model="newHorse.name" placeholder="Pferd Name">
        <select v-model="newHorse.breed">
          <option>Arabier</option>
          <option>Hannoveraner</option>
          <option>Westfale</option>
          <option>Vollblüter</option>
          <option>Warmblüter</option>
        </select>
        <p><strong>Preis:</strong> 1000 Münzen</p>
        <div style="margin-top: 20px; display: flex; gap: 10px;">
          <button class="btn btn-success" @click="buyHorse">Kaufen</button>
          <button class="btn btn-secondary" @click="showNewHorseModal = false">Abbrechen</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const showNewHorseModal = ref(false)
const selectedHorse = ref(null)

const horses = ref([
  {
    id: 1,
    name: 'Thunder',
    breed: 'Arabier',
    age: 5,
    level: 3,
    health: 100,
    maxHealth: 100,
    energy: 80,
    maxEnergy: 100,
    speed: 8,
    endurance: 7,
    jumping: 6,
  },
  {
    id: 2,
    name: 'Luna',
    breed: 'Hannoveraner',
    age: 4,
    level: 2,
    health: 85,
    maxHealth: 100,
    energy: 60,
    maxEnergy: 100,
    speed: 7,
    endurance: 8,
    jumping: 7,
  },
])

const newHorse = ref({
  name: '',
  breed: 'Arabier',
})

const selectHorse = (horse) => {
  selectedHorse.value = horse
}

const buyHorse = () => {
  horses.value.push({
    id: horses.value.length + 1,
    name: newHorse.value.name,
    breed: newHorse.value.breed,
    age: 1,
    level: 1,
    health: 100,
    maxHealth: 100,
    energy: 100,
    maxEnergy: 100,
    speed: 5,
    endurance: 5,
    jumping: 4,
  })
  showNewHorseModal.value = false
  newHorse.value = { name: '', breed: 'Arabier' }
}
</script>

<style scoped>
.stable-view h2 {
  color: white;
  margin-bottom: 20px;
}
</style>
