<script setup>
import { computed } from 'vue'

const props = defineProps({
  data: Object,
  currentPage: Number
})

const emit = defineEmits(['update:currentPage'])

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
</script>

<template>
  <div>
    <table>
      <thead>
        <tr>
            <th v-if="paginatedData.length > 0 && 'name' in paginatedData[0]">Player</th>
            <th>Team</th>
            <th>vs.</th>
            <!-- <th>stat</th> -->
            <th v-if="paginatedData.length > 0 && 'count' in paginatedData[0]">count</th>
            <th v-if="paginatedData.length > 0 && 'avg' in paginatedData[0]">avg</th>
            <th v-if="paginatedData.length > 0 && 'ip' in paginatedData[0]">ip</th>
            <th v-else-if="paginatedData.length > 0 && 'ab' in paginatedData[0]">ab</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, index) in paginatedData" :key="index">
          <td v-if="row.name">{{ row.name }}</td>
          <td>{{ row.team }}</td>
          <td>{{ row.opponent }}</td>
          <!-- <td>{{ row.metric }}</td> -->
          <td v-if="row.count">{{ row.count }}</td>
          <td v-if="row.avg">{{ row.avg }}</td>
          <td v-if="row.ip">{{ row.ip.toFixed(1) }}</td>
          <td v-else-if="row.ab">{{ row.ab }}</td>
        </tr>
      </tbody>
    </table>
    <div style="margin-top: 1rem;">
      <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
      <span style="margin: 0 1rem;">Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>
  </div>
</template>

<style scoped>
/* Add your styles here */
</style>
