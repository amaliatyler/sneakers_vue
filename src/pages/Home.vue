<script setup>

import axios from 'axios'
import { inject, reactive, watch, ref, onMounted } from 'vue'
import CardList from '../components/CardList.vue'

const { cart, addToCart, removeFromCart } = inject('cart')

const items = ref([]);

const filters = reactive({
    sortBy: 'title',
    searchQuery: ''
});



const onClickPlus = async (item) => {
  if(!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item);
  }
}
const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeInput = (event) => {
  filters.searchQuery = event.target.value
}

const addToFavorite = async (item) => {
  try {
    if(!item.isFavorite) {
      const obj = {
      parentId: item.id
    }

    item.isFavorite = true;
    const { data } = await axios.post('https://1741fc408e0f10be.mokky.dev/favorites', obj);
    item.favoriteId = data.id

    } else {
      item.isFavorite = false;
      await axios.delete(`https://1741fc408e0f10be.mokky.dev/favorites/${item.favoriteId}`);
      item.favoriteId = null;
    }
  } catch (error) {
    console.error(error)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://1741fc408e0f10be.mokky.dev/favorites')
    
    items.value = items.value.map(item => {
      const favorite = favorites.find(favorite => favorite.parentId === item.id);

      if(!favorite) {
        return item;
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }

    })
  } catch (err) {
    console.error(err)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy,
    }

    if(filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const { data } = await axios.get('https://1741fc408e0f10be.mokky.dev/items', {
      params
    })
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.error(err)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart');
  cart.value = localCart ? JSON.parse(localCart) : [];

  await fetchItems();
  await fetchFavorites();

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

// watch следит за изменением самого value, а не содержимого корзины
watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  })
  )
});

watch(filters, fetchItems);

</script>

<template>
    <div class="flex justify-between align-items">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

        <div class="flex flex-wrap gap-4 h-min">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По цене (по возрастанию)</option>
            <option value="-price">По цене (по убыванию)</option>
          </select>

          <div class="relative h-min">
            <img
              class="absolute left-4 top-[50%] -translate-y-[50%]"
              src="/search.svg"
              alt="Search"
            />
            <input
              @input="onChangeInput"
              class="border rounded-md py-2 pl-10 pr-4 outline-none focus:border-gray-400"
              type="text"
              placeholder="Поиск..."
            />
          </div>
        </div>
      </div>

      <div class="mt-10">
        <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickPlus"/>
      </div>
</template>