<template>
    <div id="fiveDayForecast">
      <h3>5-Day Forecast</h3>
      <div class="forecast-container">
        <div v-for="(item, index) in forecastItems" :key="index" class="forecast-item">
          <p>{{ item.day }}</p>
          <p>{{ item.avgTemp }}°C</p>
          <img :src="`http://openweathermap.org/img/wn/${item.icon}@2x.png`" :alt="`Weather icon for ${item.day}`">
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: ['forecastData'],
    computed: {
      forecastItems() {
        const forecastByDay = {};
  
        this.forecastData.list.forEach(item => {
          const date = new Date(item.dt * 1000);
          const day = date.toLocaleDateString('en-US', { weekday: 'short' });
  
          if (!forecastByDay[day]) {
            forecastByDay[day] = {
              temps: [],
              icons: [],
            };
          }
  
          forecastByDay[day].temps.push(item.main.temp);
          forecastByDay[day].icons.push(item.weather[0].icon);
        });
  
        return Object.keys(forecastByDay).slice(0, 5).map(day => {
          const temps = forecastByDay[day].temps;
          const avgTemp = (temps.reduce((a, b) => a + b, 0) / temps.length).toFixed(1);
          const icon = forecastByDay[day].icons[Math.floor(forecastByDay[day].icons.length / 2)];
  
          return { day, avgTemp, icon };
        });
      },
    },
  };
  </script>
  
  <style scoped>
  .forecast-container {
    display: flex;
    justify-content: space-between;
  }
  
  .forecast-item {
    text-align: center;
  }
  </style>
  