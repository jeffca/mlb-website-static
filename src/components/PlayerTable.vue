<script setup>
const props = defineProps({
  data: Object
})
</script>

<template>
  <!-- <div>{{ props.data }}</div> -->

    <div>
        <table>
            <tbody>
                <tr v-for="row in data">
                    <td>{{ row.name }}</td>
                    <td>{{ row.team }}</td>
                    <td>{{ row.opponent }}</td>
                    <td>{{ row.metric }}</td>
                    <td>{{ row.count }}</td>
                    <td>{{ row.ip.toFixed(1) }}</td>&nbsp;
                </tr>
            </tbody>
        </table>
    </div>

</template>

<style scoped>
</style>