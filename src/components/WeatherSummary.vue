<template>
  <div>
    <div v-for="weatherSummary in weatherData" v-bind:key="weatherSummary.id" class="weatherSummary">
      <img v-bind:src="'http://openweathermap.org/img/w/' + weatherSummary.icon + '.png'" v-bind:alt="weatherSummary.main">
      <br>
      <b>{{ weatherSummary.main }}</b>
    </div>
  </div>
</template>

<script>
export default {
  name: 'WeatherSummary',
  data () {
    return {

    }
  },
  props: {
    weatherData: {}
  }
}
</script>

<style scoped>
  .weatherSummary {
    display: inline-block;
    width: 100px;
  }
</style>