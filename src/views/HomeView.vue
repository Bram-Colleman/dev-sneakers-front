<script setup>
import Configurator from '@/components/Config.vue';
</script>

<template>
  <main>
    <div class="nav">
      <router-link to="/login">Login</router-link>
    </div>
    <img src="/media/logo.webp" alt="SWEAR" id="logo">
    <Configurator></Configurator>
  </main>
</template>

<style scoped>
#logo {
  position: absolute;
  margin: 1rem 5rem;
  filter: invert(1);
}
canvas {
  outline: 1px solid black;
}
.nav {
  display: flex;
  justify-content: flex-end;
  padding: 0 2rem;
  background-color: #69FF47;
}
a {
  color: black;
  text-decoration: none;
}
</style>
