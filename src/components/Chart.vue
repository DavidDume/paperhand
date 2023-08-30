<template>
    <div class="w-3/4 m-auto">
        <canvas ref="chartCanvas"></canvas>
        <input type="range" v-model="selectedTimeFrame" @input="fetchHistoricalData" :min="0" :max="timeFrames.length - 1">
    </div>
</template>

<script>

import { ref, onMounted } from 'vue';
import axios from 'axios';
import Chart from 'chart.js/auto';

export default {
    expose: ['fetchHistoricalData'],
    setup() {
    const chartCanvas = ref(null);
    const selectedTimeFrame = ref(0);
    const timeFrames = ['1d', '7d', '30d', '90d', '180d']; // Define your time frames
    let chart;

    const fetchHistoricalData = async (id) => {

      try {
       
        const response = await axios.get(`https://api.coingecko.com/api/v3/coins/${id}/market_chart`, {
          params: {
            vs_currency: 'usd',
            days: 'max',
            interval: 'daily'
          },
        });

        const data = response.data.prices;
        console.log(data);

        const labels = data.map(entry => new Date(entry[0]).toLocaleDateString());
        const values = data.map(entry => entry[1]);

        if (chart) {
          chart.data.labels = labels;
          chart.data.datasets[0].data = values;
          chart.update();
        } else {
          chart = new Chart(chartCanvas.value, {
            type: 'line',
            data: {
              labels,
              datasets: [
                {
                  label: 'Price',
                  data: values,
                  borderColor: 'blue',
                  fill: false,
                },
              ],
            },
            options: {
              // Configure your chart options
            },
          });
        }
      } catch (error) {
        console.error('Error fetching historical data:', error);
      }
    };

    return {
      chartCanvas,
      selectedTimeFrame,
      timeFrames,
      fetchHistoricalData
    };
  },
};
</script>