<template>
    <div class="container">
      <h1>Weather Dashboard</h1>
      <SearchBar @search="fetchWeather"/>
      <Favorites :favorites="favorites" @select="fetchWeather"/>
      <WeatherDisplay v-if="currentWeather" :weather="currentWeather" @toggle-favorite="toggleFavorite"/>
      <Forecast v-if="forecast" :forecast="forecast"/>
    </div>
  </template>
  
  <script>
  import SearchBar from './components/SearchBar.vue';
  import WeatherDisplay from './components/WeatherDisplay.vue';
  import Forecast from './components/Forecast.vue';
  import Favorites from './components/Favorites.vue';
  
  export default {
    components: {
      SearchBar,
      WeatherDisplay,
      Forecast,
      Favorites,
    },
    data() {
      return {
        currentWeather: null,
        forecast: null,
        favorites: JSON.parse(localStorage.getItem('favoriteLocations')) || [],
      };
    },
    created() {
    // Initialize favoriteLocations if it's not already present
    if (!localStorage.getItem('favoriteLocations')) {
      localStorage.setItem('favoriteLocations', JSON.stringify([]));
    }
  },
    methods: {
      fetchWeather(location) {
        const apiKey = '58e9cbe09caef5824249382dc08ff1e1';
        const apiBaseUrl = 'https://api.openweathermap.org/data/2.5/';
  
        fetch(`${apiBaseUrl}weather?q=${location}&appid=${apiKey}&units=metric`)
          .then(response => response.json())
          .then(data => {
            if (data.cod === 200) {
              this.currentWeather = data;
              this.fetchForecast(location);
            } else {
              alert('Location not found');
            }
          })
          .catch(error => console.error('Error fetching weather data:', error));
      },
      fetchForecast(location) {
        const apiKey = '58e9cbe09caef5824249382dc08ff1e1';
        const apiBaseUrl = 'https://api.openweathermap.org/data/2.5/';
  
        fetch(`${apiBaseUrl}forecast?q=${location}&appid=${apiKey}&units=metric`)
          .then(response => response.json())
          .then(data => {
            this.forecast = data;
          })
          .catch(error => console.error('Error fetching forecast data:', error));
      },
      toggleFavorite(location) {
        console.log('Weather data:', this.weather);
        if (this.favorites.includes(location)) {
          this.favorites = this.favorites.filter(fav => fav !== location);
        } else {
          this.favorites.push(location);
        }
        localStorage.setItem('favoriteLocations', JSON.stringify(this.favorites));
      }
    }
  }
  </script>
  