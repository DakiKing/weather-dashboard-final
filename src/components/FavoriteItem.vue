<template>
    <li class="favorite-item" @click="selectLocation(location)">
      <div class="favorite-details">
        <h3>{{ location }}</h3>
      </div>
    </li>
  </template>
  
  <script>
  export default {
    props: ['location'],
    methods: {
      selectLocation() {
        this.$emit('select', this.location);
      },
    },
  };
  </script>
  