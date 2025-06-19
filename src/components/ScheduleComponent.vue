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

.today { 
  font-family:fantasy;
}

.scoreboard {
  display: flex;
  gap: 10px;
  padding: 10px;
  overflow-x: auto;
  white-space: nowrap;
  background: #f0f0f0;
}

.game {
  font-size: 8pt;
  background: #fff;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 12px;
  min-width: 180px;
  display: flex;
  flex-direction: column;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.game-box span {
  margin: 2px 0;
}

.teams {
  font-size: smaller;
  font-family: Helvetica;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  display: block; /* or inline-block */
  max-width: 100%; /* or a fixed width like 180px */
  font-weight: bold;
}

.time {
  font-size: larger;
  color: rgb(143, 143, 143);
}
</style>
