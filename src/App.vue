<template>
  <div id="app">
    <div class="app-title fade-in">
      <h1>☀️🌈 wonderful weather ☀️🌈</h1>
    </div>

    <div class="search-box fade-in">
      <input
        v-model="query"
        type="text"
        class="search-bar"
        placeholder="Search ..."
      />
      <button
        :disabled="searchButtonDisabled"
        @click.prevent="fetchWeather"
        class="rainbow-button"
      >
        search
      </button>
    </div>

    <div class="weather-wrap fade-in" v-if="typeof weather.main != 'undefined'">
      <div id="location">{{ weather.name }}, {{ weather.sys.country }}</div>
      <div id="temp">{{ weatherTemp }} &#176;F</div>
      <div id="description">{{ weather.weather[0].description }}</div>
      <img
        :src="weatherIcon"
        :alt="`weather icon for ${weather.weather[0].description}`"
      />
    </div>

    <div v-if="errorFound" class="weather-wrap fade-in">
      Whoops... I couldn't find that place. Try checking your spelling.
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      api_key: process.env.VUE_APP_API_KEY,
      url_base: "https://api.openweathermap.org/data/2.5",
      query: "",
      weather: {},
      errorFound: false,
    };
  },
  methods: {
    fetchWeather() {
      axios
        .get(
          `${this.url_base}/weather?q=${this.query}&units=imperial&appid=${this.api_key}`
        )
        .then((res) => {
          this.errorFound = false;
          this.setResults(res.data);
          return res.data;
        })
        .catch((error) => {
          this.errorFound = true;
          console.error(error);
        });
    },

    setResults(result) {
      this.weather = result;
    },
  },
  computed: {
    weatherTemp() {
      console.log(this.weather);
      if (this.weather.main.temp) {
        return this.weather.main.temp.toFixed();
      }
      return "";
    },
    weatherIcon() {
      return `http://openweathermap.org/img/wn/${this.weather.weather[0].icon}@2x.png`;
    },
    searchButtonDisabled() {
      return this.query === "";
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Fira+Code&display=swap");
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  align-content: center;
  font-family: "Fira Code", monospace;
}

body {
  background-color: black;
  height: 100vh;
}

input {
  width: 60%;
  font-size: medium;
  font-family: "Fira Code", monospace;
  padding-left: 16px;
}

.search-box {
  padding: 2%;
  width: 80%;
  display: flex;
  justify-content: center;
  flex-direction: row;
  height: 50px;
}

h1 {
  color: lavender;
}

.weather-wrap {
  background-color: lavender;
  width: 80%;
  margin: 0px auto;
  text-align: center;
  border: 4px solid transparent;
  border-image: linear-gradient(
    to bottom right,
    #b827fc 0%,
    #2c90fc 25%,
    #b8fd33 50%,
    #fec837 75%,
    #fd1892 100%
  );
  border-image-slice: 1;
  padding: 2%;
}

.fade-in {
  animation: fadeIn ease 0.5s;
  -webkit-animation: fadeIn ease 0.5s;
  -moz-animation: fadeIn ease 0.5s;
  -o-animation: fadeIn ease 0.5s;
  -ms-animation: fadeIn ease 0.5s;
}
@keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

@-moz-keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

@-webkit-keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

@-o-keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

@-ms-keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

button {
  margin-left: 15px;
  border-radius: 5px;
  border-style: none;
  display: flex;
  align-items: center;
  justify-content: center;
  text-transform: uppercase;
  width: 125px;
  color: white;
  background-color: #302244;
  cursor: pointer;
  transition: border 0.3s;
  padding: 2px;
  font-family: "Fira Code", monospace;
}

button:hover:enabled {
  border: 4px solid transparent;
  border-image: linear-gradient(
    to bottom right,
    #b827fc 0%,
    #2c90fc 25%,
    #b8fd33 50%,
    #fec837 75%,
    #fd1892 100%
  );
  border-image-slice: 1;
  transition: border 0.2s;
  padding: 2px;
}

button:focus {
  outline: 0;
}

.rainbow-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
