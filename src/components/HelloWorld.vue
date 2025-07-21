<script setup>
import PitcherComponent from './PitcherComponent.vue'
import BatterComponent from './BatterComponent.vue'
import TeamComponent from './TeamComponent.vue'
import ScheduleComponent from './ScheduleComponent.vue'
import { ref, onMounted } from 'vue'

const tabs = ['All Stats', 'Pitchers', 'Team Batting', 'Batters']
const selectedTab = ref('')

const data = ref(null)
const loading = ref(true)

onMounted(async () => {
  try {
    loading.value = false
  } catch (error) {
    console.error('Error fetching data:', error)
    loading.value = false
  }
})
</script>

<template>
  <div class="schedule mb-4">
    <ScheduleComponent />
  </div>

  <div class="tabs justify-center gap-2 mb-6">
    <button
      v-for="tab in tabs"
      :key="tab"
      :class="['tab-button', { active: selectedTab === tab }]"
      @click="selectedTab = tab"
    >
      {{ tab }}
    </button>
  </div>

  <div :class="selectedTab === 'All' ? 'layout-all' : 'layout-centered'">
    <PitcherComponent v-if="selectedTab === 'Pitchers' || selectedTab === 'All Stats'" />
    <TeamComponent v-if="selectedTab === 'Team Batting' || selectedTab === 'All Stats'" />
    <BatterComponent v-if="selectedTab === 'Batters' || selectedTab === 'All Stats'" />
  </div>
</template>

<style scoped>

.tabs {
  text-align: center;
  margin-bottom: 1em;
}
.tabs button {
  font-size: 1rem;
  font-weight: 900;
  padding: 0.6em 1.2em;
  border-radius: 9999px;
  background-color: #4cb7f5e6;
  font-family: 'Arial Narrow Bold', sans-serif;
  color: #fafafa;
  border: none;
  cursor: pointer;
  margin-right: 1em;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
}

.tabs button:hover {
  background-color: #e5e7eb;
}

.tabs button.active {
  background-color: #111827;
  color: white;
  font-weight: 700;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  transform: scale(1.05);
  border-bottom: 1px solid #1209be;
}

/* Correct layout for "All" */
.layout-all {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
}

/* Center layout for individual tabs */
.layout-centered {
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

</style>
