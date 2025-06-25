<script setup>
import { ref, onMounted } from 'vue'
let schedule = ref([]);
let loading = ref(true);

onMounted(async () => {
  try {
    const baseUrl = window.location.pathname.split('/')[1] || ''
    const response = await fetch(`/${baseUrl}/json/schedule_export.json`)
    schedule.value = await response.json()
    loading.value = false;
  } catch (error) {
    console.error('Error fetching data:', error)
    loading.value = false;
  }
})

</script>

<template>
    <div class="center">
        <span v-if="schedule[0]" class="today">{{ schedule[0]["date_est"] }}</span>
    </div>
  <div class="scoreboard">
    <div v-for="s in schedule" class="game">
        <span class="teams">{{ s.away_team }} @ {{ s.home_team }}</span><br />
        <span class="time">{{ s.time_est }} EDT</span>
    </div>
  </div>
</template>

<style scoped>

</style>
