<template>
  <div id="app">
    <!-- <div id="container"> -->
      <div class="search-box">
        <input
          v-model="query"
          type="text"
          class="search-bar"
          placeholder="Search ..."
        />
        <button :disabled="searchButtonDisabled" @click.prevent="fetchWeather" class="rainbow-button">search</button>
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div id="location">{{ weather.name }}, {{ weather.sys.country }}</div>
        <div id="temp">{{ weatherTemp }} &#176;F</div>
        <div id="description">{{ weather.weather[0].description }}</div>
        <img :src="weatherIcon" :alt="`weather icon for ${weather.weather[0].description}`"/>
      </div>
    <!-- </div> -->
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
    },
    searchButtonDisabled() {
      return this.query === "";
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

body {
  background-image: radial-gradient(circle, #6666b6, #736fbe, #7f78c6, #8c81ce, #988bd6, #9488d1, #9184cc, #8d81c7, #7a71b3, #6761a0, #54518d, #42427b);
  height: 100vh;
}

.search-box{
  padding: 8%;
}

button {
  border-radius: 5px;
  border-style: none;
  display:flex;
  align-items:center;
  justify-content:center;
  text-transform:uppercase;
  width: 125px;
  color: white;
  background-color: #302244;
  cursor: pointer;
  transition: border 0.3s;
  padding: 2px;
}

button:hover:enabled {
  border: 4px solid transparent;
  border-image: linear-gradient(to bottom right, #b827fc 0%, #2c90fc 25%, #b8fd33 50%, #fec837 75%, #fd1892 100%);
  border-image-slice: 1;
  transition: border 0.3s;
  padding: 2px;
}

.rainbow-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

</style>
