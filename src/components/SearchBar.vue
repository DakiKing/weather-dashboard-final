<template>
    <div class="search">
      <input v-model="location" placeholder="Enter location" />
      <button @click="search">Search</button>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        location: '',
      };
    },
    methods: {
      search() {
        if (this.location) {
          this.$emit('search', this.location);
        }
      },
    },
  };
  </script>
  