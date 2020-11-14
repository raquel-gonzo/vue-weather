<template>
  <div id="app">
    <main>
      <div class="search-box">
        <input
          v-model="query"
          type="text"
          class="search-bar"
          placeholder="Search ..."
        />
        <button @click.prevent="fetchWeather" class="btn-search">search</button>
      </div>

      <div class="weather-wrap" v-if="typeof weather.main !='undefined'">
        <div id="location">{{ weather.name }}, {{ weather.sys.country }}</div>
        <div id="temp">{{ weatherTemp }} &#176;F</div>
        <div id="description">{{ weather.weather[0].description }}</div>
      </div>
    </main>
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
      console.log(this.weather)
    },
  },
  computed: {
    weatherTemp() {
      if (this.weather.main.temp) {
        return this.weather.main.temp.toFixed(1);
      } 
      return "";
    }
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: 0;
}

.search-box,
.search-bar {
  display: inline;
  width: 90%;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  background-color: rgba(255, 255, 255, 0.5);
}

.btn-search {
  display: inline;
  font-size: 20px;
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

body {
  font-family: "montserrat", sans-serif;
}

#app {
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

main {
  min-height: 100vh;
  padding: 25px;
}
</style>
