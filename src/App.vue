<template>
  <div class="app">
    <header class="header">
      <h1>🐴 Horse Champion</h1>
      <nav class="nav">
        <button 
          v-for="item in navItems" 
          :key="item.id"
          @click="currentView = item.id"
          :class="{ active: currentView === item.id }"
        >
          {{ item.label }}
        </button>
      </nav>
    </header>
    
    <main class="main-content">
      <component :is="currentComponent" />
    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import Dashboard from './views/Dashboard.vue'
import StableView from './views/StableView.vue'
import TrainingArena from './views/TrainingArena.vue'
import TournamentHub from './views/TournamentHub.vue'
import HorseDetail from './views/HorseDetail.vue'

const currentView = ref('dashboard')

const navItems = [
  { id: 'dashboard', label: '📊 Dashboard' },
  { id: 'stable', label: '🐴 Stall' },
  { id: 'training', label: '🏆 Trainingsarena' },
  { id: 'tournaments', label: '🎪 Turniere' },
]

const components = {
  dashboard: Dashboard,
  stable: StableView,
  training: TrainingArena,
  tournaments: TournamentHub,
}

const currentComponent = computed(() => components[currentView.value])
</script>

<style scoped>
.app {
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: #333;
}

.header {
  background: rgba(255, 255, 255, 0.95);
  padding: 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.header h1 {
  margin: 0 0 20px 0;
  color: #667eea;
}

.nav {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.nav button {
  padding: 10px 20px;
  border: 2px solid #667eea;
  background: white;
  color: #667eea;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
  transition: all 0.3s;
}

.nav button:hover {
  background: #667eea;
  color: white;
}

.nav button.active {
  background: #667eea;
  color: white;
}

.main-content {
  padding: 30px;
  max-width: 1400px;
  margin: 0 auto;
}
</style>
