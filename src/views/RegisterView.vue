<script setup>
import { ref } from "vue";

const userName = ref("");
const email = ref("");
const password = ref("");
const invalid = ref(false);

function register() {
    console.log("reg");
  try {
    fetch("https://dev5-sneaker-api.onrender.com/api/v1/users/register", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
          userName: userName.value,
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

</script>
<template>
  <div class="nav">
    <router-link to="/">Configurator</router-link>
  </div>
  <div class="flex">

    <div class="register">
        <h1>Register</h1>
        <div class="user-details-div">
          <label for="userName">Username</label>
          <input type="text" id="userName" name="userName" v-model="userName"/>
        </div>
        <div class="user-details-div">
          <label for="email">E-mail</label>
          <input type="email" id="email" name="email" v-model="email"/>
        </div>
        <div class="user-details-div">
          <label for="password">Password</label>
          <input type="password" id="password" name="password" v-model="password"/>
        </div>
  
        <span v-if="invalid" class="error">Invalid credentials</span>
  
        <button class="login__button" type="submit" @click="register">Register</button>
        <span>Already have an account?</span>
        <router-link to="/login">Log in</router-link>
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
  .register {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin: 0 auto;
    width: 100%;
    height: 100%;
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
