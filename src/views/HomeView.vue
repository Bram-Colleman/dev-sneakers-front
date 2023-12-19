<script setup>
import Configurator from '@/components/Config.vue';
import { ref, onMounted } from 'vue';
const loggedIn = ref(false);
onMounted(() => {
  if (localStorage.getItem('token')) {
    loggedIn.value = true;
  }
});
</script>

<template>
  <main>
    <div class="nav">
      <router-link to="/overview" v-if="loggedIn">Overview</router-link>
      <router-link to="/login" v-else>Login</router-link>
    </div>
    <img src="/media/logo.webp" alt="SWEAR" id="logo">
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
  background-color: #69FF47;
}
a {
  color: black;
  text-decoration: none;
}
</style>
