<script setup>
import PlayerTable from './PlayerTable.vue'
import { ref, onMounted } from 'vue'

const daysOptions = [1,2,3,4,5] 
const selectedDays = ref(1)
const dataMapH = ref({}) 
const dataMapER = ref({})
const dataMapK = ref({}) 
const dataMapBB = ref({}) 
const loading = ref(true)
const currentPage = ref(1); 

onMounted(async () => {
  try {
    const baseUrl = window.location.pathname.split('/')[1] || ''
    for (const days of daysOptions) {
      // Fetch most-h data
      const responseH = await fetch(`/${baseUrl}/json/pitching-most-h-last-${days}-games.json`)
      dataMapH.value[days] = await responseH.json()
      const responseER = await fetch(`/${baseUrl}/json/pitching-most-er-last-${days}-games.json`)
      dataMapER.value[days] = await responseER.json()
      const responseK = await fetch(`/${baseUrl}/json/pitching-most-k-last-${days}-games.json`)
      dataMapK.value[days] = await responseK.json()
      const responseBB = await fetch(`/${baseUrl}/json/pitching-most-bb-last-${days}-games.json`)
      dataMapBB.value[days] = await responseBB.json()
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
      <span>Last {{ selectedDays }} starts</span>
    </div>
    <h2>SP Hits Allowed</h2>
    <PlayerTable :data="dataMapH[selectedDays]" :currentPage="currentPage" @update:currentPage="currentPage = $event"/>
    <h2>SP Strikeouts</h2>
    <PlayerTable :data="dataMapK[selectedDays]" :currentPage="currentPage" @update:currentPage="currentPage = $event"/>
    <h2>SP Walks</h2>
    <PlayerTable :data="dataMapBB[selectedDays]" :currentPage="currentPage" @update:currentPage="currentPage = $event"/>
    <h2>SP ER Allowed</h2>
    <PlayerTable :data="dataMapER[selectedDays]" :currentPage="currentPage" @update:currentPage="currentPage = $event"/>
  </div>
</template>

<style scoped>

</style>
