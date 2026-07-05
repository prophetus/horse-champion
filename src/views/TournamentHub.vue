<template>
  <div class="tournament-hub">
    <h2>🎪 Turniere</h2>
    
    <div class="card">
      <h3>Verfügbare Turniere</h3>
      <p>Gesamt gewonnen: {{ tournamentsWon }} | Level: {{ playerLevel }}</p>
    </div>

    <div class="grid grid-2">
      <div class="card" v-for="tournament in tournaments" :key="tournament.id">
        <div style="text-align: center; font-size: 48px; margin-bottom: 15px;">{{ tournament.emoji }}</div>
        
        <h3>{{ tournament.name }}</h3>
        <p>{{ tournament.description }}</p>
        
        <div class="stats">
          <div class="stat-box">
            <h3>Level</h3>
            <div class="value">{{ tournament.requiredLevel }}</div>
          </div>
          <div class="stat-box">
            <h3>Eintritt</h3>
            <div class="value">{{ tournament.entryFee }}</div>
          </div>
          <div class="stat-box">
            <h3>Preisgeld</h3>
            <div class="value">{{ tournament.prizePool }}</div>
          </div>
        </div>

        <div class="tournament-info">
          <p><strong>Typ:</strong> {{ tournament.type }}</p>
          <p><strong>Kategorien:</strong> {{ tournament.categories.join(', ') }}</p>
          <p><strong>Teilnehmer:</strong> {{ tournament.participants }}/{{ tournament.maxParticipants }}</p>
        </div>

        <div style="margin-top: 15px; display: flex; gap: 10px;">
          <button 
            class="btn btn-success" 
            @click="enterTournament(tournament)"
            :disabled="playerLevel < tournament.requiredLevel || tournament.participants >= tournament.maxParticipants"
          >
            Anmelden
          </button>
          <button class="btn btn-primary" @click="showTournamentDetails(tournament)">
            Details
          </button>
        </div>
      </div>
    </div>

    <!-- Tournament Details Modal -->
    <div class="modal" v-if="selectedTournament" @click.self="selectedTournament = null">
      <div class="modal-content">
        <h3>{{ selectedTournament.name }}</h3>
        <p>{{ selectedTournament.description }}</p>
        <p><strong>Gesamtkurs:</strong> {{ selectedTournament.length }}km</p>
        <p><strong>Schwierigkeit:</strong> {{ selectedTournament.difficulty }}/10</p>
        <p><strong>Hindernisse:</strong> {{ selectedTournament.obstacles }}</p>
        <button class="btn btn-primary" @click="selectedTournament = null">Schließen</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const playerLevel = ref(1)
const tournamentsWon = ref(0)
const selectedTournament = ref(null)

const tournaments = ref([
  {
    id: 1,
    name: 'Anfänger Dressur',
    emoji: '💃',
    type: 'Dressur',
    description: 'Perfekt für Anfänger zum Üben von Basistechniken',
    requiredLevel: 1,
    entryFee: 100,
    prizePool: 500,
    categories: ['Anfänger', 'Juniors'],
    participants: 5,
    maxParticipants: 10,
    length: 500,
    difficulty: 2,
    obstacles: 5,
  },
  {
    id: 2,
    name: 'Spring Challenge',
    emoji: '🚀',
    type: 'Springreiten',
    description: 'Teste deine Sprungfähigkeiten',
    requiredLevel: 2,
    entryFee: 300,
    prizePool: 2000,
    categories: ['Anfänger', 'Fortgeschritten'],
    participants: 3,
    maxParticipants: 8,
    length: 800,
    difficulty: 5,
    obstacles: 12,
  },
  {
    id: 3,
    name: 'Grand Prix',
    emoji: '👑',
    type: 'Vielseitigkeitsreiterei',
    description: 'Das höchste Turnier mit Dressur, Gelände und Springen',
    requiredLevel: 5,
    entryFee: 1000,
    prizePool: 10000,
    categories: ['Profi', 'Elite'],
    participants: 2,
    maxParticipants: 5,
    length: 2000,
    difficulty: 10,
    obstacles: 25,
  },
  {
    id: 4,
    name: 'Geländeritt',
    emoji: '🌲',
    type: 'Geländereiten',
    description: 'Travers schwieriges Gelände',
    requiredLevel: 3,
    entryFee: 250,
    prizePool: 1500,
    categories: ['Fortgeschritten'],
    participants: 6,
    maxParticipants: 12,
    length: 1500,
    difficulty: 6,
    obstacles: 15,
  },
])

const enterTournament = (tournament) => {
  alert(`Du hast dich für ${tournament.name} angemeldet!`)
}

const showTournamentDetails = (tournament) => {
  selectedTournament.value = tournament
}
</script>

<style scoped>
.tournament-hub h2 {
  color: white;
  margin-bottom: 20px;
}

.tournament-info {
  background: #f5f5f5;
  padding: 15px;
  border-radius: 8px;
  margin: 15px 0;
}

.tournament-info p {
  margin: 5px 0;
  font-size: 14px;
}
</style>
