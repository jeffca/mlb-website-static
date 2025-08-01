<script setup>
import PlayerTable from './PlayerTable.vue'
import { ref, onMounted } from 'vue'

const daysOptions = [3,5,7,10] 
const selectedDays = ref(3)
const currentPage = ref(1);
const dataMapH = ref({}) 
const dataMapHPitching = ref({}) 
const dataMapHR = ref({}) 
const dataMapK = ref({}) 
const dataMapKPitching = ref({}) 
const dataMapBB = ref({}) 
const dataMapRBI = ref({}) 
const loading = ref(true)
const pageH = ref(1)
const pageHPitching = ref(1)
const pageK = ref(1)
const pageKPitching = ref(1)
const pageHR = ref(1)
const pageRBI = ref(1)
const pageBB = ref(1)

const selectedTab = ref('batting') // 'batting' or 'pitching'

onMounted(async () => {
  try {
    const baseUrl = window.location.pathname.split('/')[1] || ''
    for (const days of daysOptions) {
      // Fetch most-h data
      const responseH = await fetch(`/${baseUrl}/json/team-most-h-last-${days}-days.json`)
      dataMapH.value[days] = await responseH.json()
      const responseHPitching = await fetch(`/${baseUrl}/json/team-most-h-pitching-last-${days}-games.json`)
      dataMapHPitching.value[days] = await responseHPitching.json()
      const responseHR = await fetch(`/${baseUrl}/json/team-most-hr-last-${days}-days.json`)
      dataMapHR.value[days] = await responseHR.json()
      const responseK = await fetch(`/${baseUrl}/json/team-most-k-last-${days}-days.json`)
      dataMapK.value[days] = await responseK.json()
      const responseKPitching = await fetch(`/${baseUrl}/json/team-most-k-pitching-last-${days}-games.json`)
      dataMapKPitching.value[days] = await responseKPitching.json()
      const responseBB = await fetch(`/${baseUrl}/json/team-most-bb-last-${days}-days.json`)
      dataMapBB.value[days] = await responseBB.json()
      const responseRBI = await fetch(`/${baseUrl}/json/team-most-rbi-last-${days}-days.json`)
      dataMapRBI.value[days] = await responseRBI.json()
    }
    loading.value = false
  } catch (error) {
    console.error('Error fetching data:', error)
    loading.value = false
  }
})

function selectDays(days) {
  selectedDays.value = days
  currentPage.value = 1;
}
</script>

<template>
  <div>
    <div class="center">
        <span class="averages-or-streaks-button bold-text">Averages</span>
      </div>
    <div class="button-div">
        <div class="button-group">
            <button
                v-for="days in daysOptions"
                :key="days"
                @click="selectDays(days)"
                :class="{ active: selectedDays === days }"
            >
                {{ days }}
            </button>
        </div>
        <span class="days-label">Last {{ selectedDays }} days</span>
    </div>
    <div class="tab-buttons-container">
      <div class="tab-buttons">
        <button class="team-button" :class="{ active: selectedTab === 'batting' }" @click="selectedTab = 'batting'">Batting</button>
        <button class="team-button" :class="{ active: selectedTab === 'pitching' }" @click="selectedTab = 'pitching'">Pitching</button>
      </div>
    </div>

    <div v-if="selectedTab === 'batting'">
      <h2>Team Batting Averages</h2>
      <PlayerTable :data="dataMapH[selectedDays]" v-model:currentPage="pageH" metric="h" position="team" :selectedDays="selectedDays" />
      
      <h2>Team Strikeouts</h2>
      <PlayerTable :data="dataMapK[selectedDays]" v-model:currentPage="pageK" metric="k" position="team" :selectedDays="selectedDays" />
      
      <h2>Team RBIs</h2>
      <PlayerTable :data="dataMapRBI[selectedDays]" v-model:currentPage="pageRBI" metric="rbi" position="team" :selectedDays="selectedDays" />
      
      <h2>Team HRs</h2>
      <PlayerTable :data="dataMapHR[selectedDays]" v-model:currentPage="pageHR" metric="hr" position="team" :selectedDays="selectedDays" />
      
      <h2>Team Walks</h2>
      <PlayerTable :data="dataMapBB[selectedDays]" v-model:currentPage="pageBB" metric="bb" position="team" :selectedDays="selectedDays" />
    </div>

    <div v-else-if="selectedTab === 'pitching'">
      <h2>Team Pitching: Hits Allowed</h2>
      <PlayerTable :data="dataMapHPitching[selectedDays]" v-model:currentPage="pageHPitching" metric="h" position="team" :selectedDays="selectedDays" />
      
      <h2>Team Pitching: Strikeouts</h2>
      <PlayerTable :data="dataMapKPitching[selectedDays]" v-model:currentPage="pageKPitching" metric="k" position="team" :selectedDays="selectedDays" />
    </div>
  </div>
</template>

<style scoped>

.team-button { 
  background-color: transparent;
  color: white;
}

.tab-buttons-container {
  display: flex;
  justify-content: center;
  margin: 1rem 0;
}

.tab-buttons {
  display: flex;
  gap: 1rem;
}

.tab-buttons button {
  padding: 0.5rem 1.2rem;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 1rem;
  transition: background 0.3s;
}

.tab-buttons button.active {
  font-weight: bold;
}


button.active {
  font-weight: bold;
}
</style>
