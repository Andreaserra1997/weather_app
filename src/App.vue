<script>
import WeatherSearch from "./components/WeatherSearch.vue";
import FavoriteCities from "./components/FavoriteCities.vue";
export default {
  components: {
    WeatherSearch,
    FavoriteCities,
  },
  data() {
    return {
      favorites: JSON.parse(localStorage.getItem("favorites")) || [],
      lat: null,
      lng: null,
    };
  },
  methods: {
    addFavorite(city) {
      if (!this.favorites.some((fav) => fav.name === city.name)) {
        this.favorites.push(city);
        this.saveFavorites();
      }
    },
    removeFavorite(cityName) {
      this.favorites = this.favorites.filter((fav) => fav.name !== cityName);
      this.saveFavorites();
    },
    saveFavorites() {
      localStorage.setItem("favorites", JSON.stringify(this.favorites));
    },
    updateCoordinates(lat, lng) {
      this.lat = lat;
      this.lng = lng;
    },
  },
};
</script>

<template>
  <div class="full-screen-image">
    <img src="../public/img_weather.jpg" alt="img" />
    <div class="container">
      <h1 class="pt-5">Weather App</h1>
      <WeatherSearch
        @addFavorite="addFavorite"
        @updateCoordinates="updateCoordinates"
      />
      <FavoriteCities :favorites="favorites" @removeFavorite="removeFavorite" />
    </div>
  </div>
</template>

<style>
.full-screen-image {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

img {
  position: absolute;
  top: 50%;
  left: 50%;
  min-width: 100%;
  min-height: 100%;
  width: auto;
  height: auto;
  transform: translate(-50%, -50%);
}

.container {
  position: relative;
  z-index: 1;
  color: white;
  text-align: center;
}
</style>
