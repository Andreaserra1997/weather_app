<script>
import axios from "axios";
import { weatherDescriptions } from "../weatherCodes";
export default {
  data() {
    return {
      cityName: "",
      weather: null,
      apiKey: "e9c1358d0e78452e99125296856f49ac",
      errorMessage: "",
    };
  },
  emits: ["coordinates", "addFavorite", "updateCoordinates"],
  methods: {
    async fetchWeather() {
      try {
        const geoResponse = await axios.get(
          `https://api.opencagedata.com/geocode/v1/json?q=${this.cityName}&key=${this.apiKey}`
        );

        if (geoResponse.data.results.length === 0) {
          this.errorMessage = "Nessuna città trovata!";
          this.weather = null;
          return;
        }
        const { lat, lng } = geoResponse.data.results[0].geometry;

        const weatherResponse = await axios.get(
          `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lng}&current_weather=true&current=temperature_2m,wind_speed_10m&hourly=temperature_2m,relative_humidity_2m,wind_speed_10m`
        );
        this.weather = {
          name: this.cityName,
          temperature: weatherResponse.data.current_weather.temperature,
          conditions: weatherResponse.data.current_weather.weathercode,
          humidity: weatherResponse.data.hourly.relative_humidity_2m,
          windSpeed: weatherResponse.data.current_weather.windspeed,
        };
        this.averageHumidity = this.calculateAverageHumidity(
          this.weather.humidity
        );
        this.errorMessage = "";
      } catch (error) {
        this.errorMessage = "No cities found!";
        this.weather = null;
      }
    },
    calculateAverageHumidity(humidityArray) {
      if (humidityArray.length === 0) return 0;

      const total = humidityArray.reduce((sum, value) => sum + value, 0);
      const average = total / humidityArray.length;

      return Math.round(average);
    },
    getWeatherDescription(code) {
      return weatherDescriptions[code] || "No weather conditions found!";
    },
  },
};
</script>

<template>
  <div class="d-flex mx-auto" style="width: 40%">
    <input
      v-model="cityName"
      placeholder="Cerca una città"
      class="form-control me-2"
    />
    <button @click="fetchWeather" class="btn btn-outline-light">Search</button>
  </div>
  <div v-if="errorMessage" class="error-message">{{ errorMessage }}</div>
  <div v-if="weather">
    <div class="card mx-auto my-4" style="width: 18rem">
      <ul class="list-group list-group-flush">
        <li class="list-group-item">
          <font-awesome-icon :icon="['fas', 'temperature-three-quarters']" />
          {{ weather.temperature }}°C
        </li>
        <li class="list-group-item">
          <font-awesome-icon :icon="['fas', 'cloud-sun-rain']" />
          {{ getWeatherDescription(weather.conditions) }}
        </li>
        <li class="list-group-item">
          <font-awesome-icon :icon="['fas', 'droplet']" />
          {{ averageHumidity }}%
        </li>
        <li class="list-group-item">
          <font-awesome-icon :icon="['fas', 'wind']" />
          {{ weather.windSpeed }} km/h
        </li>
      </ul>
    </div>
    <button
      @click="$emit('addFavorite', weather)"
      class="btn btn-outline-light"
    >
      Add to favorites
    </button>
  </div>
</template>

<style>
li {
  list-style: none;
}
</style>
