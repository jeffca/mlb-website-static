  <script setup>
  import { ref, onMounted, onBeforeUnmount } from 'vue'
  let history = ref([]);
  let loading = ref(true);

  const props = defineProps({
    player_id: Number,
    selectedDays: Number,
    metric: String,
    position: String
  })

  const emit = defineEmits(['close'])

  onMounted(async () => {
    try {
      console.log(props.selectedDays);
      console.log(props.metric);
      console.log(props.position);
      const baseUrl = window.location.pathname.split('/')[1] || ''
      let response;
      if (props.position == 'b') {
        if (props.metric == 'h') {
          response = await fetch(`/${baseUrl}/json/batter-history-h-last-${props.selectedDays}-days.json`)
        } else if (props.metric == 'k') {
          response = await fetch(`/${baseUrl}/json/batter-history-k-last-${props.selectedDays}-days.json`)
        } else if (props.metric == 'r') {
          response = await fetch(`/${baseUrl}/json/batter-history-r-last-${props.selectedDays}-days.json`)
        } else if (props.metric == 'rbi') {
          response = await fetch(`/${baseUrl}/json/batter-history-rbi-last-${props.selectedDays}-days.json`)
        }
      } else if (props.position == 'p') {
        if (props.metric == 'h') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-h-last-${props.selectedDays}-games.json`)
        } else if (props.metric == 'k') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-k-last-${props.selectedDays}-games.json`)
        } else if (props.metric == 'er') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-er-last-${props.selectedDays}-games.json`)
        } else if (props.metric == 'bb') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-bb-last-${props.selectedDays}-games.json`)
        }
      }
      let allPlayers = await response.json()
      for (let i = 0; i < allPlayers.length; i++) {
        if (allPlayers[i]["player_id"] == props.player_id) {
          history.value.push(allPlayers[i]) 
        }
      }
      console.log(history.value);
      
      loading.value = false;
    } catch (error) {
      console.error('Error fetching data:', error)
      loading.value = false;
    }
    window.addEventListener('keydown', handleKeyDown)
  })

  onBeforeUnmount(() => {
    window.removeEventListener('keydown', handleKeyDown)
  })

  function handleKeyDown(event) {
    if (event.key === 'Escape') {
      emit('close')
    }
  }

</script>

<template>
    <div class="modal" @click="$emit('close')">
      <div class="modal-content" @click.stop>
        <table>
          <thead>
            <tr>
              <th>Date</th>
              <th>Team</th>
              <th>Player</th>
              <th>{{metric}}</th>
              <th v-if="position=='b'">ab</th>
              <th v-else-if="position=='p'">ip</th>
              <th>vs.</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="b in history">
              <td>{{ b.date_est }}</td>
              <td>{{ b.team }}</td>
              <td>{{ b.name }}</td>
              <td>{{ b.metric }}</td>
              <td v-if="b.ab">{{ b.ab }}</td>
              <td v-else-if="b.ip">{{ b.ip }}</td>
              <td>{{ b.opponent }}</td>
            </tr>
          </tbody>
        </table>  
          <button @click="$emit('close')">Close</button>

      </div>
    </div>
</template>

<style scoped>

td {
  padding-left: 1.25em;
}
</style>
