<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue';
import CryptoSelector from './components/CryptoSelector.vue';

let cryptos = ref([]);
let selected = ref('')
let cryptoPrice = ref('')

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

const selectCrypto = (crypto) => {
  
  axios.get(`https://api.coingecko.com/api/v3/simple/price?ids=${crypto.id}&vs_currencies=usd`).then(res => {
    
    const priceKey = Object.keys(res.data)[0]
    
    cryptoPrice.value = res.data[priceKey].usd

  });
  selected.value = crypto.name;
  cryptos.value = []
}

</script>

<template>
  <div>
    <h1 class="text-3xl font-bold text-center">
      Search Crypto
    </h1>
    <CryptoSelector v-on:crypto="getCryptos" v-on:selectCrypto="selectCrypto" :cryptos="cryptos"/>
    <div v-if="selected">
      <h1 class="text-1xl my-8 font-bold text-center">Crypto Selected: {{ selected }}</h1>
    </div>
  </div>

  
</template>

<style scoped>
/* Your styles here */
</style>
