<script setup>
import PlayerTable from './PlayerTable.vue'
import { ref, onMounted } from 'vue'

const daysOptions = [3,5,7,10,15] 
const selectedDays = ref(3)
const dataMapH = ref({}) 
const dataMapK = ref({}) 
const dataMapBB = ref({}) 
const dataMapRBI = ref({}) 
const loading = ref(true)
const currentPage = ref(1); 

onMounted(async () => {
  try {
    const baseUrl = window.location.pathname.split('/')[1] || ''
    for (const days of daysOptions) {
      // Fetch most-h data
      const responseH = await fetch(`/${baseUrl}/json/team-most-h-last-${days}-days.json`)
      dataMapH.value[days] = await responseH.json()
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
    <div class="button-div">
      <button
        v-for="days in daysOptions"
        :key="days"
        @click="selectDays(days)"
        :class="{ active: selectedDays === days }"
      >
        {{ days }}
      </button>
      <span>Last {{ selectedDays }} days</span>
    </div>
    <h2>Team Hits</h2>
    <PlayerTable :data="dataMapH[selectedDays]" :currentPage="currentPage" @update:currentPage="currentPage = $event"/>
    <h2>Team Strikeouts</h2>
    <PlayerTable :data="dataMapK[selectedDays]" :currentPage="currentPage" @update:currentPage="currentPage = $event"/>
    <h2>Team Walks</h2>
    <PlayerTable :data="dataMapBB[selectedDays]" :currentPage="currentPage" @update:currentPage="currentPage = $event"/>
    <h2>Team RBIs</h2>
    <PlayerTable :data="dataMapRBI[selectedDays]" :currentPage="currentPage" @update:currentPage="currentPage = $event"/>
  </div>
</template>

<style scoped>
button.active {
  font-weight: bold;
}
</style>
