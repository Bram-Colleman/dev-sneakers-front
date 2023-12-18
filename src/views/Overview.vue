<script setup>

let userName = localStorage.getItem("userName");
</script>

<template>
<h1>Hi {{ userName }}</h1>
</template>

<style scoped>
</style>