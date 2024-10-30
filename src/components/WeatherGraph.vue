<script>
import { Line } from "vue-chartjs";
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend,
} from "chart.js";

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend
);

export default {
  name: "LineChart",
  components: { Line },
  props: {
    weatherData: {
      type: Object,
      required: true,
    },
  },
  watch: {
    weatherData: {
      immediate: true,
      handler(newData) {
        this.updateChartData(newData);
      },
    },
  },
  data() {
    return {
      chartData: {
        labels: [],
        datasets: [
          {
            label: "Temperature over the next days",
            data: [],
            borderColor: "#25282a",
            fill: false,
          },
        ],
      },
      chartOptions: {
        responsive: true,
        scales: {
          x: {
            title: {
              display: true,
              text: "Time",
            },
            ticks: {
              maxTicksLimit: 12,
            },
          },
          y: {
            title: {
              display: true,
              text: "Temperature (°C)",
            },
          },
        },
      },
    };
  },
  methods: {
    formatTimeToHHMM(dateTime) {
      return dateTime.slice(11, 16);
    },
    updateChartData(newData) {
      if (newData.date24h && newData.temperature24h) {
        this.chartData.labels = newData.date24h.map(this.formatTimeToHHMM);
        this.chartData.datasets[0].data = newData.temperature24h;
      } else {
        this.chartData.labels = [];
        this.chartData.datasets[0].data = [];
      }
    },
  },
};
</script>

<template>
  <p v-if="!weatherData || !weatherData.temperature">No data to display</p>
  <Line v-else id="my-chart-id" :options="chartOptions" :data="chartData" />
</template>

<style>
#my-chart-id {
  width: 50%;
}
</style>
