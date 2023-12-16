<script setup>
import { ref, onMounted } from 'vue';

const outside1 = ref(["red", "green", "blue", "purple", "yellow"]);

</script>
<template>
    <div>
        <button>test</button>
        <span>test</span>
        <button>test</button>
    </div>
    <div class="colors">
        <div v-for="c in outside1">
            <input type="radio" :name=c :id=c >
            <label :for=c >{{ c }}</label>
        </div>
    </div>

</template>
<style scoped>
.colors {
    display: flex;
}
</style>