<script setup>
import { ref, provide, watch, computed } from 'vue'

import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'


/* Корзина - START*/
const cart = ref([]);
const drawerIsOpen = ref(false);

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0));
const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100));

const openDrawer = () => {
  drawerIsOpen.value = true;
}

const closeDrawer = () => {
  drawerIsOpen.value = false;
}

const addToCart = (item) => {
  item.isAdded = true;
  cart.value.push(item);
}

const removeFromCart = (item) => {
 item.isAdded = false;
 cart.value.splice(cart.value.indexOf(item), 1);
//  updateCartPrice();
}

watch(cart, () => {
  localStorage.setItem('cart', JSON.stringify(cart.value))
}, { deep: true})

provide('cart', {
  cart,
  openDrawer,
  closeDrawer,
  addToCart,
  removeFromCart
});

/* Корзина - END */

</script>

<template>
  <Drawer 
    v-if="drawerIsOpen" 
    :cart="cart" 
    :total-price="totalPrice" 
    :vat-price="vatPrice"  />
  <div class="bg-white w-4/5 mx-auto rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer"/>

    <div class="p-10">
      <RouterView />
    </div>
  </div>
</template>

<style scoped></style>
