<template>
    <div class="w-3/4 m-auto">
        <canvas ref="chartCanvas"></canvas>
        <div class="flex justify-between items-center">
            <div class="h-8">
                <h3>Select the timeframes you wished you bought</h3>
                <DatePicker range v-model="selectedTime" lang="en" @change="fetchHistoricalData(tokenId)"/>
            </div>
            <button @click="calcTotal">Calculate</button>
            <div class="h-8">
                <h3>Insert the quantity in $ you wish you bought</h3>
                <input type="text" class="border" v-model="quantity">
            </div>
        </div>
        <div class="text-center" v-if="currTotal">
            You would now have: {{ currTotal }}$
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
        tokenId: String,
        currPrice: Number
    },
    setup(props) {

        let selectedTime = ref([new Date('2022-10-20'), new Date()])

        const chartCanvas = ref(null);
        const medianPrice = ref(0)
        const quantity = ref(0)
        const currTotal = ref(0)
        let chart;

        watch(selectedTime, () => {
            fetchHistoricalData(props.tokenId)
        })

        const calcTotal = () => {
            currTotal.value = ((quantity.value / medianPrice.value) * props.currPrice).toFixed(2)
        }

        const calcMedianPrice = (arr) => {
            const total = arr.reduce((acc, curr) => acc + curr);
            return (total / arr.length).toFixed(2)
        }

        const fetchHistoricalData = async (id) => {
            const unixTime = selectedTime.value.map(el => Math.floor(el.getTime() / 1000).toFixed(0))
        try {
        
            const response = await axios.get(`https://api.coingecko.com/api/v3/coins/${id}/market_chart/range?vs_currency=usd&from=${unixTime[0]}&to=${unixTime[1]}`);

            const data = response.data.prices;
            //console.log(data);

            const labels = data.map(entry => new Date(entry[0]).toLocaleDateString());
            
            const values = data.map(entry => entry[1]);

            medianPrice.value = calcMedianPrice(values)

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
            fetchHistoricalData,
            selectedTime,
            medianPrice,
            calcTotal,
            currTotal,
            quantity
        };
    },
    };
</script>