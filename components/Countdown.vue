<template>
  <h1>Ronja Bday in: {{days}} Tagen, {{hours}} Stunden, {{minutes}} Minuten, {{seconds}} Sekunden</h1>
</template>
<script setup lang="ts">
import moment from "moment";
let rBday = moment('2023-04-26');
let duration = moment.duration(rBday.diff(moment()))
const days = ref(Math.floor(duration.asDays()))
const hours = ref(duration.hours() + 1)
const minutes = ref(duration.minutes())
const seconds = ref(duration.seconds())

setInterval(() => {
    duration = moment.duration(rBday.diff(moment()))
    days.value = Math.floor(duration.asDays())
    hours.value = duration.hours() + 1
    minutes.value = duration.minutes()
    seconds.value = duration.seconds()
}, 1000)
</script>
<script lang="ts">
export default {
    name: "Countdown"
}
</script>

<style scoped>

</style>