<script setup>
import { onMounted, ref, reactive, provide, watch, computed } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

const items = ref([]);
const cart = ref([]);

const drawerIsOpen = ref(false);

// const totalPrice = ref(0);

// const updateCartPrice = () => {
//   totalPrice.value = cart.value.reduce((acc, item) => acc + item.price, 0);
// }

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0));
const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100));

const openDrawer = () => {
  drawerIsOpen.value = true;
}

const closeDrawer = () => {
  drawerIsOpen.value = false;
}

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const addToCart = (item) => {
  item.isAdded = true;
  cart.value.push(item);
  // updateCartPrice();
}

const removeFromCart = (item) => {
 item.isAdded = false;
 cart.value.splice(cart.value.indexOf(item), 1);
//  updateCartPrice();
}

const onClickPlus = async (item) => {
  if(!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item);
  }
  console.log(cart.value)
}
const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeInput = (event) => {
  filters.searchQuery = event.target.value
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
  await fetchItems();
  await fetchFavorites();
})
watch(filters, fetchItems)

// provide('addToFavorite', addToFavorite);
provide('addToCart', addToCart);
provide('cart', {
  cart,
  openDrawer,
  closeDrawer,
  addToCart,
  removeFromCart
});

</script>

<template>
  <Drawer v-if="drawerIsOpen" :cart="cart" :total-price="totalPrice" :vatPrice="vatPrice"/>
  <div class="bg-white w-4/5 mx-auto rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer"/>

    <div class="p-10">
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
    </div>
  </div>
</template>

<style scoped></style>
