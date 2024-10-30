<script>
import WeatherSearch from "./components/WeatherSearch.vue";
import FavoriteCities from "./components/FavoriteCities.vue";
import WeatherGraph from "./components/WeatherGraph.vue";
export default {
  components: {
    WeatherSearch,
    FavoriteCities,
    WeatherGraph,
  },
  data() {
    return {
      favorites: JSON.parse(localStorage.getItem("favorites")) || [],
      lat: null,
      lng: null,
      weatherData: {},
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
    updateWeatherData(newWeatherData) {
      this.weatherData = newWeatherData;
    },
  },
};
</script>

<template>
  <div class="container text-center">
    <h1 class="pt-5">Weather App</h1>
    <WeatherSearch
      @addFavorite="addFavorite"
      @updateCoordinates="updateCoordinates"
      @updateWeatherData="updateWeatherData"
    />
    <FavoriteCities :favorites="favorites" @removeFavorite="removeFavorite" />
    <WeatherGraph :weatherData="weatherData"></WeatherGraph>
  </div>
</template>

<style></style>
