<script setup>
import { ref, onMounted } from "vue";

const userName = ref("");
const userEmail = ref("");
const changePassword = ref(false);
const currentPassword = ref("");
const newPassword = ref("");
const confirmNewPassword = ref("");
const isChanged = ref(false);

function getInfo() {
  try {
  fetch("https://dev5-sneaker-api.onrender.com/api/v1/users", {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("token")}`,
        },
      })
        .then((response) => response.json())
        .then((data) => {
          if (data.status === "success") {
            userName.value = data.data[0].userName;
            userEmail.value = data.data[0].email;
          } else {
            console.error("Something went wrong!");
          }
        });
  } catch (error) {
    console.log(error);
  }
};

function updatePassword(cp, np, cnp) {
    if(newPassword.value !== confirmNewPassword.value) {
        console.log("Passwords don't match");
        return;
    }
  try {
  fetch("https://dev5-sneaker-api.onrender.com/api/v1/users", {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("token")}`,
        },
        body: JSON.stringify({
            password: currentPassword.value,
            newPassword: newPassword.value,
        })
      })
        .then((response) => response.json())
        .then((data) => {
          if (data.status === "success") {
            isChanged.value = true;
            changePassword.value = false;
          } else {
            document.querySelector("#cp").classList.add("wrong");
            console.error("Wrong password!");
          }
        });
  } catch (error) {
    console.log(error);
  }
};

onMounted(() => {
  if (!localStorage.getItem("token")) {
    window.location.href = "/login";
  } else {
    getInfo();
  }
});

let logout = () => {
  localStorage.removeItem("token");
  localStorage.removeItem("userName");
};

</script>
<template>
    <div class="nav">
        <router-link to="/">Configurator</router-link>
        <RouterLink class="logout" to="/overview" >Overview</RouterLink>
        <RouterLink class="logout" to="/" @click="logout">Logout</RouterLink>
    </div>
    <div class="container">
        <div class="card">
            <h1> Your profile </h1>
            <div class="div__details">
                <span><strong>Username</strong></span>
                <span>{{ userName }}</span>
            </div>
            <div class="div__details">
                <span><strong>E-mail</strong></span> 
                <span>{{ userEmail }}</span>
            </div>
            <span v-if="isChanged" class="change__confirm">Password changed!</span>
            <div class="user-details-div form__change" v-if="changePassword">
                <label for="cp">Current password</label>
                <input type="password" name="cp" id="cp" v-model="currentPassword">
                <label for="np">New password</label>
                <input type="password" name="np" id="np" v-model="newPassword">
                <label for="cnp">Confirm new password</label>
                <input type="password" name="np" id="cnp" v-model="confirmNewPassword">
            </div>
            <button @click="changePassword = !changePassword" v-if="!changePassword">change password</button>
            <button v-else @click="updatePassword">change password</button>
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
  margin-left: 3rem;
}
a {
  color: black;
  text-decoration: none;
}
.container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: calc(100vh - 1.5rem);
}
.card {
    display: flex;
    flex-direction: column;
}
h1 {
    text-align: center;
}
.div__details {
    display: flex;
    justify-content: space-between;
    margin: 10px;
    gap: 7.5rem;
}
.form__change {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    flex-basis: 100%;
    margin-top: 2rem;
}
.form__change input {
    width: 100%;
}
button {
    font-size: 1rem;
}
.wrong {
    border-bottom: 1px solid red !important;
    color: red;
}
.change__confirm {
    color: green;
    text-align: center;
    margin-top: 1rem;
    font-weight: bold;
}
</style>