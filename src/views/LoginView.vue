<script setup>
import { onMounted, ref } from "vue";

const email = ref("");
const password = ref("");
const invalid = ref(false);

function login() {
  try {
    fetch("https://dev5-sneaker-api.onrender.com/api/v1/users/login", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
          email: email.value,
          password: password.value,
      }),
    })
      .then((response) => response.json())
      .then((data) => {
        if (data.status === "success") {
          localStorage.setItem("token", data.data[0].token);
          localStorage.setItem("userName", data.data[0].user.userName);
          localStorage.getItem("token");
          window.location.href = "/overview";
        } else {
          invalid.value = true;
        }
      });
  } catch (error) {
    console.log(error);
  }
    
}

onMounted(() => {
  if (localStorage.getItem("token")) {
    window.location.href = "/overview";
  }
});

</script>
<template>
  <div class="nav">
    <router-link to="/">Configurator</router-link>
  </div>
  <div class="flex">

    <div class="login">
        <h1>Log In</h1>
        <div class="user-details-div">
          <label for="email">E-mail</label>
          <input type="email" id="email" name="email" v-model="email"/>
        </div>
        
        <div class="user-details-div">
          <label for="password">Password</label>
          <input type="password" id="password" name="password" v-model="password"/>
        </div>
  
        <span v-if="invalid" class="error">Invalid credentials</span>
  
        <button class="login__button" type="submit" @click="login">Log In</button>
        <span>Don't have an account?</span>
        <router-link to="/register">Register</router-link>
    </div>
  </div>
</template>

<style scoped>
  html {
    background-color: #f0f0f0;
    color: rgb(8, 8, 8);
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
  h1 {
    text-align: center;
    color: rgb(8, 8, 8);
    margin-bottom: 5rem;
    margin-top: 0;
  }
  .login {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin: 0 auto;
    width: 100%;
    margin-bottom: 5rem;
    flex-grow: 1; 

  }
  .login__button {
    margin: 1rem;
  }
  .user-details-div {
    margin: 0.5rem;
  }
.error {
  color: red;
  font-weight: bold;
}
.flex {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

</style>
