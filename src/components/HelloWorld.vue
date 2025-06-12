<script setup>
import PitcherComponent from './PitcherComponent.vue'
import BatterComponent from './BatterComponent.vue'
import TeamComponent from './TeamComponent.vue'
import { ref, onMounted } from 'vue'

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
  <div class="layout">
    <PitcherComponent />
    <TeamComponent />
    <BatterComponent />
  </div>
</template>

<style scoped>

</style>
