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

      <div id="location">Chicago, IL</div>
      <div id="date">Friday, November 13, 2020</div>
      <div id="temp">40 degrees F</div>
      <div id="weather">Light Rain</div>
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
      fetch(`${this.url_base}/weather?q=${this.query}&appid=${this.api_key}`)
        .then(res => res.json())
        .then(data => {
          console.log(data);
          this.weather = data;
        });
    },
  },
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
