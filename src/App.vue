<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

const items = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get('https://1741fc408e0f10be.mokky.dev/items')
    items.value = data
    console.log(data)
  } catch (err) {
    console.error(err)
  }
})
</script>

<template>
  <!-- <Drawer /> -->
  <div class="bg-white w-4/5 mx-auto rounded-xl shadow-xl mt-14">
    <Header />

    <div class="p-10">
      <div class="flex justify-between align-items">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

        <div class="flex flex-wrap gap-4 h-min">
          <select class="py-2 px-3 border rounded-md outline-none">
            <option>По названию</option>
            <option>По цене (по возрастанию)</option>
            <option>По цене (по убыванию)</option>
          </select>

          <div class="relative h-min">
            <img
              class="absolute left-4 top-[50%] -translate-y-[50%]"
              src="/search.svg"
              alt="Search"
            />
            <input
              class="border rounded-md py-2 pl-10 pr-4 outline-none focus:border-gray-400"
              type="text"
              placeholder="Поиск..."
            />
          </div>
        </div>
      </div>

      <div class="mt-10">
        <CardList :items="items" />
      </div>
    </div>
  </div>
</template>

<style scoped></style>
