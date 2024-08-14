<script setup>
import { ref, computed } from "vue";

// 引入品項列表元件
import itemLists from "./components/itemLists.vue";
// 引入品項購物車元件
import itemCart from "./components/itemCart.vue";
// 引入訂單元件
import itemOrder from "./components/itemOrder.vue";

// 引入品項資料
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

// 方法: 處理項目被移除的事件
const handleItemRemoved = (removedId) => {
  cart.value = cart.value.filter((item) => item.id !== removedId);
};

// 方法: 重置購物車狀態，允許重新添加品項
const resetCart = () => {
  cart.value = [];
};
</script>

<template>
  <div class="container my-5">
    <div class="row">
      <itemLists :drinks="drinks" :addToCart="addToCart" :cart="cart" />
      <itemCart
        :cart="cart"
        :sum="sum"
        v-model:description="description"
        @update:cart="(newCart) => (cart.value = newCart)"
        @orderCreated="handleOrderCreated"
        @itemRemoved="handleItemRemoved"
        @resetCart="resetCart" />
    </div>
    <hr />
    <itemOrder :order="order" />
  </div>
</template>
