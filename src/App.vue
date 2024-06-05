<script setup>
import { ref, provide, watch, computed } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'


/* Корзина - START*/
const cart = ref([]);
const isOrderPending = ref(false);

const drawerIsOpen = ref(false);

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0));
const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100));

const isCartEmpty = computed(() => cart.value.length === 0)

const cartButtonDisabled = computed(() => 
  isOrderPending.value || isCartEmpty.value
);

const openDrawer = () => {
  drawerIsOpen.value = true;
}

const closeDrawer = () => {
  drawerIsOpen.value = false;
}

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

const createOrder = async () => {
  try {
    isOrderPending.value = true;
    const { data } =  await axios.post(`https://1741fc408e0f10be.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: totalPrice.value
    });

    cart.value = [];
    console.log(cart.value)

    return data;

  } catch (error) {
    console.error(error);
  } finally {
    isOrderPending.value = false;
  }
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
    :vat-price="vatPrice" 
    @create-order="createOrder" 
    :button-disabled="cartButtonDisabled"/>
  <div class="bg-white w-4/5 mx-auto rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer"/>

    <div class="p-10">
      <RouterView />
    </div>
  </div>
</template>

<style scoped></style>
