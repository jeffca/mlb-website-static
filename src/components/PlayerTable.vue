<script setup>
import PlayerHistory from './PlayerHistory.vue'

import { computed, ref } from 'vue'

const props = defineProps({
  data: Object,
  currentPage: Number,
  metric: String,
  selectedDays: Number,
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
  clickedValue.value = player_id;
  showModal.value = true
}
</script>

<template>
  <div class="player-table-container">
    <table class="player-table">
      <thead>
        <tr>
            <th v-if="paginatedData.length > 0 && 'name' in paginatedData[0]">Player</th>
            <th>Team</th>
            <th>vs.</th>
            <th v-if="paginatedData.length > 0 && 'count' in paginatedData[0]">{{metric}}</th>
            <th v-if="paginatedData.length > 0 && 'avg' in paginatedData[0]">avg</th>
            <th v-if="paginatedData.length > 0 && 'ip' in paginatedData[0] && metric != 'ip'">ip</th>
            <th v-else-if="paginatedData.length > 0 && 'ab' in paginatedData[0]">ab</th>
            <th v-else-if="metric == 'ip'">games</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, index) in paginatedData" :key="index">
          <td v-if="row.name">
            <span @click="openPlayerHistory(row.player_id)"
              style="cursor: pointer; text-decoration: underline;">{{ row.name }}</span>
          </td>
          <td>
            <span v-if="position=='team'" @click="openPlayerHistory(row.id)"
            style="cursor: pointer; text-decoration: underline;">
              {{ row.team }}
            </span>
            <span v-else>
              {{ row.team }}
            </span>
          </td>
          <td>{{ row.opponent }}</td>
          <td v-if="row.count">{{ row.count }}</td>
          <td v-if="row.avg">{{ row.avg.toFixed(3) }}</td>
          <td v-if="row.ip && metric!='ip'">{{ row.ip.toFixed(1) }}</td>
          <td v-else-if="row.ab">{{ row.ab }}</td>
          <td v-else-if="metric=='ip'">{{ selectedDays }}</td>
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
      :selectedDays="selectedDays"
      :metric="metric"
      :position="position"
      @close="showModal = false"
    />
  </div>
</template>

<style scoped>
</style>
