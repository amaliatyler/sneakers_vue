<script setup>

defineProps({
  totalPrice: Number
})

const emit = defineEmits(['openDrawer']);


</script>

<template>
  <header class="flex justify-between border-b border-slate-100 px-10 py-8">
    
    <RouterLink to="/">
      <div class="flex items-center gap-4">
        <img src="/logo.png" alt="Logo" class="w-10" />
      <div>
        <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
        <p class="text-slate-400">Магазин лучших кроссовок</p>
      </div>
    </div>
    </RouterLink>
    <ul class="flex items-center gap-10">
      <li
        @click="() => emit('openDrawer')"
      >
        <a 
          href="#" class="flex items-center gap-3 text-gray-500 hover:text-black">
          <img src="/cart.svg" alt="Cart" />
          <span class="font-bold">{{ totalPrice }} руб.</span></a
        >
      </li>
      <RouterLink to="/favorites">
        <li class="flex items-center gap-3 text-gray-500 hover:text-black">
          <img src="/heart.svg" alt="Liked" />
          <span>Закладки</span>
        </li>
      </RouterLink>
      <li class="flex items-center gap-3 text-gray-500 hover:text-black">
        <a href="#" >
          <img src="/profile.svg" alt="Profile" />
          <span>Профиль</span>
        </a>
      </li>
    </ul>
  </header>
</template>
