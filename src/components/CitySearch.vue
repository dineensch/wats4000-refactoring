<template>
  <div>
    <h2>City Search</h2>
   
    <form v-on:submit.prevent="getCities">
        <p>Enter city name: <input type="text" v-model="query" placeholder="Paris, TX"> <button type="submit">Go</button></p>
    </form>
    
    <ul class="cities" v-if="results && results.list.length > 0">
        <li v-for="city in results.list" v-bind:key="city.id">
            <h2>{{ city.name }}, {{ city.sys.country }}</h2>
            <p><router-link v-bind:to="{ name: 'CurrentWeather', params: { cityId: city.id } }">View Current Weather</router-link></p>

            <weather-summary v-bind:weatherData="city.weather"></weather-summary>
            <weather-conditions v-bind:conditions="city.main"></weather-conditions>
        </li>
    </ul>

    <error-list v-bind:errorList="errors"></error-list>
  </div>
</template>

<script>
import {API} from '@/common/api';
import WeatherSummary from '@/components/WeatherSummary';
import WeatherConditions from '@/components/WeatherConditions';
import ErrorList from '@/components/ErrorList';

export default {
  name: 'CitySearch',
  data () {
    return {
      results: null,
      errors: [],
      query: ''
    }
  },
  methods: {
    getCities: function () {
      API.get('find', {
        params: {
            q: this.query,
        }
      })
      .then(response => {
        this.results = response.data
      })
      .catch(error => {
        this.errors.push(error)
      });
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
  width: 300px;
  min-height: 300px;
  border: solid 1px #e8e8e8;
  padding: 10px;
  margin: 5px;
}
a {
  color: #42b983;
}
</style>


