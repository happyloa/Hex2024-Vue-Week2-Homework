<script setup>
import { defineProps } from "vue";

// 定義從父元件傳入的 props
const props = defineProps({
  order: Object,
});
</script>

<template>
  <div class="row">
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
              備註: <span>{{ order.description || "無" }}</span>
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
</template>
