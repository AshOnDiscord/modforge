<script setup lang="ts">
import { RouterLink } from 'vue-router';

</script>

<template>
  <main class="flex justify-center items-center h-screen flex-col gap-2">
    <h1 class="text-8xl text-emerald-500 font-semibold">Modforge</h1>
    <h2 class="text-4xl text-emerald-50">A Modrinth Wrapper</h2>
    <RouterLink class="no-underline text-2xl px-8 py-4 outline-2  outline outline-emerald-400 text-emerald-400 rounded-2xl mt-4 hover:bg-emerald-400 hover:text-white transition" to="/mods/">Mods</RouterLink>
  </main>
</template>
