<template>
  <div>
    <h2>Five Day Hourly Forecast <span v-if="weatherData"> for {{ weatherData.city.name }}, {{weatherData.city.country }}</span></h2>
    <p>
      <router-link to="/">Home</router-link> |
      <router-link v-bind:to="{ name: 'CurrentWeather', params: { cityId: $route.params.cityId } }">Current Weather <span v-if="weatherData"> for {{ weatherData.city.name }}, {{weatherData.city.country }}</span></router-link>
    </p>

    <ul v-if="weatherData && errors.length===0" class="forecast">
      <li v-for="forecast in weatherData.list">
        <h3>{{ forecast.dt|formatDate }}</h3>
        <!-- TODO: Make weather summary be in a child component. -->
        <weather-summary v-bind:weatherData="forecast.weather"></weather-summary>
        <!-- TODO: Make dl of weather data be in a child component. -->
        <dl>
            <dt>Humidity</dt>
            <dd>{{ forecast.main.humidity }}%</dd>
            <dt>High</dt>
            <dd>{{ forecast.main.temp_max }}&deg;F</dd>
            <dt>Low</dt>
            <dd>{{ forecast.main.temp_min }}&deg;F</dd>
        </dl>
      </li>
    </ul>
    <div v-else-if="errors.length > 0">
      <h2>There was an error fetching weather data.</h2>
      <ul class="errors">
        <li v-for="error in errors">{{ error }}</li>
      </ul>
    </div>
    <div v-else>
      <h2>Loading...</h2>
    </div>
  </div>
</template>

<script>
import {API} from '@/common/api';

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
      //let weekday = date.getDay();
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
      //let year = date.getFullYear();
      return `${ months[month] } ${ daynum } @ ${ hour }`;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.errors li {
  color: red;
  border: solid red 1px;
  padding: 5px;
}
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
.weatherSummary {
  display: inline-block;
  width: 100px;
}
dl {
  padding: 5px;
  background: #e8e8e8;
}
dt {
  float: left;
  clear: left;
  width: 120px;
  text-align: right;
  font-weight: bold;
  color: blue;
}
dd {
  margin: 0 0 0 130px;
  padding: 0 0 0.5em 0;
}
dt::after {
  content: ":";
}
</style>


