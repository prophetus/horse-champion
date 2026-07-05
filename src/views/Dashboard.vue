<template>
  <div class="dashboard">
    <h2>📊 Spieler Dashboard</h2>
    
    <div class="card">
      <h3>Willkommen, {{ playerName }}!</h3>
      <p>Level: {{ playerLevel }} | Erfahrung: {{ playerXP }}/{{ nextLevelXP }}</p>
      <div class="progress">
        <div class="progress-bar" :style="{ width: progressPercent + '%' }"></div>
      </div>
    </div>

    <div class="grid grid-3">
      <div class="stat-box">
        <h3>Pferde</h3>
        <div class="value">{{ horses.length }}</div>
      </div>
      <div class="stat-box">
        <h3>Münzen</h3>
        <div class="value">{{ coins }}</div>
      </div>
      <div class="stat-box">
        <h3>Turniere gewonnen</h3>
        <div class="value">{{ tournamentsWon }}</div>
      </div>
    </div>

    <div class="card">
      <h3>🏆 Meine Pferde</h3>
      <div class="grid grid-3">
        <div class="horse-card" v-for="horse in horses" :key="horse.id">
          <div class="emoji">🐴</div>
          <h4>{{ horse.name }}</h4>
          <p>Rasse: {{ horse.breed }}</p>
          <p>Level: {{ horse.level }}</p>
          <div class="progress" style="margin-top: 10px;">
            <div class="progress-bar" :style="{ width: (horse.health / horse.maxHealth * 100) + '%' }"></div>
          </div>
          <small>Gesundheit: {{ horse.health }}/{{ horse.maxHealth }}</small>
        </div>
      </div>
    </div>

    <div class="card">
      <h3>📈 Statistiken</h3>
      <div class="stats">
        <div class="stat-box">
          <h3>Trainings-Sitzungen</h3>
          <div class="value">{{ trainingSessions }}</div>
        </div>
        <div class="stat-box">
          <h3>Turniere gespielt</h3>
          <div class="value">{{ tournamentsPlayed }}</div>
        </div>
        <div class="stat-box">
          <h3>Gesamt Punkte</h3>
          <div class="value">{{ totalPoints }}</div>
        </div>
        <div class="stat-box">
          <h3>Win-Rate</h3>
          <div class="value">{{ winRate }}%</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const playerName = ref('Champion')
const playerLevel = ref(1)
const playerXP = ref(250)
const nextLevelXP = ref(1000)
const coins = ref(5000)
const tournamentsWon = ref(0)
const trainingSessions = ref(0)
const tournamentsPlayed = ref(0)
const totalPoints = ref(0)

const horses = ref([
  { id: 1, name: 'Thunder', breed: 'Arabier', level: 3, health: 100, maxHealth: 100 },
  { id: 2, name: 'Luna', breed: 'Hannoveraner', level: 2, health: 85, maxHealth: 100 },
  { id: 3, name: 'Storm', breed: 'Westfale', level: 1, health: 90, maxHealth: 100 },
])

const progressPercent = ref((playerXP.value / nextLevelXP.value) * 100)
const winRate = ref(0)
</script>

<style scoped>
.dashboard h2 {
  color: white;
  margin-bottom: 20px;
}

.card {
  background: white;
  border-radius: 12px;
  padding: 20px;
  margin-bottom: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.card h3 {
  color: #667eea;
  margin-bottom: 15px;
}
</style>
