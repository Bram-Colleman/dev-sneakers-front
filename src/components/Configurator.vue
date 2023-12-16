<script setup>
import { ref, onMounted } from 'vue';

const outside1 = ref(["red", "green", "blue", "purple", "yellow"]);
const outside2 = ref(["red", "green", "blue", "purple"]);
const laces = ref(["red", "green", "blue", "purple", "yellow"]);
const sole1 = ref(["red", "green", "purple", "yellow"]);
const sole2 = ref(["red", "green", "blue", "purple", "yellow"]);
const inside = ref(["red", "green", "blue", "purple", "yellow"]);
const categories = ref(["outside1", "outside2", "laces", "sole1", "sole2", "inside"]);
const categoriesFull = [outside1, outside2, laces, sole1, sole2, inside];
const current = ref(0);

const cycle = (factor) => {
    if (factor == 1) {
        if (current.value == categories.value.length - 1) {
            current.value = 0;
        } else {
            current.value++;
        }
    } else {
        if (current.value == 0) {
            current.value = categories.value.length - 1;
        } else {
            current.value--;
        }
    }
};


const c_outside1 = ref("");

// {
//     "brand": "nike",
//     "outside_1Color": "red",
//     "outside_2Color": "green",
//     "lacesColor": "blue",
//     "sole_1Color": "black",
//     "sole_2Color": "white",
//     "insideColor": "yellow",
//     "size": 8,
//     "price": 8,
//     "status": "delivery"

// }

</script>
<template>
    <div>
        <button @click="cycle(-1)">prev</button>
        <span>{{ categories[current] }}</span>
        <button @click="cycle(1)">next</button>
    </div>
    <div class="colors">
        <div v-for="c in categoriesFull[current].value">
            <input type="radio" name="c_outside1" :id=c v-model="c_outside1">
            <label :for=c >{{ c }}</label>
        </div>
    </div>

</template>
<style scoped>
.colors {
    display: flex;
}
</style>