<script setup>
import Configurator from '@/components/Config.vue';
</script>

<template>
  <main>
    <div class="nav">
      <router-link to="/login">Login</router-link>
    </div>
    <Configurator></Configurator>
  </main>
</template>

<style scoped>
canvas {
  outline: 1px solid black;
}
.nav {
  display: flex;
  justify-content: flex-end;
  padding: 0 2rem;
}
a {
  color: black;
  text-decoration: none;
}
</style>
