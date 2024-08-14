<script setup>
// 從 Vue 引入 defineProps 函數，用於定義父元件傳入的 props
import { defineProps } from "vue";

// 定義從父元件傳入的 props
// order: 包含訂單訊息的對象，包括訂單 ID、購物車內容、備註和總金額
const props = defineProps({
  order: Object,
});
</script>

<template>
  <!-- 使用 Bootstrap 的 row 和 col-12 類別來定義網格佈局 -->
  <div class="row">
    <div class="col-12">
      <!-- 如果 order.id 不存在，則顯示一個提示訊息，表明尚未建立訂單 -->
      <div
        v-if="!order.id"
        class="alert alert-secondary text-center"
        role="alert">
        尚未建立訂單
      </div>

      <!-- 如果 order.id 存在，顯示訂單詳細訊息 -->
      <div v-else class="card">
        <div class="card-body">
          <div class="card-title">
            <h5>訂單</h5>
            <!-- 使用表格展示訂單中的各個品項 -->
            <table class="table">
              <thead>
                <tr>
                  <th scope="col">品項</th>
                  <th scope="col">數量</th>
                  <th scope="col">小計</th>
                </tr>
              </thead>
              <tbody>
                <!-- 使用 v-for 指令遍歷 order.cart 陣列，為每個品項渲染一行 -->
                <tr v-for="item in order.cart" :key="item.id">
                  <td>{{ item.name }}</td>
                  <!-- 顯示品項名稱 -->
                  <td>{{ item.quantity }}</td>
                  <!-- 顯示品項數量 -->
                  <td>{{ item.price * item.quantity }}</td>
                  <!-- 顯示品項的小計金額 -->
                </tr>
              </tbody>
            </table>
            <!-- 顯示訂單的備註內容，如果沒有備註，顯示 "無" -->
            <div class="text-end">
              備註: <span>{{ order.description || "無" }}</span>
            </div>
            <!-- 顯示訂單的總金額，並排版為右對齊 -->
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
</template>
