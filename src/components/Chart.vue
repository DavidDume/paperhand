<template>
    <div class="w-3/4 m-auto">
        <canvas ref="chartCanvas"></canvas>
        <div>
            
        </div>
        <div>
            <h3>Select the timeframes you wished you bought</h3>
            <DatePicker range v-model="selectedTime" lang="en" @change="fetchHistoricalData(tokenId)"/>
        </div>
        
    </div>
</template>

<script>

import { ref, onMounted, watch } from 'vue';
import axios from 'axios';
import Chart from 'chart.js/auto';

import 'vue-datepicker-ui/lib/vuedatepickerui.css';
import VueDatepickerUi from 'vue-datepicker-ui';

export default {
    expose: ['fetchHistoricalData'],
    components: {
        DatePicker: VueDatepickerUi
    },
    props: {
        tokenId: String
    },
    setup(props) {

        let selectedTime = ref([new Date('2022-10-20'), new Date()])

        const chartCanvas = ref(null);
        const selectedTimeFrame = ref(0);
        const timeFrames = ['1d', '7d', '30d', '90d', '180d']; 
        let chart;

        watch(selectedTime, () => {
            fetchHistoricalData(props.tokenId)
        })

        const fetchHistoricalData = async (id) => {
            const unixTime = selectedTime.value.map(el => Math.floor(el.getTime() / 1000).toFixed(0))
            console.log(unixTime);
        try {
        
            const response = await axios.get(`https://api.coingecko.com/api/v3/coins/${id}/market_chart/range?vs_currency=usd&from=${unixTime[0]}&to=${unixTime[1]}`);

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
            fetchHistoricalData,
            selectedTime
        };
    },
    };
</script>