<template>
    <div>
        <input type="text" v-model="searched" @keyup="$emit('crypto', searched)">
    </div>

    <div>
        <h1 v-for="(crypto, index) in cryptos" :key="index">{{ crypto.name }}</h1>
    </div>
</template>

<script>
import { ref } from 'vue';


    export default {
        props: {
            cryptos: Array
        },
        emits: {
            crypto
        },
        setup() {
            let searched = ref('');
            return {
                searched
            }
        }
    }

</script>