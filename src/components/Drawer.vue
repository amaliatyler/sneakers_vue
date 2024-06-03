<script setup>
import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'

const props = defineProps({
  cart: Array,
  totalPrice: Number,
  vatPrice: Number
})

const btnActive = props.cart.length <= 0;

</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white flex flex-col w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />

    <CartItemList />

    <div class="flex flex-col gap-4 mt-7">
      <div class="flex gap-3">
        <span>Итого:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <span class="font-bold">{{ totalPrice}} руб.</span>
      </div>
      <div class="flex gap-3">
        <span>Налог:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <span class="font-bold">{{ vatPrice}} руб.</span>
      </div>
      <button
        :disabled="btnActive"
        class="bg-[#9DD458] w-full rounded-xl py-3 mt-4 text-white transition disabled:bg-slate-300 disabled:cursor-not-allowed hover:bg-[#8bb950] active:bg-[#82a653] cursor-pointer"
      >
        Оформить заказ
      </button>
    </div>
  </div>
</template>
