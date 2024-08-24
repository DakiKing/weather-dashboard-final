<template>
    <div class="forecast">
      <div class="forecast-item" v-for="(item, index) in forecast.list.slice(0, 8)" :key="index">
        <p>{{ getHours(item.dt) }}:00</p>
        <p>{{ item.main.temp }}°C</p>
        <img :src="`http://openweathermap.org/img/wn/${item.weather[0].icon}@2x.png`" :alt="item.weather[0].description" />
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: ['forecast'],
    methods: {
      getHours(timestamp) {
        return new Date(timestamp * 1000).getHours();
      },
    },
  };
  </script>
  