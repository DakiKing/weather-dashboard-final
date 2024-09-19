<template>
  <li 
    class="favorite-item" 
    @click="selectLocation(location)" 
    :style="{ backgroundImage: `url(${backgroundImage})` }"
  >
    <div class="favorite-overlay"></div>
    <div class="favorite-details">
      <h3>{{ location }}</h3>
      <p>{{ temperature }}°C</p>
    </div>
  </li>
</template>

<script>
export default {
  props: ['location', 'backgroundImage', 'temperature'],
  methods: {
    selectLocation() {
      this.$emit('select', this.location);
    },
  },
};
</script>

<style scoped>
.favorite-item {
  position: relative;
  background-size: cover;
  background-position: center;
  color: white;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 5px;
  cursor: pointer;
}
.favorite-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.4);
  border-radius: 5px;
}
.favorite-details {
  position: relative;
  z-index: 1;
}
</style>
