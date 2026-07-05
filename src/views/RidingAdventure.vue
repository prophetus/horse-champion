<template>
  <div class="riding-adventure">
    <h2>🌲 Reiten & Abenteuer</h2>
    
    <!-- Horse Selection -->
    <div class="card" v-if="!activeRide">
      <h3>Wähle ein Pferd zum Reiten</h3>
      <select v-model="selectedHorseId" class="select-horse">
        <option :value="null">-- Wähle ein Pferd --</option>
        <option v-for="horse in horses" :key="horse.id" :value="horse.id">
          {{ horse.name }} (Level {{ horse.level }}) - Energie: {{ horse.energy }}/{{ horse.maxEnergy }}
        </option>
      </select>
    </div>

    <!-- Routes Selection -->
    <div class="card" v-if="selectedHorse && !activeRide">
      <h3>Wähle einen Reitpfad</h3>
      <div class="grid grid-3">
        <div class="route-card" v-for="route in routes" :key="route.id">
          <div class="emoji">{{ route.emoji }}</div>
          <h4>{{ route.name }}</h4>
          <p>{{ route.description }}</p>
          
          <div class="stats">
            <div class="stat-box">
              <h3>Distanz</h3>
              <div class="value">{{ route.distance }}km</div>
            </div>
            <div class="stat-box">
              <h3>Schwierigkeit</h3>
              <div class="value">{{ route.difficulty }}/5</div>
            </div>
            <div class="stat-box">
              <h3>Energie</h3>
              <div class="value">{{ route.energyCost }}</div>
            </div>
          </div>

          <p><strong>Belohnung:</strong> {{ route.coins }} Münzen + {{ route.xp }} XP</p>

          <button 
            class="btn btn-success" 
            @click="startRide(route)"
            :disabled="selectedHorse.energy < route.energyCost"
          >
            🚀 Starten
          </button>
        </div>
      </div>
    </div>

    <!-- Active Ride -->
    <div class="card" v-if="activeRide">
      <h3>🐎 {{ selectedHorse.name }} - {{ activeRide.name }}</h3>
      
      <div class="riding-scene">
        <div class="scene-container">
          <div class="scenery">{{ activeRide.scenery }}</div>
          <div class="horse-rider">
            <div class="horse">🐴</div>
            <div class="rider">👤</div>
          </div>
        </div>

        <div class="ride-stats">
          <div class="stat-display">
            <h4>Distanz</h4>
            <p>{{ rideProgress }}km / {{ activeRide.distance }}km</p>
            <div class="progress">
              <div class="progress-bar" :style="{ width: (rideProgress / activeRide.distance * 100) + '%' }"></div>
            </div>
          </div>

          <div class="stat-display">
            <h4>Pferdegesundheit</h4>
            <p>{{ selectedHorse.health }}/{{ selectedHorse.maxHealth }}</p>
            <div class="progress">
              <div class="progress-bar" :style="{ width: (selectedHorse.health / selectedHorse.maxHealth * 100) + '%' }"></div>
            </div>
          </div>

          <div class="stat-display">
            <h4>Energie</h4>
            <p>{{ selectedHorse.energy }}/{{ selectedHorse.maxEnergy }}</p>
            <div class="progress">
              <div class="progress-bar" :style="{ width: (selectedHorse.energy / selectedHorse.maxEnergy * 100) + '%' }"></div>
            </div>
          </div>
        </div>
      </div>

      <div class="controls">
        <button class="btn btn-primary" @click="accelerate" :disabled="selectedHorse.energy < 5">
          ⚡ Beschleunigen
        </button>
        <button class="btn btn-secondary" @click="normalPace">
          🚶 Normales Tempo
        </button>
        <button class="btn btn-warning" @click="stopRide">
          ⛔ Beenden
        </button>
      </div>

      <!-- Events during ride -->
      <div v-if="currentEvent" class="event-popup">
        <div class="event-content">
          <h3>{{ currentEvent.title }}</h3>
          <p>{{ currentEvent.description }}</p>
          <div v-if="currentEvent.choices" class="event-choices">
            <button 
              v-for="choice in currentEvent.choices" 
              :key="choice.id"
              class="btn btn-primary"
              @click="handleEventChoice(choice)"
            >
              {{ choice.text }}
            </button>
          </div>
          <button v-else class="btn btn-success" @click="dismissEvent">
            Verstanden
          </button>
        </div>
      </div>
    </div>

    <!-- Ride Complete -->
    <div class="card" v-if="rideComplete">
      <h3>🎉 Reitabenteuer abgeschlossen!</h3>
      
      <div class="completion-stats">
        <div class="stat-box">
          <h3>Distanz geritten</h3>
          <div class="value">{{ activeRide.distance }}km</div>
        </div>
        <div class="stat-box">
          <h3>Münzen verdient</h3>
          <div class="value">+{{ rideRewards.coins }}</div>
        </div>
        <div class="stat-box">
          <h3>XP verdient</h3>
          <div class="value">+{{ rideRewards.xp }}</div>
        </div>
        <div class="stat-box">
          <h3>Zufriedenheit</h3>
          <div class="value">+{{ rideRewards.happiness }}</div>
        </div>
      </div>

      <p><strong>Pferd-Zustand:</strong></p>
      <p>Energie: {{ selectedHorse.energy }}/{{ selectedHorse.maxEnergy }}</p>
      <p>Gesundheit: {{ selectedHorse.health }}/{{ selectedHorse.maxHealth }}</p>

      <button class="btn btn-primary" @click="resetRide" style="margin-top: 20px;">
        Zurück zum Stall
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const selectedHorseId = ref(null)
const activeRide = ref(null)
const rideComplete = ref(false)
const rideProgress = ref(0)
const currentEvent = ref(null)
const rideRewards = ref({ coins: 0, xp: 0, happiness: 0 })

const horses = ref([
  {
    id: 1,
    name: 'Thunder',
    level: 3,
    health: 100,
    maxHealth: 100,
    energy: 100,
    maxEnergy: 100,
  },
  {
    id: 2,
    name: 'Luna',
    level: 2,
    health: 85,
    maxHealth: 100,
    energy: 60,
    maxEnergy: 100,
  },
])

const routes = ref([
  {
    id: 1,
    name: 'Waldweg',
    emoji: '🌲',
    description: 'Ein gemütlicher Spaziergang durch den Wald',
    scenery: '🌲🌳🌲',
    distance: 5,
    difficulty: 1,
    energyCost: 15,
    coins: 100,
    xp: 50,
  },
  {
    id: 2,
    name: 'Bergpfad',
    emoji: '⛰️',
    description: 'Anspruchsvoller Aufstieg mit herrlicher Aussicht',
    scenery: '⛰️🏔️🧗',
    distance: 10,
    difficulty: 3,
    energyCost: 35,
    coins: 300,
    xp: 150,
  },
  {
    id: 3,
    name: 'Strandritt',
    emoji: '🏖️',
    description: 'Entspannter Galopp am Strand entlang',
    scenery: '🌊🏖️🌴',
    distance: 8,
    difficulty: 2,
    energyCost: 25,
    coins: 200,
    xp: 100,
  },
  {
    id: 4,
    name: 'Wildnis-Explorer',
    emoji: '🌿',
    description: 'Abenteuerliche Expedition durch unbekanntes Gelände',
    scenery: '🌿🦁🐘',
    distance: 15,
    difficulty: 4,
    energyCost: 50,
    coins: 500,
    xp: 250,
  },
  {
    id: 5,
    name: 'Nachtreitgang',
    emoji: '🌙',
    description: 'Mystischer Ritt unter dem Sternenhimmel',
    scenery: '🌙⭐✨',
    distance: 12,
    difficulty: 3,
    energyCost: 40,
    coins: 400,
    xp: 200,
  },
])

const events = ref([
  {
    id: 1,
    title: '🦌 Hirsch-Begegnung',
    description: 'Ein wunderschöner Hirsch kreuzt deinen Weg!',
    effect: { health: 5, happiness: 10 },
  },
  {
    id: 2,
    title: '⛈️ Plötzlicher Regen',
    description: 'Ein Regenschauer zieht auf!',
    effect: { health: -5, energy: -5 },
  },
  {
    id: 3,
    title: '🌻 Blumenwiese',
    description: 'Du findest eine wunderbare Blumenwiese zum Grasen!',
    effect: { health: 10, happiness: 15 },
  },
  {
    id: 4,
    title: '🦅 Adler im Flug',
    description: 'Ein majestätischer Adler fliegt über euch!',
    effect: { happiness: 8 },
  },
  {
    id: 5,
    title: '🏞️ Atemberaubende Aussicht',
    description: 'Ihr erreicht einen Punkt mit herrlicher Aussicht!',
    effect: { happiness: 12 },
  },
])

const selectedHorse = computed(() => {
  return horses.value.find(h => h.id === selectedHorseId.value)
})

const startRide = (route) => {
  if (!selectedHorse.value || selectedHorse.value.energy < route.energyCost) return
  
  activeRide.value = route
  rideProgress.value = 0
  rideRewards.value = { coins: route.coins, xp: route.xp, happiness: 0 }
  selectedHorse.value.energy -= route.energyCost
  
  // Simulate ride with random events
  simulateRide()
}

const simulateRide = () => {
  const interval = setInterval(() => {
    if (activeRide.value) {
      rideProgress.value += 0.5
      
      // Random event chance
      if (Math.random() < 0.05 && !currentEvent.value) {
        const randomEvent = events.value[Math.floor(Math.random() * events.value.length)]
        triggerEvent(randomEvent)
      }
      
      // Horse stamina decrease
      if (selectedHorse.value.energy > 0) {
        selectedHorse.value.energy = Math.max(0, selectedHorse.value.energy - 0.2)
      }
      
      if (rideProgress.value >= activeRide.value.distance) {
        rideProgress.value = activeRide.value.distance
        clearInterval(interval)
        completeRide()
      }
    } else {
      clearInterval(interval)
    }
  }, 500)
}

const triggerEvent = (event) => {
  currentEvent.value = {
    title: event.title,
    description: event.description,
    effect: event.effect,
  }
  
  // Apply effects
  if (event.effect.health) {
    selectedHorse.value.health = Math.max(0, Math.min(selectedHorse.value.maxHealth, selectedHorse.value.health + event.effect.health))
  }
  if (event.effect.happiness) {
    rideRewards.value.happiness += event.effect.happiness
  }
  if (event.effect.energy) {
    selectedHorse.value.energy = Math.max(0, selectedHorse.value.energy + event.effect.energy)
  }
}

const dismissEvent = () => {
  currentEvent.value = null
}

const handleEventChoice = (choice) => {
  // Implement choice logic if needed
  dismissEvent()
}

const accelerate = () => {
  if (selectedHorse.value.energy >= 5) {
    selectedHorse.value.energy -= 5
    rideProgress.value += 1
    rideRewards.value.coins += 10
  }
}

const normalPace = () => {
  // Just continue at normal pace
}

const stopRide = () => {
  if (confirm('Möchtest du den Ritt beenden?')) {
    completeRide()
  }
}

const completeRide = () => {
  activeRide.value = null
  rideComplete.value = true
}

const resetRide = () => {
  activeRide.value = null
  rideComplete.value = false
  rideProgress.value = 0
  selectedHorseId.value = null
  currentEvent.value = null
}
</script>

<style scoped>
.riding-adventure h2 {
  color: white;
  margin-bottom: 20px;
}

.select-horse {
  padding: 10px;
  font-size: 16px;
  margin: 10px 0;
  width: 100%;
}

.route-card {
  background: white;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
  transition: transform 0.3s;
}

.route-card:hover {
  transform: translateY(-5px);
}

.route-card .emoji {
  font-size: 48px;
  margin-bottom: 10px;
}

.route-card button {
  width: 100%;
  margin-top: 10px;
}

.route-card button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.riding-scene {
  background: linear-gradient(180deg, #87ceeb 0%, #e0f6ff 100%);
  border-radius: 12px;
  padding: 30px;
  margin: 20px 0;
  text-align: center;
}

.scene-container {
  position: relative;
  height: 200px;
  margin-bottom: 20px;
}

.scenery {
  font-size: 48px;
  margin-bottom: 10px;
}

.horse-rider {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 5px;
  font-size: 64px;
  animation: gallop 1s infinite;
}

@keyframes gallop {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.ride-stats {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
  margin: 20px 0;
}

.stat-display {
  background: white;
  padding: 15px;
  border-radius: 8px;
}

.stat-display h4 {
  color: #667eea;
  margin-bottom: 10px;
}

.controls {
  display: flex;
  gap: 10px;
  justify-content: center;
  margin-top: 20px;
  flex-wrap: wrap;
}

.controls button {
  flex: 1;
  min-width: 150px;
}

.event-popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.event-content {
  background: white;
  border-radius: 12px;
  padding: 30px;
  max-width: 500px;
  text-align: center;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

.event-content h3 {
  font-size: 32px;
  margin-bottom: 15px;
  color: #667eea;
}

.event-choices {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-top: 20px;
}

.event-choices button {
  width: 100%;
}

.completion-stats {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 15px;
  margin: 20px 0;
}

.btn-warning {
  background: #ff9800;
  color: white;
}

.btn-warning:hover {
  background: #e68900;
}
</style>
