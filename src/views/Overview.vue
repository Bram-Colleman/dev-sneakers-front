<script setup>
import { ref, onMounted } from "vue";
import OrderCard from "@/components/OrderCard.vue";



let userName = localStorage.getItem("userName");
let userEmail = "";
let isAdmin = "";
let statusEdit = ref(false);
let statusSelected = ref("");
const orders = ref();

let socket = Primus.connect("http://localhost:3000/", {
  reconnect: {
    max: Infinity, // Number: The max delay before we try to reconnect.
    min: 500, // Number: The minimum delay before we try reconnect.
    retries: 10, // Number: How many times we should try to reconnect.
  },
});

socket.on("data", (data) => {
  if (data.action === "delete") {
    getOrders();
  }
  if (data.action === "create") {
    getOrders();
  }
});

function getInfo() {
  try {
  fetch("http://localhost:3000/api/v1/users", {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("token")}`,
        },
      })
        .then((response) => response.json())
        .then((data) => {
          if (data.status === "success") {
            userName = data.data[0].userName;
            userEmail = data.data[0].email;
            isAdmin = data.data[0].isAdmin;
          } else {
            console.error("Something went wrong!");
          }
        });
  } catch (error) {
    console.log(error);
  }
};

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
          orders.value = data.data.reverse();
        } else {
          console.log("error");
        }
      });
  } catch (error) {
    console.log(error);
  }
}

function deleteShoe(id) {
    try {
      fetch(`https://dev5-sneaker-api.onrender.com/api/v1/shoes/${id}`, {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("token")}`,
        },
      })
        .then((response) => response.json())
        .then((data) => {
          if (data.status === "success") {
            socket.write({
              "action": "delete",
            });
          } else {
            console.log("error");
          }
        });
    } catch (error) {
      console.log(error);
    }
  }


function toggleStatusEdit() {
  statusEdit.value = !statusEdit.value;
}
 function update() {
  getOrders();
 }

onMounted(() => {
  getInfo();
  getOrders();
});

let logout = () => {
    localStorage.removeItem("token");
    localStorage.removeItem("userName");
    window.location.href = "/";
    };

</script>

<template>
  <div class="nav">
    <router-link to="/">Configurator</router-link>
    <RouterLink class="logout" to="/" @click="logout">Logout</RouterLink>
  </div>

  <h1 class="name">Hi, {{ userName }}</h1>
  <div class="overview__item">
    <h1 v-if="isAdmin">All Orders</h1>
    <h1 v-else>My Orders</h1>
    <div class="order_list">
      <ul class="orders">
        <li v-for="(order, index) in orders" :key="order" :order="order" :index="index" :edit="false">
          <OrderCard :order="order" :admin="isAdmin" @deleteShoe="deleteShoe(order._id)" @updateShoe="update"></OrderCard>
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
.logout {
  margin-left: 1rem;
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
  justify-content: space-between;
  padding: 0;
  margin: 0 10rem;
  gap: 3rem;
}
li {
  margin: 0 2rem;
  display: flex;
  flex-direction: column;
  width: 15%;
  flex-grow: 0;
  flex-basis: 16%;

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
.status {
  display: flex;
  justify-content: space-between;
}
.status__edit {
  text-decoration: underline;
}
.status__select {
  display: flex;
}

@media (max-width: 950px) {
    li {
      margin: 0 1rem;
    }
    .orders {
      margin: 0;
      justify-content: center;
    }
  }
@media (max-width: 389px) {
    li {
      border-bottom: 1px solid rgb(8, 8, 8) ;
    }
  }
</style>
