<script setup>

const emit = defineEmits(['onClickRemove']);

defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number
})
</script>

<template>
  <div 
    class="flex border items-center border-slate-200 p-4 rounded-xl gap-4">
    <img class="w-16 h-16" :src="imageUrl" :alt="title" />

    <div class="flex flex-col flex-1">
      <p>{{title}}</p>
      <div class="flex justify-between">
        <p class="font-bold flex-1">{{price}}</p>
        <img
          @click="emit('onClickRemove')"
          class="cursor-pointer opacity-40 hover:opacity-100" 
          src="/close.svg" 
          alt="Delete" />
      </div>
    </div>
  </div>
</template>
