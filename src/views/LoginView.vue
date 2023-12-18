<script setup>
</script>
<template>
  <div class="nav">
    <router-link to="/">Configurator</router-link>
  </div>
    <h1>log In </h1>

    <form class="login" action="#">
      <div class="user-details-div">
        <label for="username">Username</label>
        <input type="text" id="username" name="username" />
      </div>
      
      <div class="user-details-div">
        <label for="password">Password</label>
        <input type="password" id="password" name="password" />
      </div>
     
      <button class="login__button" type="submit" value="Login">Log In</button>
    </form>
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
  }
  a {
    color: black;
    text-decoration: none;
  }
  h1 {
    text-align: center;
    color: rgb(8, 8, 8);
  }
  .login {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin: 0 auto;
    width: 50%;
    height: 50%; 
  }

  .user-details-div {
    margin: 0.5rem;
  }

</style>
