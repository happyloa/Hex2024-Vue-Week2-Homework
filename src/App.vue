<script setup>
import { ref, computed } from "vue";

// 引入品項列表元件
import itemLists from "./components/itemLists.vue";

// 引入資料檔案
import { data } from "./data/items.js";

const drinks = ref(data);
const cart = ref([]);
const description = ref("");
const order = ref({});

const sum = computed(() => {
  return cart.value.reduce((pre, next) => {
    return pre + next.price * next.quantity;
  }, 0);
});

const addToCart = (drink) => {
  cart.value.push({
    ...drink,
    id: new Date().getTime(),
    quantity: 1,
  });
};

const updateCart = (item) => {
  cart.value = cart.value.map((cartItem) => {
    if (cartItem.id === item.id) {
      cartItem.quantity = parseInt(item.quantity);
    }
    return cartItem;
  });
};

const removeFromCart = (id) => {
  cart.value = cart.value.filter((cartItem) => cartItem.id !== id);
};

const createOrder = () => {
  order.value = {
    id: new Date().getTime(),
    cart: cart.value,
    description: description.value,
    sum: sum.value,
  };
  cart.value = [];
  description.value = "";
};

const itemSubtotal = (item) => {
  return item.price * item.quantity;
};
</script>

<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col-md-4">
        <itemLists :drinks="drinks" :addToCart="addToCart" />
      </div>
      <div class="col-md-8">
        <table class="table">
          <thead>
            <tr>
              <th scope="col" width="50">操作</th>
              <th scope="col">品項</th>
              <th scope="col">描述</th>
              <th scope="col" width="90">數量</th>
              <th scope="col">單價</th>
              <th scope="col">小計</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in cart" :key="item.id">
              <td>
                <button
                  type="button"
                  class="btn btn-sm"
                  @click="removeFromCart(item.id)">
                  x
                </button>
              </td>
              <td>{{ item.name }}</td>
              <td>
                <small>{{ item.description }}</small>
              </td>
              <td>
                <select
                  class="form-select"
                  v-model="item.quantity"
                  @change="updateCart(item)">
                  <option v-for="n in 10" :key="n" :value="n">{{ n }}</option>
                </select>
              </td>
              <td>{{ item.price }}</td>
              <td>{{ itemSubtotal(item) }}</td>
            </tr>
          </tbody>
        </table>
        <div
          v-if="cart.length === 0"
          class="alert alert-primary text-center"
          role="alert">
          請選擇商品
        </div>
        <div v-else>
          <div class="text-end mb-3">
            <h5>
              總計: <span>${{ sum }}</span>
            </h5>
          </div>
          <textarea
            class="form-control mb-3"
            rows="3"
            placeholder="備註"
            v-model="description"></textarea>
          <div class="text-end">
            <button class="btn btn-primary" @click.prevent="createOrder">
              送出
            </button>
          </div>
        </div>
      </div>
    </div>
    <hr />
    <div class="row justify-content-center">
      <div class="col-8">
        <div
          v-if="!order.id"
          class="alert alert-secondary text-center"
          role="alert">
          尚未建立訂單
        </div>
        <div v-else class="card">
          <div class="card-body">
            <div class="card-title">
              <h5>訂單</h5>
              <table class="table">
                <thead>
                  <tr>
                    <th scope="col">品項</th>
                    <th scope="col">數量</th>
                    <th scope="col">小計</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in order.cart" :key="item.id">
                    <td>{{ item.name }}</td>
                    <td>{{ item.quantity }}</td>
                    <td>{{ itemSubtotal(item) }}</td>
                  </tr>
                </tbody>
              </table>
              <div class="text-end">
                備註: <span>{{ order.description }}</span>
              </div>
              <div class="text-end">
                <h5>
                  總計: <span>${{ order.sum }}</span>
                </h5>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
