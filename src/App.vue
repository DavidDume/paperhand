<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue';
import CryptoSelector from './components/CryptoSelector.vue';

let cryptos = ref([]);

const getCryptos = (searchCrypto) => {
  try {
    axios.get(`https://api.coingecko.com/api/v3/search?query=${searchCrypto}`).then(res => {
      cryptos.value = res.data.coins;
      console.log(res.data.coins);
    })
    
  } catch (error) {
    console.error('Error fetching crypto data:', error);
  }
};

</script>

<template>
  <div>
    <h1 class="text-3xl font-bold underline">
      Hello world!
    </h1>
    <CryptoSelector v-on:crypto="getCryptos" :cryptos="cryptos"/>
  </div>
</template>

<style scoped>
/* Your styles here */
</style>
