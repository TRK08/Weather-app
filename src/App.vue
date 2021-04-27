<template>
  <div class="main" :class="isDay ? 'day' : 'night'">
      <div class="app-wrapper">
      <h1 class="app-title">Погода</h1>
      <div class="city">
        <form class="city-search__form" @submit.prevent="getWeather">
          <input 
            type="text" 
            class="city-input" 
            placeholder="Введите название города"
            v-model="searchCity">
        </form>
        <span class="cityUndefind" v-if="isFound"> Город не найден</span>
        <span class="city-name"> {{ weather.city }} </span>
      </div>

      <div>
          <div class="anim-bg" icon="sunny" v-if="clearSky">
            <span class="sun"></span>
          </div>

          <div class="anim-bg" icon="snowy" v-if="snowy">
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>

          <div class="anim-bg" icon="stormy" v-if="stormy">
            <span class="cloud"></span>
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>
          <div class="anim-bg" icon="cloudy" v-if="cloudy">
            <span class="cloud"></span>
            <span class="cloud"></span>
          </div>
          <div class="anim-bg" icon="nightmoon" v-if="clearNight">
            <span class="moon"></span>
            <span class="meteor"></span>
          </div>
        </div>

      <div class="weather-wrap" v-if="isVisible">
        <div class="weather">
        <div class="weather-temperature">
          <div class="currentTemperature">
            <img class="icon" src="./assets/img/thermometer.svg" alt="">
             {{ weather.curTemp }}&deg;C
          </div>
          <div class="maxTemperature"> 
            {{weather.maxTemp}}&deg;C
            <img src="./assets/img/arrowUp.svg" alt="" class="icon">
          </div> 
          <div class="minTemperature"> 
            {{weather.minTemp}}&deg;C
            <img src="./assets/img/arrowUp.svg" alt="" class="icon">
          </div>
        </div>
        <div class="weather-info">
          <div class="description"> {{ weather.descr }} </div>
          <div class="feelsLike"> {{ weather.feelsLike }}&deg;C</div>
          <div class="humidity"> 
            <img class="icon" src="./assets/img/humidity.svg" alt="">
            {{ weather.humidity }}%</div>
        </div>
      </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        stormy: false,
        cloudy: false,
        clearSky: false,
        clearNight: false,
        snowy: false,

        isFound: false,
        isVisible: false,
        isDay: true,
        searchCity: '',

        weather: {
          city: '',
          curTemp: '',
          minTemp: '',
          maxTemp: '',
          descr: '',
          feelsLike: '',
          humidity: ''
        },
      }
    },
    methods: {
      getWeather: async function () {
        console.log(this.searchCity)
        const key = 'd7cdd45902766dd2c5141d271fe51adf';
        const baseURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.searchCity}&appid=${key}&units=metric`;
        
        // fetch weather
        try {
          const response = await fetch(baseURL);
          const data = await response.json();
          const timeOfDay = data.weather[0].icon;
          console.log(data);
          this.searchCity = '';
          this.weather.city = data.name;
          this.weather.curTemp = Math.round(data.main.temp);
          this.weather.minTemp = Math.round(data.main.temp_min);
          this.weather.maxTemp = Math.round(data.main.temp_max);
          this.weather.descr = data.weather[0].description;
          this.weather.feelsLike = Math.round(data.main.feels_like);
          this.weather.humidity = data.main.humidity;


          const mainWeather = data.weather[0].main;

        //check weather animations
        if (mainWeather.includes("Clouds")) {
          this.stormy = false;
          this.cloudy = true;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = false;
        }
        if (
          mainWeather.includes("Thunderstorm") ||
          mainWeather.includes("Rain")
        ) {
          this.stormy = true;
          this.cloudy = false;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = false;
        }
        if (mainWeather.includes("Clear") && this.isDay) {
          this.stormy = false;
          this.cloudy = false;
          this.clearSky = true;
          this.clearNight = false;
          this.snowy = false;
        }
        if (mainWeather.includes("Clouds") && !this.isDay) {
          this.stormy = false;
          this.cloudy = false;
          this.clearSky = false;
          this.clearNight = true;
          this.snowy = false;
        }
        if (mainWeather.includes("Snow")) {
          this.stormy = false;
          this.cloudy = false;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = true;
        }

          this.isFound = false;
          this.isVisible = true;

          if (timeOfDay.includes("n")) {
          this.isDay = false;
        } else {
          this.isDay = true;
        }
        } catch (error) {
          console.log(error);
          this.isFound = true;
          this.isVisible = false;
          this.weather.city = '';
        }

        

        
      }
    },
  }
</script>

<style>
@import url(./assets/css/animation.css);
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@100;400;700&display=swap');
@import url(./assets/css/style.css);
</style>

