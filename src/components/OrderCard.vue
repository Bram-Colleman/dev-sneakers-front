<script setup>
import { ref, onMounted } from "vue";

const props = defineProps({
    order: Object,
    admin: Boolean
});
const emits = defineEmits(['deleteShoe']);

const isEdit = ref(false);
const statusSelected = ref("");


function updateStatusOrder(id) {
    try {
      fetch(`http://localhost:3000/api/v1/shoes/${id}`, {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("token")}`,
        },
        body: JSON.stringify({
          status: statusSelected.value,
        }),
      })
        .then((response) => response.json())
        .then((data) => {
          if (data.status === "success") {
            props.order.status = statusSelected.value;
            isEdit.value = false;
          } else {
            console.log(data);
          }
        });
    } catch (error) {
      console.log(error);
    }
}

onMounted(() => {
    statusSelected.value = props.order.status;
});
</script>
<template>
    <div class="card">
        <div class="card__header">
            <h2 class="order__title"><strong>{{ order.shoeType }}</strong></h2>          
            <span class="status__edit" @click="isEdit = !isEdit" v-if="admin && !isEdit">
                <img src="https://cdn3.iconfinder.com/data/icons/feather-5/24/edit-512.png" alt="">
            </span>
            <span class="status__edit" @click="isEdit = !isEdit" v-if="admin && isEdit">
                <img src="https://cdn-icons-png.flaticon.com/512/66/66847.png" alt="">
            </span>
        </div>

        <div class="colors">
            <div class="part">
              <span><strong>Inside</strong></span>
              <div v-if="order.shoeColorPanelDown == '#FFFFFF'" style="background-color: #ffffff" class="orderColor"></div>
              <div v-if="order.shoeColorPanelDown == '#000000'" style="background-color: black" class="orderColor"></div>
              <div v-if="order.shoeColorPanelDown == '#FE7F00'" style="background-color: #fe7f00" class="orderColor"></div>
              <div v-if="order.shoeColorPanelDown == '#69FF47'" style="background-color: #69ff47" class="orderColor"></div>
            </div>
            <div class="part">
              <span><strong>Outside</strong></span>
              <div v-if="order.shoeColorPanelUp == '#FFFFFF'" style="background-color: #ffffff" class="orderColor"></div>
              <div v-if="order.shoeColorPanelUp == '#000000'" style="background-color: black" class="orderColor"></div>
              <div v-if="order.shoeColorPanelUp == '#FE7F00'" style="background-color: #fe7f00" class="orderColor"></div>
              <div v-if="order.shoeColorPanelUp == '#69FF47'" style="background-color: #69ff47" class="orderColor"></div>
            </div>
            <div class="part">
              <span><strong>laces</strong></span>
              <div v-if="order.shoeColorLaces == '#FFFFFF'" style="background-color: #ffffff" class="orderColor"></div>
              <div v-if="order.shoeColorLaces == '#000000'" style="background-color: black" class="orderColor"></div>
              <div v-if="order.shoeColorLaces == '#FE7F00'" style="background-color: #fe7f00" class="orderColor"></div>
              <div v-if="order.shoeColorLaces == '#69FF47'" style="background-color: #69ff47" class="orderColor"></div>
            </div>
            <div class="part">
              <span><strong>Sole</strong></span>
              <div v-if="order.shoeColorSole == '#FFFFFF'" style="background-color: #ffffff" class="orderColor"></div>
              <div v-if="order.shoeColorSole == '#000000'" style="background-color: black" class="orderColor"></div>
              <div v-if="order.shoeColorSole == '#FE7F00'" style="background-color: #fe7f00" class="orderColor"></div>
              <div v-if="order.shoeColorSole == '#69FF47'" style="background-color: #69ff47" class="orderColor"></div>
            </div>
          </div>
          <span class="part"><strong>Jewel: </strong>{{order.jewel}}</span>
          <span class="part" v-if="order.initials"><strong>Initials: </strong>{{order.initials}}</span>
          <span class="part" v-if="admin"><strong>User: </strong>{{order.userName}}</span>
          <!-- <span class="part" v-if="admin"><strong>E-mail: </strong>{{order.userEmail}}</span> -->
          <span class="part"><strong>Address: </strong>{{order.userAddress}}</span>
          <div class="order__status">
              <span><strong>Status: </strong></span>
              <span v-if="!isEdit">{{order.status}}</span>
              <div v-if="isEdit">
                <select name="" id="" class="status__select" v-model="statusSelected">
                    <option value="Order placed">Order placed</option>
                    <option value="In progress">In progress</option>
                    <option value="Sent">Sent</option>
                </select>
            </div>
        </div>
        <button @click="updateStatusOrder(order._id)" v-if="isEdit" class="button button--confirm">Confirm</button>
        <button @click="$emit('deleteShoe')" v-if="isEdit" class="button button--delete">Delete</button>

    </div>
</template>
<style scoped>
.card {
    display: flex;
    flex-flow: column;
}
.card__header {
    display: flex;
    justify-content: space-between;
    align-items: center;
}
.card__header img {
    width: 1rem;
    height: 1rem;
}
.order__title {
    font-size: 1.5rem;
    margin:.5rem 0;
}
.part {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-basis: 100%;
    flex-wrap: wrap;
}
.orderColor {
    display: flex;
    flex-direction: row;
    width: 10px;
    height: 10px;
}
.order__status {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    flex-basis: 100%;
    flex-wrap: wrap;
    margin-bottom: 2rem;
}
select {
    border: none;
    background-color: #f6f6f6;
    border-bottom: 1px solid rgb(207, 207, 207) !important;
}
select:hover {
    border: none;
    background-color: #f6f6f6;
    border-bottom: 1px solid rgb(8, 8, 8) !important;
}
.button--delete, .button--confirm {
    font-size: 1rem;
    height: unset;
    padding: .25rem 1rem;
    margin: .25rem 0;
}
.button--delete {
    background-color: #ff0000;
    border-color: #ff0000;
}
.button--delete:hover {
    background-color: #f6f6f6;
    border-color: #ff0000;
    color: #ff0000;
}
</style>