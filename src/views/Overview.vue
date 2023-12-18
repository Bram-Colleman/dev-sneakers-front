<script setup>
import { ref, onMounted } from "vue";

let userName = localStorage.getItem("userName");
const orders = ref();

function getOrders() {
  try {
    fetch("https://dev5-sneaker-api.onrender.com/api/v1/shoes", {
      method: "GET",
      headers: {
        "Content-Type": "application/json",
        Authorization: `Bearer ${localStorage.getItem("token")}`,
      },
    })
      .then((response) => response.json())
      .then((data) => {
        if (data.status === "success") {
          orders.value = data.data;
          console.log(orders.value);
        } else {
          console.log("error");
        }
      });
  } catch (error) {
    console.log(error);
  }
}

onMounted(() => {
  getOrders();
});
</script>

<template>
  <div class="nav">
    <router-link to="/">Configurator</router-link>
  </div>

  <h1 class="name">Hi, {{ userName }}</h1>
  <div class="overview__item">
    <h1>My Orders</h1>
    <div class="order_list">
      <ul class="orders">
        <li v-for="order in orders">
          <h4>{{ order.shoeType }}</h4>
          <!-- <span>{{ order.shoeColorLaces }}</span> -->
          <div class="colors">
            <div class="part">
              <span>Laces</span>
              <div v-if="order.shoeColorLaces == '#FFFFFF'" style="background-color: #ffffff" class="orderColor"></div>
              <div v-if="order.shoeColorLaces == '#000000'" style="background-color: black" class="orderColor"></div>
              <div v-if="order.shoeColorLaces == '#FE7F00'" style="background-color: #fe7f00" class="orderColor"></div>
              <div v-if="order.shoeColorLaces == '#69FF47'" style="background-color: #69ff47" class="orderColor"></div>
            </div>
            <div class="part">
              <span>Inside</span>
              <div v-if="order.shoeColorPanelDown == '#FFFFFF'" style="background-color: #ffffff" class="orderColor"></div>
              <div v-if="order.shoeColorPanelDown == '#000000'" style="background-color: black" class="orderColor"></div>
              <div v-if="order.shoeColorPanelDown == '#FE7F00'" style="background-color: #fe7f00" class="orderColor"></div>
              <div v-if="order.shoeColorPanelDown == '#69FF47'" style="background-color: #69ff47" class="orderColor"></div>
            </div>
            <div class="part">
              <span>Outside</span>
              <div v-if="order.shoeColorPanelUp == '#FFFFFF'" style="background-color: #ffffff" class="orderColor"></div>
              <div v-if="order.shoeColorPanelUp == '#000000'" style="background-color: black" class="orderColor"></div>
              <div v-if="order.shoeColorPanelUp == '#FE7F00'" style="background-color: #fe7f00" class="orderColor"></div>
              <div v-if="order.shoeColorPanelUp == '#69FF47'" style="background-color: #69ff47" class="orderColor"></div>
            </div>
            <div class="part">
              <span>Sole</span>
              <div v-if="order.shoeColorSole == '#FFFFFF'" style="background-color: #ffffff" class="orderColor"></div>
              <div v-if="order.shoeColorSole == '#000000'" style="background-color: black" class="orderColor"></div>
              <div v-if="order.shoeColorSole == '#FE7F00'" style="background-color: #fe7f00" class="orderColor"></div>
              <div v-if="order.shoeColorSole == '#69FF47'" style="background-color: #69ff47" class="orderColor"></div>
            </div>
          </div>
          <p>{{ order.status }}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.nav {
  display: flex;
  justify-content: flex-end;
  padding: 0 2rem;
  background-color: #69ff47;
}

.name {
  display: flex;
  justify-content: center;
}
a {
  color: black;
  text-decoration: none;
}
.overview__item {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: auto;
  margin-bottom: 5rem;
  flex-grow: 1;
}

.order_list {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 1.2rem;
  flex-grow: 1;
}

.orders {
  list-style-type: none;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: flex-start;
  padding: 0;
  margin: 0 10rem;
}
li {
  margin: 0 2.5rem;
}
.colors {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 0;
}
.orderColor {
  width: 1rem;
  height: 1rem;
}
.part {
  display: flex;
  align-items: center;
}
.part span {
  width: 4rem;
}
</style>
