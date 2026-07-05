<template>
  <div class="training-arena">
    <h2>🏆 Trainingsarena</h2>
    
    <div class="card">
      <h3>Wähle ein Pferd zum Trainieren</h3>
      <select v-model="selectedHorseId" class="select-horse">
        <option :value="null">-- Wähle ein Pferd --</option>
        <option v-for="horse in horses" :key="horse.id" :value="horse.id">
          {{ horse.name }} (Level {{ horse.level }})
        </option>
      </select>
    </div>

    <div v-if="selectedHorse" class="card">
      <h3>{{ selectedHorse.name }} trainieren</h3>
      
      <div class="grid grid-3">
        <div class="stat-box">
          <h3>Level</h3>
          <div class="value">{{ selectedHorse.level }}</div>
        </div>
        <div class="stat-box">
          <h3>Energie</h3>
          <div class="value">{{ selectedHorse.energy }}/{{ selectedHorse.maxEnergy }}</div>
        </div>
        <div class="stat-box">
          <h3>Gesundheit</h3>
          <div class="value">{{ selectedHorse.health }}/{{ selectedHorse.maxHealth }}</div>
        </div>
      </div>

      <h3 style="margin-top: 30px;">Trainingstypen</h3>
      <div class="grid grid-3">
        <div class="training-card" v-for="training in trainingTypes" :key="training.id">
          <div class="emoji">{{ training.emoji }}</div>
          <h4>{{ training.name }}</h4>
          <p>{{ training.description }}</p>
          <p><strong>Energie:</strong> {{ training.energyCost }}</p>
          <p><strong>Belohnung:</strong> {{ training.xpReward }} XP</p>
          <button 
            class="btn btn-primary" 
            @click="startTraining(training)"
            :disabled="selectedHorse.energy < training.energyCost"
          >
            Starten
          </button>
        </div>
      </div>
    </div>

    <div v-if="isTraining" class="card">
      <h3>⏳ Training läuft...</h3>
      <div class="progress">
        <div class="progress-bar" :style="{ width: trainingProgress + '%' }"></div>
      </div>
      <p>{{ trainingProgress }}%</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const selectedHorseId = ref(null)
const isTraining = ref(false)
const trainingProgress = ref(0)

const horses = ref([
  {
    id: 1,
    name: 'Thunder',
    level: 3,
    energy: 100,
    maxEnergy: 100,
    health: 100,
    maxHealth: 100,
  },
  {
    id: 2,
    name: 'Luna',
    level: 2,
    energy: 60,
    maxEnergy: 100,
    health: 85,
    maxHealth: 100,
  },
])

const trainingTypes = ref([
  {
    id: 1,
    name: 'Grundlagen',
    emoji: '🏃',
    description: 'Grundlagen-Training für Anfänger',
    energyCost: 20,
    xpReward: 100,
    skillBoost: { speed: 1, endurance: 1, jumping: 0 },
  },
  {
    id: 2,
    name: 'Ausdauer',
    emoji: '💨',
    description: 'Erhöhe die Ausdauer deines Pferdes',
    energyCost: 30,
    xpReward: 150,
    skillBoost: { speed: 0, endurance: 3, jumping: 0 },
  },
  {
    id: 3,
    name: 'Sprung',
    emoji: '🚀',
    description: 'Trainiere Sprungfähigkeiten',
    energyCost: 35,
    xpReward: 200,
    skillBoost: { speed: 1, endurance: 1, jumping: 3 },
  },
  {
    id: 4,
    name: 'Dressur',
    emoji: '💃',
    description: 'Dressur und Präzision',
    energyCost: 25,
    xpReward: 120,
    skillBoost: { speed: 2, endurance: 1, jumping: 1 },
  },
])

const selectedHorse = computed(() => {
  return horses.value.find(h => h.id === selectedHorseId.value)
})

const startTraining = (training) => {
  if (!selectedHorse.value) return
  
  selectedHorse.value.energy -= training.energyCost
  isTraining.value = true
  trainingProgress.value = 0
  
  const interval = setInterval(() => {
    trainingProgress.value += Math.random() * 15
    if (trainingProgress.value >= 100) {
      trainingProgress.value = 100
      clearInterval(interval)
      setTimeout(() => {
        isTraining.value = false
        trainingProgress.value = 0
        alert(`${training.name}-Training abgeschlossen! +${training.xpReward} XP`)
      }, 500)
    }
  }, 300)
}
</script>

<style scoped>
.training-arena h2 {
  color: white;
  margin-bottom: 20px;
}

.select-horse {
  padding: 10px;
  font-size: 16px;
  margin: 10px 0;
}

.training-card {
  background: white;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
  transition: transform 0.3s;
}

.training-card:hover {
  transform: translateY(-5px);
}

.training-card .emoji {
  font-size: 48px;
  margin-bottom: 10px;
}

.training-card button {
  width: 100%;
  margin-top: 10px;
}

.training-card button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
