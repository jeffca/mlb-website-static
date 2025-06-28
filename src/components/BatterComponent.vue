<script setup>
import PlayerTable from './PlayerTable.vue'
import { ref, onMounted } from 'vue'

const daysOptions = [3,5,7,10,15] 
const selectedDays = ref(3)
const dataMapH = ref({}) // For most-h
const dataMapHR = ref({}) // For most-h
const dataMapK = ref({}) // For most-k
const dataMapR = ref({}) // For most-h
const dataMapRBI = ref({}) // For most-bb
const loading = ref(true)

const pageH = ref(1)
const pageK = ref(1)
const pageHR = ref(1)
const pageRBI = ref(1)
const pageR = ref(1)

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
</template>

<style scoped>

</style>
