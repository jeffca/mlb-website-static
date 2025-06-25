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

  let last3DaysAvg = ref(0)
  let last5DaysAvg = ref(0)
  let last10DaysAvg = ref(0)

  let showLast3Days = ref(false);
  let showLast5Days = ref(false);
  let showLast10Days = ref(false);

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
        } else if (props.metric == 'hr') {
          response = await fetch(`/${baseUrl}/json/batter-history-hr-last-${props.selectedDays}-days.json`)
        }
      } else if (props.position == 'p') {
        if (props.metric == 'h') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-h-last-${props.selectedDays}-games.json`)
        } else if (props.metric == 'k') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-k-last-${props.selectedDays}-games.json`)
        } else if (props.metric == 'hr') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-hr-last-${props.selectedDays}-games.json`)
        } else if (props.metric == 'er') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-er-last-${props.selectedDays}-games.json`)
        } else if (props.metric == 'bb') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-bb-last-${props.selectedDays}-games.json`)
        } else if (props.metric == 'ip') {
          response = await fetch(`/${baseUrl}/json/pitcher-history-ip-last-${props.selectedDays}-games.json`)
        }
      } else if (props.position == 'team') {
        if (props.metric == 'h') {
          response = await fetch(`/${baseUrl}/json/team-history-h-last-${props.selectedDays}-days.json`)
        } else if (props.metric == 'k') {
          response = await fetch(`/${baseUrl}/json/team-history-k-last-${props.selectedDays}-days.json`)
        } else if (props.metric == 'hr') {
          response = await fetch(`/${baseUrl}/json/team-history-hr-last-${props.selectedDays}-days.json`)
        } else if (props.metric == 'rbi') {
          response = await fetch(`/${baseUrl}/json/team-history-rbi-last-${props.selectedDays}-days.json`)
        } else if (props.metric == 'bb') {
          response = await fetch(`/${baseUrl}/json/team-history-bb-last-${props.selectedDays}-days.json`)
        }
      }
      let allPlayers = await response.json()


      let last3DaysSum = 0;
      let last5DaysSum = 0;
      let last10DaysSum = 0;

      let foundPlayer = false;
      let foundPlayerIndex;

      let playerHistoryCount = 0;


      for (let i = 0; i < allPlayers.length; i++) {
        if (props.position != 'team') {
          //when a user drills down to a pitcher or batter
          if (allPlayers[i]["player_id"] == props.player_id) {
            if (foundPlayer == false) {
              console.log("found the player for the first time...")
              foundPlayerIndex = i;
              foundPlayer = true;
            }
            history.value.push(allPlayers[i]);
            if (i < (foundPlayerIndex + 3)) {
              last3DaysSum += allPlayers[i]['metric']
            } 
            if (i < (foundPlayerIndex + 5)) {
              last5DaysSum += allPlayers[i]['metric']
            }
            if (i < (foundPlayerIndex + 10)) {
              last10DaysSum += allPlayers[i]['metric']
            }
            playerHistoryCount += 1;
          }
          if (foundPlayer == true && allPlayers[i]['player_id'] != props.player_id) {
            console.log("found a new player that isn't the target player...")
            foundPlayer = false;
          }
        } else {
          //when the user drill down into a team
          if (allPlayers[i]["team_id"] == props.player_id) {
            history.value.push(allPlayers[i]);
          }
        }

      }
      console.log(history);

      last3DaysAvg.value = (Math.round((last3DaysSum / Math.min(3,playerHistoryCount)) * 10) / 10).toFixed(1)
      last5DaysAvg.value = (Math.round((last5DaysSum / Math.min(5,playerHistoryCount)) * 10) / 10).toFixed(1)
      last10DaysAvg.value = (Math.round((last10DaysSum / Math.min(10, playerHistoryCount)) * 10) / 10).toFixed(1)
      
      if (playerHistoryCount >= 3) {
        showLast3Days.value = true;
      } 
      if (playerHistoryCount >= 5) {
        showLast5Days.value = true;
      }
      if (playerHistoryCount >= 10) {
        showLast10Days.value = true;
      }

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
        <div class="center" v-if="history[0]">
          <span class="player-name">{{ history[0]['name']}}</span>
        </div>
        <div class="kpi">
          <div class="kpi-box" v-if="showLast3Days">
            <span class="kpi-label">Last 3: </span>
            <span class="kpi-value">{{last3DaysAvg}} {{ metric }}</span>
          </div>
          <div class="kpi-box" v-if="showLast5Days">
            <span class="kpi-label">Last 5: </span>
            <span class="kpi-value">{{last5DaysAvg}} {{ metric }}</span>        
          </div>
          <div class="kpi-box" v-if="showLast10Days">
            <span class="kpi-label">Last 10: </span>
            <span class="kpi-value">{{last10DaysAvg}} {{ metric }}</span>        
          </div>
        </div>
        <table>
          <thead>
            <tr>
              <th>Date</th>
              <th>Team</th>
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
