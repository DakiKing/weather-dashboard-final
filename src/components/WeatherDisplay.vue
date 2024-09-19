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
  props: ['weather', 'favorites'],
  computed: {
    isFavorite() {
      return this.favorites.includes(this.weather.name);
    },
    isDaytime() {
      const timezoneOffset = this.weather.timezone;
      const localTime = new Date(Date.now() + timezoneOffset * 1000);
      const hours = localTime.getHours(); 
      return hours >= 6 && hours < 18;
    },
  },
  methods: {
    toggleFavorite() {
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
    adjustBackgroundBrightness() {
      const currentTime = Date.now() / 1000; 
      const sunrise = this.weather.sys.sunrise;
      const sunset = this.weather.sys.sunset;

      if (currentTime >= sunrise && currentTime <= sunset) {
        document.body.style.backgroundColor = 'rgba(0, 0, 0, 0.8)'; 
      } else {
        document.body.style.backgroundColor = 'rgba(255, 255, 255, 0.8)'; 
      }
    },
    updateWeatherAnimation() {
      const weatherCondition = this.weather.weather[0].main.toLowerCase();
      const isDaytime = this.isDaytime;

      
      this.clearWeatherEffects();

      if (weatherCondition === 'rain') {
        this.addRainEffect();
      } else if (weatherCondition === 'snow') {
        this.addSnowEffect();
      }

      
      if (isDaytime) {
        document.body.classList.add('daytime');
      } else {
        document.body.classList.add('nighttime');
      }
    },
    clearWeatherEffects() {
      const rainContainer = document.querySelector('.rain-container');
      if (rainContainer) rainContainer.remove();
    },
    addRainEffect() {
      const rainContainer = document.createElement('div');
      rainContainer.classList.add('rain-container');
      
      for (let i = 0; i < 100; i++) {
        const raindrop = document.createElement('div');
        raindrop.classList.add('raindrop');
        raindrop.style.left = `${Math.random() * 100}vw`;
        raindrop.style.animationDuration = `${Math.random() * 0.5 + 1}s`;
        rainContainer.appendChild(raindrop);
      }
      
      document.body.appendChild(rainContainer);
    },
    addSnowEffect() {
      const snowContainer = document.createElement('div');
      snowContainer.classList.add('snow-container');

      for (let i = 0; i < 100; i++) {
        const snowflake = document.createElement('div');
        snowflake.classList.add('snowflake');
        snowflake.style.left = `${Math.random() * 100}vw`;
        snowflake.style.animationDuration = `${Math.random() * 1 + 2}s`;
        snowContainer.appendChild(snowflake);
      }

      document.body.appendChild(snowContainer);
    },
  },
  watch: {
    weather() {
      this.adjustBackgroundBrightness(); 
      this.updateWeatherAnimation();
    },
  },
};
</script>
