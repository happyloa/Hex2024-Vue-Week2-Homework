<script setup>
// 從 Vue 引入 ref 和 computed 函數，用於管理響應式狀態和計算屬性
import { ref, computed } from "vue";

// 引入品項列表元件，這個元件負責展示所有可選購的飲品項目
import itemLists from "./components/itemLists.vue";
// 引入品項購物車元件，這個元件負責展示購物車中的項目及其相關內容
import itemCart from "./components/itemCart.vue";
// 引入訂單元件，這個元件負責展示已建立的訂單內容
import itemOrder from "./components/itemOrder.vue";

// 引入品項資料檔案，這些資料包含了可供選擇的飲品列表
import { data } from "./data/items.js";

// 使用 ref 創建一個響應式變數 drinks 來儲存所有飲品項目
const drinks = ref(data);
// 使用 ref 創建一個響應式變數 cart 來儲存購物車中的項目
const cart = ref([]);
// 使用 ref 創建一個響應式變數 description 來儲存訂單的備註
const description = ref("");
// 使用 ref 創建一個響應式變數 order 來儲存已建立的訂單訊息
const order = ref({});

// 使用 computed 創建一個計算屬性 sum，用來計算購物車中所有項目的總金額
const sum = computed(() => {
  // 使用 reduce 方法累加購物車中每個項目的價格乘以數量，返回總金額
  return cart.value.reduce((pre, next) => {
    return pre + next.price * next.quantity;
  }, 0);
});

// 方法: 新增項目至購物車
// 接收一個飲品項目作為參數，將該項目添加到購物車中，並設定初始數量為1
const addToCart = (drink) => {
  cart.value.push({
    ...drink, // 展開飲品項目的所有屬性
    id: new Date().getTime(), // 使用當前時間戳作為唯一 ID
    quantity: 1, // 設定初始購買數量為1
  });
};

// 方法: 接收訂單建立的事件
// 當新的訂單建立時，接收新訂單並更新 order 變數
const handleOrderCreated = (newOrder) => {
  order.value = newOrder;
};

// 方法: 處理項目被移除的事件
// 當某個項目從購物車中移除時，接收該項目的 ID 並從購物車中移除對應的項目
const handleItemRemoved = (removedId) => {
  cart.value = cart.value.filter((item) => item.id !== removedId);
};

// 方法: 重置購物車狀態，允許重新添加品項
// 清空購物車中的所有項目，允許用戶重新選擇飲品
const resetCart = () => {
  cart.value = [];
};
</script>

<template>
  <!-- 整個頁面布局的主容器，設定適當的外間距 -->
  <div class="container my-5">
    <!-- 使用 row 來建立一行布局 -->
    <div class="row">
      <!-- 引用品項列表元件，傳入 drinks（飲品列表）和 addToCart（新增至購物車的方法）作為屬性 -->
      <!-- 同時將購物車（cart）傳入，用於檢查哪些項目已在購物車中 -->
      <itemLists :drinks="drinks" :addToCart="addToCart" :cart="cart" />

      <!-- 引用購物車元件，傳入 cart（購物車項目）、sum（總金額）和 description（訂單備註）作為屬性 -->
      <!-- 綁定 update:cart 事件以更新購物車內容 -->
      <!-- 綁定 orderCreated 事件來處理訂單建立 -->
      <!-- 綁定 itemRemoved 事件來處理項目的移除 -->
      <!-- 綁定 resetCart 事件來重置購物車 -->
      <itemCart
        :cart="cart"
        :sum="sum"
        v-model:description="description"
        @update:cart="(newCart) => (cart.value = newCart)"
        @orderCreated="handleOrderCreated"
        @itemRemoved="handleItemRemoved"
        @resetCart="resetCart" />
    </div>

    <!-- 在購物車和訂單之間插入一條水平分隔線 -->
    <hr />

    <!-- 引用訂單元件，傳入 order（訂單訊息）作為屬性 -->
    <itemOrder :order="order" />
  </div>
</template>
