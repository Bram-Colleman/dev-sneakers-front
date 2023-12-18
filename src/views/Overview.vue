<script setup>

let userName = localStorage.getItem("userName");
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
            <li>
                <h4>Order 1</h4>
                <p>Order details</p>
                <p>status</p>
            </li>
            <li>
                <h4>Order 2</h4>
                <p>Order details</p>
                <p>status</p>
            </li>
            <li>
                <h4>Order 3</h4>
                <p>Order details</p>
                <p>status</p>
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
    background-color: #69FF47;
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
        justify-content: space-between;
        padding: 0;
    }

</style>