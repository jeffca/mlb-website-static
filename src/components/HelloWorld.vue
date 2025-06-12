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
.layout {
  display: flex;
  justify-content: flex-start;
}

.layout > * {
  flex: 0 0 33.33%;  /* Each takes up 1/3 of the container */
  max-width: 33.33%;
  box-sizing: border-box;
}
</style>
