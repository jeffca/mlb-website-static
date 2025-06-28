<script setup>
import PlayerTable from './PlayerTable.vue'
import { ref, onMounted } from 'vue'

const daysOptions = [1,2,3,4,5] 
const selectedDays = ref(1)
const dataMapH = ref({}) 
const dataMapHR = ref({}) 
const dataMapER = ref({})
const dataMapK = ref({}) 
const dataMapBB = ref({}) 
const dataMapIP = ref({}) 
const loading = ref(true)
const pageH = ref(1)
const pageK = ref(1)
const pageHR = ref(1)
const pageER = ref(1)
const pageBB = ref(1)
const pageIP = ref(1)

onMounted(async () => {
  try {
    const baseUrl = window.location.pathname.split('/')[1] || ''
    for (const days of daysOptions) {
      // Fetch most-h data
      const responseH = await fetch(`/${baseUrl}/json/pitching-most-h-last-${days}-games.json`)
      dataMapH.value[days] = await responseH.json()
      const responseHR = await fetch(`/${baseUrl}/json/pitching-most-hr-last-${days}-games.json`)
      dataMapHR.value[days] = await responseHR.json()
      const responseER = await fetch(`/${baseUrl}/json/pitching-most-er-last-${days}-games.json`)
      dataMapER.value[days] = await responseER.json()
      const responseK = await fetch(`/${baseUrl}/json/pitching-most-k-last-${days}-games.json`)
      dataMapK.value[days] = await responseK.json()
      const responseBB = await fetch(`/${baseUrl}/json/pitching-most-bb-last-${days}-games.json`)
      dataMapBB.value[days] = await responseBB.json()
      const responseIP = await fetch(`/${baseUrl}/json/pitching-most-ip-last-${days}-games.json`)
      dataMapIP.value[days] = await responseIP.json()
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
            <span class="days-label">Last {{ selectedDays }} start<span v-if="selectedDays > 1">s</span></span>
        </div>
        <h2>Top Starting Pitcher Hits Allowed</h2>
        <PlayerTable :data="dataMapH[selectedDays]" v-model:currentPage="pageH" :metric="'h'" :position="'p'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
        <h2>Top S.P. Strikeouts</h2>
        <PlayerTable :data="dataMapK[selectedDays]" v-model:currentPage="pageK" :metric="'k'" :position="'p'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
        <h2>Top S.P. Home Runs</h2>
        <PlayerTable :data="dataMapHR[selectedDays]" v-model:currentPage="pageHR" :metric="'hr'" :position="'p'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
        <h2>Top S.P. Walks</h2>
        <PlayerTable :data="dataMapBB[selectedDays]" v-model:currentPage="pageBB" :metric="'bb'" :position="'p'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
        <h2>Top S.P. ER Allowed</h2>
        <PlayerTable :data="dataMapER[selectedDays]" v-model:currentPage="pageER" :metric="'er'" :position="'p'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
        <h2>Top S.P. IP</h2>
        <PlayerTable :data="dataMapIP[selectedDays]" v-model:currentPage="pageIP" :metric="'ip'" :position="'p'" :selectedDays="selectedDays" @update:currentPage="currentPage = $event"/>
    </div>
</template>

<style scoped>

</style>
