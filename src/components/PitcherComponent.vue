<script setup>
import PlayerTable from './PlayerTable.vue'
import { ref, onMounted } from 'vue'

const daysOptions = [1,2,3,4,5] 
const selectedDays = ref(1)
const dataMapH = ref({}) // For most-h
const dataMapER = ref({}) // For most-h
const dataMapK = ref({}) // For most-k
const dataMapBB = ref({}) // For most-bb
const loading = ref(true)

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
}
</script>

<template>
  <div>
    <div>
      <button
        v-for="days in daysOptions"
        :key="days"
        @click="selectDays(days)"
        :class="{ active: selectedDays === days }"
      >
        {{ days }}
      </button>
    </div>
    <h2>Most Hits Allowed (Last {{ selectedDays }} games)</h2>
    <PlayerTable :data="dataMapH[selectedDays]" />
    <h2>Most Strikeouts (Last {{ selectedDays }} games)</h2>
    <PlayerTable :data="dataMapK[selectedDays]" />
    <h2>Most Walks (Last {{ selectedDays }} games)</h2>
    <PlayerTable :data="dataMapBB[selectedDays]" />
    <h2>Most ER Allowed (Last {{ selectedDays }} games)</h2>
    <PlayerTable :data="dataMapER[selectedDays]" />
  </div>
</template>

<style scoped>
button.active {
  font-weight: bold;
}
</style>
