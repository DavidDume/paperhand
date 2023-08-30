<template>
    <div class="flex justify-center items-center flex-col">
        <input type="text" v-model="searched" @keyup="$emit('crypto', searched)" class="border mt-5 w-56">
        <div class="border w-56 max-h-52 h-auto overflow-y-scroll">
            <h1 v-for="(crypto, index) in cryptos" :key="index" class="hover:cursor-pointer hover:bg-slate-300" @click="$emit('selectCrypto', crypto); clearInput()">{{ crypto.name }}</h1>
        </div>
        
    </div>

</template>

<script>
import { ref } from 'vue';


    export default {
        props: {
            cryptos: Array
        },
        emits: [
            'crypto',
            'selectCrypto'
        ],
        setup() {
            let searched = ref('');

            const clearInput = () => {
                searched.value = ''
            }

            return {
                searched,
                clearInput
            }
        }
    }

</script>