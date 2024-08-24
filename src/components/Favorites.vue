<template>
  <div class="favorites">
    <h2 @click="toggleFavorites">Favorites &#9662;</h2>
    <ul :class="{ hidden: hidden }">
      <favorite-item
        v-for="(location, index) in favorites"
        :key="index"
        :location="location"
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
    };
  },
  methods: {
    toggleFavorites() {
      this.hidden = !this.hidden;
    },
    selectLocation(location) {
      this.$emit('select', location);
    },
  },
};
</script>
