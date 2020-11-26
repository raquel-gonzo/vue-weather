<template>
  <div id="app">
    <div class="app-title fade-in">
      <h1>☀️🌈 wonderful weather ☀️🌈</h1>
    </div>

    <div class="search-input">
      <span class="cs-parent" v-if="selectData === 'City, State'">
        <input
          class="cs-style fade-in"
          v-model="queryCity"
          type="text"
          placeholder="City"
        />
        <input
          class="cs-style fade-in"
          v-model="queryState"
          type="text"
          placeholder="State"
        />
      </span>

      <span v-else-if="selectData === 'US zip Code'">
        <input
          class="cs-style fade-in"
          v-model="queryZip"
          type="number"
          placeholder="Zip Code"
        />
      </span>

      <!-- todo: 
      - have default search be city and state.
      - add breakpoint responsive styling 
      -->

      <select v-model="selectData" class="select-style fade-in">
        <option disabled value="">Search by</option>
        <option value="City, State">City, State</option>
        <option value="US zip Code">US zip code</option>
      </select>

      <button
        :disabled="searchButtonDisabled"
        @click.prevent="fetchWeather"
        class="rainbow-button fade-in"
      >
        search
      </button>
    </div>

    <div class="weather-wrap fade-in" v-if="typeof weather.main != 'undefined'">
      <div id="location">
        <h2>{{ weather.name }}, {{ weather.sys.country }}</h2>
      </div>
      <div id="temp">
        <h3>
          {{ weatherTemp }}
          <span v-if="isMetric">&#176;C</span>
          <span v-else>&#176;F</span>
        </h3>
      </div>

      <div id="description">{{ weather.weather[0].description }}</div>

      <div>
        <img
          :src="weatherIcon"
          :alt="`weather icon for ${weather.weather[0].description}`"
        />
      </div>

      <div class="unit-switch">
        <label class="switch">
          <input v-model="isMetric" type="checkbox" />
          <span class="slider round"></span>
        </label>
        <p>Change units</p>
      </div>
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
      queryCity: "",
      queryState: "",
      queryZip: "",
      selectData: "",
      weather: {},
      errorFound: false,
      isMetric: false,
    };
  },
  methods: {
    fetchWeather() {
      // let isnum = /^\d{5}$/.test(this.query); // regexp for finding a 5-digit zip code
      let queryKind;
      if (this.selectData === "US zip Code" && this.queryZip !== "") {
        // if the user is searching for a US zip code
        queryKind = `${this.url_base}/weather?zip=${this.queryZip},us&units=imperial&appid=${this.api_key}`;
        console.log(queryKind);
      } else if (
        this.selectData === "City, State" &&
        this.queryCity !== "" &&
        this.queryState !== ""
      ) {
        // else the user is searching by city name
        queryKind = `${this.url_base}/weather?q=${this.queryCity},${this.queryState}&units=imperial&appid=${this.api_key}`;
      }
      axios
        .get(queryKind)
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
      if (this.weather.main.temp && !this.isMetric) {
        return this.weather.main.temp.toFixed(); // farenheit
      } else if (this.weather.main.temp && this.isMetric) {
        let celsius = ((this.weather.main.temp - 32) * (5 / 9)).toFixed(); // celsius
        return celsius;
      }
      return "";
    },
    weatherIcon() {
      return `http://openweathermap.org/img/wn/${this.weather.weather[0].icon}@2x.png`;
    },
    searchButtonDisabled() {
      if (this.selectData === "") {
        return true;
      }
      if (this.selectData === "US zip Code" && this.queryZip === "") {
        return true;
      }
      if (
        this.selectData === "City, State" &&
        (this.queryCity === "" || this.queryState === "")
      ) {
        return true;
      }
      return false;
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

.search-input {
  display: flex;
  flex-direction: row;
}

.cs-parent {
  display: flex;
  flex-direction: row;
}

.unit-switch {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-size: x-small;
}

.select-style {
  width: 125px;
  height: 50px;
  font-family: "Fira Code", monospace;
  border-radius: 5px;
  margin-right: 10px;
  text-align: center;
}

.cs-style {
  text-align: center;
  border-radius: 5px;
  padding: 0;
  padding-left: 4px;
  margin-right: 10px;
  border-width: 1px;
  width: 200px;
  height: 50px;
  font-family: "Fira Code", monospace;
}

body {
  background-color: black;
  height: 100vh;
}

h1 {
  color: lavender;
}

.weather-wrap {
  background-color: lavender;
  width: 60%;
  margin: 0px auto;
  margin-top: 15px;
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
  border-radius: 5px;
  border-style: none;
  display: flex;
  align-items: center;
  justify-content: center;
  text-transform: uppercase;
  width: 125px;
  height: 50px;
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

/* The switch - the box around the slider */
.switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
}

/* Hide default HTML checkbox */
.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

/* The slider */
.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  -webkit-transition: 0.4s;
  transition: 0.4s;
}

.slider:before {
  position: absolute;
  content: "";
  height: 26px;
  width: 26px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  -webkit-transition: 0.4s;
  transition: 0.4s;
}

input:checked + .slider {
  /* background-color: #2196F3; */
  background-color: #302244;
}

input:focus + .slider {
  box-shadow: 0 0 1px #2196f3;
}

input:checked + .slider:before {
  -webkit-transform: translateX(26px);
  -ms-transform: translateX(26px);
  transform: translateX(26px);
}

/* Rounded sliders */
.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
}

/*****************/
/* MEDIA QUERIES */
/*****************/

/* below 768px */
@media screen and (max-width: 500px) {
  .weather-wrap {
    width: 75%;
  }

  .search-input {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .cs-style {
    margin-bottom: 15px;
  }

  .cs-parent {
    flex-direction: column;
  }

  .select-style {
    margin-bottom: 15px;
    width: 200px;
  }

  h1 {
    font-size: large;
    margin-top: 20px;
  }
}
</style>
