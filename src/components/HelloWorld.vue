<script setup>
import PlayerTable from './PlayerTable.vue'
import { ref, onMounted } from 'vue'

const data = ref(null)
const loading = ref(true)

onMounted(async () => {
  try {
    const baseUrl = window.location.pathname.split('/')[1] || ''
    const response = await fetch(`/${baseUrl}/json/pitching-most-h-last-5-days.json`)
    data.value = await response.json()
    console.log(data.value);
    loading.value = false
  } catch (error) {
    console.error('Error fetching data:', error)
    loading.value = false
  }
})
</script>

<template>
  <div>
    <PlayerTable :data="data" />
  </div>
</template>

<style scoped>
</style>
