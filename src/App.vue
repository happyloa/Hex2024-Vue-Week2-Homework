<script setup>
import { ref, computed } from "vue";

// 引入品項列表元件
import itemLists from "./components/itemLists.vue";
// 引入品項購物車元件
import itemCart from "./components/itemCart.vue";

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

// 方法: 新增項目至購物車
const addToCart = (drink) => {
  cart.value.push({
    ...drink,
    id: new Date().getTime(),
    quantity: 1,
  });
};

// 方法: 接收訂單建立的事件
const handleOrderCreated = (newOrder) => {
  order.value = newOrder;
};
</script>

<template>
  <div class="container my-5">
    <div class="row">
      <itemLists :drinks="drinks" :addToCart="addToCart" />
      <itemCart
        :cart="cart"
        :sum="sum"
        v-model:description="description"
        @update:cart="(newCart) => (cart.value = newCart)"
        @orderCreated="handleOrderCreated" />
    </div>
    <hr />
    <div class="row justify-content-center">
      <div class="col-12">
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
                    <td>{{ item.price * item.quantity }}</td>
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
