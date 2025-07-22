<script setup>
import PlayerStreakTable from './PlayerStreakTable.vue'
import PlayerTable from './PlayerTable.vue'
import { ref, onMounted } from 'vue'

const daysOptions = [3,5,7,10,15] 
const selectedDays = ref(3)
const dataMapH = ref({})
const dataMapHR = ref({})
const dataMapK = ref({}) 
const dataMapR = ref({}) 
const dataMapRBI = ref({}) 
const dataMapHStreak = ref({})
const dataMapHRStreak = ref({})
const dataMapKStreak = ref({}) 
const dataMapRStreak = ref({}) 
const dataMapRBIStreak = ref({}) 
const loading = ref(true)

const currentPage = ref(null);
const pageH = ref(1)
const pageHStreak = ref(1)
const pageK = ref(1)
const pageKStreak = ref(1)
const pageHR = ref(1)
const pageRBI = ref(1)
const pageRBIStreak = ref(1)
const pageR = ref(1)
const pageRStreak = ref(1)

const clickedAverages = ref(true);
const clickedStreaks = ref(false);

onMounted(async () => {
  try {
    const baseUrl = window.location.pathname.split('/')[1] || ''
    for (const days of daysOptions) {
      // Fetch most-h data
      const responseH = await fetch(`/${baseUrl}/json/batting-most-h-last-${days}-days.json`)
      dataMapH.value[days] = await responseH.json()
      const responseK = await fetch(`/${baseUrl}/json/batting-most-k-last-${days}-days.json`)
      dataMapK.value[days] = await responseK.json()
      const responseHR = await fetch(`/${baseUrl}/json/batting-most-hr-last-${days}-days.json`)
      dataMapHR.value[days] = await responseHR.json()
      const responseR = await fetch(`/${baseUrl}/json/batting-most-r-last-${days}-days.json`)
      dataMapR.value[days] = await responseR.json()
      const responseRBI = await fetch(`/${baseUrl}/json/batting-most-rbi-last-${days}-days.json`)
      dataMapRBI.value[days] = await responseRBI.json()
    }
    const responseHStreak = await fetch(`/${baseUrl}/json/batting-1-h-streak.json`)
    dataMapHStreak.value = await responseHStreak.json()
    const responseKStreak = await fetch(`/${baseUrl}/json/batting-1-k-streak.json`)
    dataMapKStreak.value = await responseKStreak.json()
    // const responseHRStreak = await fetch(`/${baseUrl}/json/batting-1-hr-streak.json`)
    // dataMapHRStreak.value = await responseHRStreak.json()
    const responseRBIStreak = await fetch(`/${baseUrl}/json/batting-1-rbi-streak.json`)
    dataMapRBIStreak.value = await responseRBIStreak.json()
    const responseRStreak = await fetch(`/${baseUrl}/json/batting-1-r-streak.json`)
    dataMapRStreak.value = await responseRStreak.json()

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
      <span @click="clickedAverages = true; clickedStreaks = false" class="averages-or-streaks-button" :class="{ 'averages-or-streaks-active': clickedAverages }">Averages</span>
      <span class="divider">|</span>
      <span @click="clickedStreaks = true; clickedAverages = false" class="averages-or-streaks-button" :class="{ 'averages-or-streaks-active': clickedStreaks }">Streaks</span>
    </div>
    <div v-if="clickedAverages">
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
      <div>
        <h2>Top Batter Hits</h2>
          <PlayerTable :data="dataMapH[selectedDays]" v-model:currentPage="pageH" :metric="'h'" :position="'b'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
          <h2>Top Batter Strikeouts</h2>
          <PlayerTable :data="dataMapK[selectedDays]" v-model:currentPage="pageK" :metric="'k'" :position="'b'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
          <h2>Top Batter Home Runs</h2>
          <PlayerTable :data="dataMapHR[selectedDays]" v-model:currentPage="pageHR" :metric="'hr'" :position="'b'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
          <h2>Top Batter RBIs</h2>
          <PlayerTable :data="dataMapRBI[selectedDays]" v-model:currentPage="pageRBI" :metric="'rbi'" :position="'b'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
          <h2>Top Batter Runs</h2>
          <PlayerTable :data="dataMapR[selectedDays]" v-model:currentPage="pageR" :metric="'r'" :position="'b'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
      </div>
    </div>
    
    <div v-if="clickedStreaks">
      <div class="streaks-container">
        &nbsp;
      </div>
      <h2>1+ Hit Streaks</h2>
      <PlayerStreakTable :data="dataMapHStreak" v-model:currentPage="pageHStreak" :metric="'h'" :position="'b'" @update:currentPage="currentPage = $event"/>
      <h2>1+ Strikeout Streaks</h2>
      <PlayerStreakTable :data="dataMapKStreak" v-model:currentPage="pageKStreak" :metric="'k'" :position="'b'" @update:currentPage="currentPage = $event"/>
      <h2>1+ RBI Streaks</h2>
      <PlayerStreakTable :data="dataMapRBIStreak" v-model:currentPage="pageRBIStreak" :metric="'rbi'" :position="'b'" @update:currentPage="currentPage = $event"/>
      <h2>1+ Run Streaks</h2>
      <PlayerStreakTable :data="dataMapRStreak" v-model:currentPage="pageRStreak" :metric="'r'" :position="'b'" @update:currentPage="currentPage = $event"/>
    </div>
    
  </div>
</template>

<style scoped>

</style>
