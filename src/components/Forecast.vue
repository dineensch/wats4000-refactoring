<template>
  <div>
    <h2>Five Day Hourly Forecast <span v-if="weatherData"> for {{ weatherData.city.name }}, {{weatherData.city.country }}</span></h2>
    <p>
      <router-link to="/">Home</router-link> |
      <router-link v-bind:to="{ name: 'CurrentWeather', params: { cityId: $route.params.cityId } }">Current Weather <span v-if="weatherData"> for {{ weatherData.city.name }}, {{weatherData.city.country }}</span></router-link>
    </p>

    <ul v-if="weatherData && errors.length===0" class="forecast">
      <li v-for="forecast in weatherData.list" v-bind:key="forecast.id">
        <h3>{{ forecast.dt|formatDate }}</h3>

        <weather-summary v-bind:weatherData="forecast.weather"></weather-summary>
        <weather-conditions v-bind:conditions="forecast.main"></weather-conditions>

      </li>
    </ul>
   
    <div v-else>
      <h2>Loading...</h2>
    </div>
    
    <error-list v-bind:errorList="errors"></error-list>

  </div>
</template>

<script>
import {API} from '@/common/api';
import WeatherSummary from '@/components/WeatherSummary';
import WeatherConditions from '@/components/WeatherConditions';
import ErrorList from '@/components/ErrorList';

export default {
  name: 'Forecast',
  data () {
    return {
      weatherData: null,
      errors: [],
      query: ''
    }
  },
  created () {
    API.get('forecast', {
      params: {
          id: this.$route.params.cityId,
      }
    })
    .then(response => {
      this.weatherData = response.data
    })
    .catch(error => {
      this.errors.push(error)
    });
  },
  filters: {
    formatDate: function (timestamp){
      let date = new Date(timestamp * 1000);
      const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
      const weekdays = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
      let daynum = date.getDate();
      let month = date.getMonth();

      let hour = date.getHours();
      if (hour === 12) {
        hour = 'Noon';
      } else if (hour === 0) {
        hour = 'Midnight';
      } else if (hour > 12) {
        hour = hour - 12 + 'PM';
      } else if (hour < 12) {
        hour = hour + 'AM';
      }
      return `${ months[month] } ${ daynum } @ ${ hour }`;
    }
  },
components: {
  'weather-summary': WeatherSummary,
  'weather-conditions': WeatherConditions,
  'error-list': ErrorList
}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  width: 200px;
  min-height: 300px;
  border: solid 1px #e8e8e8;
  padding: 10px;
  margin: 5px;
}

a {
  color: #42b983;
}
</style>


