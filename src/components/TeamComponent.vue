<script setup>
import PlayerTable from './PlayerTable.vue'
import { ref, onMounted } from 'vue'

const daysOptions = [3,5,7,10,15] 
const selectedDays = ref(3)
const dataMapH = ref({}) 
const dataMapHR = ref({}) 
const dataMapK = ref({}) 
const dataMapBB = ref({}) 
const dataMapRBI = ref({}) 
const loading = ref(true)
const pageH = ref(1)
const pageK = ref(1)
const pageHR = ref(1)
const pageRBI = ref(1)
const pageBB = ref(1)

onMounted(async () => {
  try {
    const baseUrl = window.location.pathname.split('/')[1] || ''
    for (const days of daysOptions) {
      // Fetch most-h data
      const responseH = await fetch(`/${baseUrl}/json/team-most-h-last-${days}-days.json`)
      dataMapH.value[days] = await responseH.json()
      const responseHR = await fetch(`/${baseUrl}/json/team-most-hr-last-${days}-days.json`)
      dataMapHR.value[days] = await responseHR.json()
      const responseK = await fetch(`/${baseUrl}/json/team-most-k-last-${days}-days.json`)
      dataMapK.value[days] = await responseK.json()
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
    <h2>Team Batting Averages</h2>
    <PlayerTable :data="dataMapH[selectedDays]" v-model:currentPage="pageH" :metric="'h'" :position="'team'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
    <h2>Team Strikeouts</h2>
    <PlayerTable :data="dataMapK[selectedDays]" v-model:currentPage="pageK" :metric="'k'" :position="'team'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
    <h2>Team RBIs</h2>
    <PlayerTable :data="dataMapRBI[selectedDays]" v-model:currentPage="pageRBI" :metric="'rbi'" :position="'team'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
    <h2>Team HRs</h2>
    <PlayerTable :data="dataMapHR[selectedDays]" v-model:currentPage="pageHR" :metric="'hr'" :position="'team'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
    <h2>Team Walks</h2>
    <PlayerTable :data="dataMapBB[selectedDays]" v-model:currentPage="pageBB" :metric="'bb'" :position="'team'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
  </div>
</template>

<style scoped>
button.active {
  font-weight: bold;
}
</style>
