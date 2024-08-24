<template>
    <div :class="['weather-container', getBackgroundClass(weather.weather[0].main.toLowerCase())]">
      <div class="weather-overlay"></div>
      <div class="weather-content">
        <h2>
          {{ weather.name }}
          <button @click="toggleFavorite" :class="['favorite-button', isFavorite ? 'active' : '']">
            <i class="fa fa-heart"></i>
          </button>
        </h2>
        <p>{{ weather.weather[0].description }}</p>
        <p>Temperature: {{ weather.main.temp }}°C</p>
        <p>Humidity: {{ weather.main.humidity }}%</p>
        <p>Wind Speed: {{ weather.wind.speed }} m/s</p>
        <i :class="['fa', isDaytime ? 'fa-sun' : 'fa-moon']" style="font-size: 2em; color: yellow;"></i>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: ['weather'],
    computed: {
      isFavorite() {
        return JSON.parse(localStorage.getItem('favoriteLocations')).includes(this.weather.name);
      },
      isDaytime() {
        const timezoneOffset = this.weather.timezone;
        const localTime = new Date((Date.now() + timezoneOffset * 1000));
        const hours = localTime.getUTCHours();
        return hours >= 6 && hours < 18;
      },
    },
    methods: {
      toggleFavorite() {
        console.log('Weather data:', this.weather);
        this.$emit('toggle-favorite', this.weather.name);
      },
      getBackgroundClass(condition) {
        switch (condition) {
          case 'clear':
            return 'weather-sunny';
          case 'clouds':
            return 'weather-cloudy';
          case 'rain':
          case 'drizzle':
          case 'thunderstorm':
            return 'weather-rainy';
          case 'snow':
            return 'weather-snowy';
          default:
            return 'weather-default';
        }
      },
    },
  };
  </script>
  