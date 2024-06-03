<script setup>

const emit = defineEmits(['openDrawer']);


</script>

<template>
  <header class="flex justify-between border-b border-slate-100 px-10 py-8">
    <div class="flex items-center gap-4">
      <a href="/"><img src="/logo.png" alt="Logo" class="w-10" /></a>
      <div>
        <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
        <p class="text-slate-400">Магазин лучших кроссовок</p>
      </div>
    </div>
    <ul class="flex items-center gap-10">
      <li
        @click="() => emit('openDrawer')"
      >
        <a 
          href="#" class="flex items-center gap-3 text-gray-500 hover:text-black">
          <img src="/cart.svg" alt="Cart" />
          <span class="font-bold">1205 руб.</span></a
        >
      </li>
      <li>
        <a href="#" class="flex items-center gap-3 text-gray-500 hover:text-black">
          <img src="/heart.svg" alt="Liked" />
          <span>Закладки</span>
        </a>
      </li>
      <li>
        <a href="#" class="flex items-center gap-3 text-gray-500 hover:text-black">
          <img src="/profile.svg" alt="Profile" />
          <span>Профиль</span>
        </a>
      </li>
    </ul>
  </header>
</template>
