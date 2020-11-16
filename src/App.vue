<template>
  <div id="app">
    <div id="container">
      <div class="search-box">
        <input
          v-model="query"
          type="text"
          class="search-bar"
          placeholder="Search ..."
        />
        <button @click.prevent="fetchWeather" class="btn-search">search</button>
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div id="location">{{ weather.name }}, {{ weather.sys.country }}</div>
        <div id="temp">{{ weatherTemp }} &#176;F</div>
        <div id="description">{{ weather.weather[0].description }}</div>
        <img :src="weatherIcon" :alt="`weather icon for ${weather.weather[0].description}`"/>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api_key: process.env.VUE_APP_API_KEY,
      url_base: "https://api.openweathermap.org/data/2.5",
      query: "",
      weather: {},
    };
  },
  methods: {
    fetchWeather() {
      fetch(
        `${this.url_base}/weather?q=${this.query}&units=imperial&appid=${this.api_key}`
      )
        .then((res) => {
          return res.json();
        })
        .then(this.setResults);
    },

    setResults(results) {
      this.weather = results;
      console.log(this.weather);
    },
  },
  computed: {
    weatherTemp() {
      if (this.weather.main.temp) {
        return this.weather.main.temp.toFixed();
      }
      return "";
    },
    weatherIcon() {
     return `http://openweathermap.org/img/wn/${this.weather.weather[0].icon}@2x.png`
    }
  },
};
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  align-content: center;
}
</style>
