<template>
  <div class="favorites">
    <h2 @click="toggleFavorites">Favorites &#9662;</h2>
    <ul :class="{ hidden: hidden }">
      <favorite-item
        v-for="(location, index) in favoriteWeatherData"
        :key="index"
        :location="location.name"
        :backgroundImage="location.backgroundImage"
        :temperature="location.temperature"
        @select="selectLocation"
      />
    </ul>
  </div>
</template>

<script>
import FavoriteItem from './FavoriteItem.vue';

export default {
  components: { FavoriteItem },
  props: ['favorites'],
  data() {
    return {
      hidden: true,
      favoriteWeatherData: [],
    };
  },
  watch: {
    favorites: {
      immediate: true,
      handler() {
        this.updateFavoriteWeatherData();
      },
    },
  },
  methods: {
    toggleFavorites() {
      this.hidden = !this.hidden;
    },
    selectLocation(location) {
      this.$emit('select', location);
    },
    updateFavoriteWeatherData() {
      this.favoriteWeatherData = [];
      this.favorites.forEach((location) => {
        const apiKey = '58e9cbe09caef5824249382dc08ff1e1';
        const apiBaseUrl = 'https://api.openweathermap.org/data/2.5/';

        fetch(`${apiBaseUrl}weather?q=${location}&appid=${apiKey}&units=metric`)
          .then((response) => response.json())
          .then((data) => {
            const weatherCondition = data.weather[0].main.toLowerCase();
            const backgroundImage = this.getBackgroundImage(weatherCondition);

            this.favoriteWeatherData.push({
              name: location,
              backgroundImage,
              temperature: data.main.temp,
            });
          })
          .catch((error) => console.error('Error fetching weather data:', error));
      });
    },
    getBackgroundImage(condition) {
      switch (condition) {
        case 'clear':
          return require('@/assets/sunny.jpg');
        case 'clouds':
          return require('@/assets/cloudy.jpg');
        case 'rain':
        case 'drizzle':
        case 'thunderstorm':
          return require('@/assets/rainy.jpg');
        case 'snow':
          return require('@/assets/snowy.jpg');
        default:
          return require('@/assets/default.jpg');
      }
    },
  },
};
</script>
