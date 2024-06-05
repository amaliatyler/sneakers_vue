<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const favorites = ref([]);

onMounted(async () => {
    try {
        const { data } = await axios.get('https://1741fc408e0f10be.mokky.dev/favorites');
        console.log(data)
    } catch (error) {
        console.error(error)
    }
}) 

</script>

<template>
    <h1>Мои закладки</h1>
</template>