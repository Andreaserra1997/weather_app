<script>
export default {
  props: ["favorites"],
  emits: ["removeFavorite"],
  computed: {
    sortedFavorites() {
      return [...this.favorites];
    },
  },
  methods: {
    sortFavorites() {
      this.favorites.sort((a, b) => a.temperature - b.temperature);
    },
  },
};
</script>

<template>
  <div class="d-flex justify-content-center py-4">
    <h2 class="px-4">Favorite Cities</h2>
    <button @click="sortFavorites" class="btn btn-outline-dark">
      Order by Temperature
    </button>
  </div>
  <ul class="d-flex justify-content-center flex-wrap">
    <li class="py-2" v-for="city in sortedFavorites" :key="city.name">
      <span class="px-4">{{ city.name }} - {{ city.temperature }}°C</span>
      <button
        @click="$emit('removeFavorite', city.name)"
        class="btn btn-outline-dark"
      >
        Remove
      </button>
    </li>
  </ul>
</template>

<style></style>
