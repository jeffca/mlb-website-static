<script setup>
import PlayerHistory from './PlayerHistory.vue'

import { computed, ref } from 'vue'

const props = defineProps({
  data: Object,
  currentPage: Number,
  metric: String,
  position: String
})

const emit = defineEmits(['update:currentPage'])

const showModal = ref(false)
const clickedValue = ref(null)

const rowsPerPage = 10

const paginatedData = computed(() => {
  if (!props.data) return []
  const start = (props.currentPage - 1) * rowsPerPage
  const end = start + rowsPerPage
  return props.data.slice(start, end)
})

const totalPages = computed(() => {
  if (!props.data) return 0
  return Math.ceil(props.data.length / rowsPerPage)
})

function nextPage() {
  if (props.currentPage < totalPages.value) emit('update:currentPage', props.currentPage + 1)
}

function prevPage() {
  if (props.currentPage > 1) emit('update:currentPage', props.currentPage - 1)
}

function openPlayerHistory(player_id) {
    console.log(player_id);
  clickedValue.value = player_id;
  showModal.value = true
}

</script>

<template>
  <div class="player-table-container">
    <table class="player-table">
      <thead>
        <tr>
            <th>Player</th>
            <th>Team</th>
            <th>vs.</th>
            <th>Streak</th>
            <th v-if="'ab' in paginatedData[0]">ab</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, index) in paginatedData" :key="index">
          <td @click="openPlayerHistory(row.player_id)">{{ row['name'] }}</td>
          <td>{{ row['team'] }}</td>
          <td>{{ row['opponent'] }}</td>
          <td>{{ row['streak'] }}</td>
          <td v-if="'ab' in paginatedData[index]">{{ row['ab'] }}</td>
        </tr>
      </tbody>
    </table>
    <div style="margin-top: 1rem;">
      <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
      <span style="margin: 0 1rem;">Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>
    <PlayerHistory 
      v-if="showModal" 
      :player_id="clickedValue"
      :selectedDays="'5'"
      :position="position"
      :metric="metric"
      @close="showModal = false"
    />
  </div>
</template>

<style scoped>
</style>
