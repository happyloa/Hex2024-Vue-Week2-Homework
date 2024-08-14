<script setup>
// 從 Vue 引入 defineProps 函數，用於定義傳入該元件的 props
import { defineProps } from "vue";

// 定義從父元件傳入的 props
// drinks: 包含所有可供選擇的飲品項目的陣列
// addToCart: 用於將飲品項目添加至購物車的函數
// cart: 包含當前已添加至購物車的品項陣列， 用於檢查某品項是否已在購物車中
const props = defineProps({
  drinks: Array,
  addToCart: Function,
  cart: Array,
});

// 計算屬性: 判斷某品項是否已在購物車中
// 接收一個 drink 物件作為參數，檢查該飲品是否已在購物車（cart）中
// 使用 some 方法遍歷購物車中的項目，檢查是否存在與 drink.name 相同的品項
const isInCart = (drink) => {
  return props.cart.some((item) => item.name === drink.name);
};
</script>

<template>
  <!-- 使用 aside 標籤來分隔頁面側邊欄，設定 col-md-4 類別以控制欄寬度 -->
  <aside class="col-md-4">
    <!-- 使用 list-group 類別來組織列表項目，保持統一的樣式 -->
    <div class="list-group">
      <!-- 使用 v-for 指令來遍歷 drinks 陣列，為每個飲品項目渲染一個 a 連結 -->
      <!-- 設定唯一的 key 來幫助 Vue 追蹤每個項目的狀態 -->
      <!-- 使用 Bootstrap 的類別來設計項目樣式 -->
      <!-- 如果飲品已在購物車中，添加 disabled 類別 -->
      <!-- 如果飲品已在購物車中，阻止添加操作；否則，將飲品添加到購物車中 -->
      <a
        v-for="drink in drinks"
        :key="drink.id"
        href="#"
        class="list-group-item list-group-item-action"
        :class="{ disabled: isInCart(drink) }"
        @click.prevent="isInCart(drink) ? null : addToCart(drink)">
        <!-- 使用 flex 佈局來排版飲品名稱和價格，justify-content-between 使兩者分佈在兩端 -->
        <div class="d-flex w-100 justify-content-between">
          <h5 class="mb-1">{{ drink.name }}</h5>
          <!-- 顯示飲品名稱 -->
          <small>${{ drink.price }}</small>
          <!-- 顯示飲品價格 -->
        </div>
        <p class="mb-1">{{ drink.description }}</p>
        <!-- 顯示飲品描述 -->
      </a>
    </div>
  </aside>
</template>
