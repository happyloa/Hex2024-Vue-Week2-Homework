<script setup>
import { defineProps } from "vue";

const props = defineProps({
  drinks: Array,
  addToCart: Function,
  cart: Array, // 用於檢查品項是否已在購物車中
});

// 計算屬性: 判斷某品項是否已在購物車中
const isInCart = (drink) => {
  return props.cart.some((item) => item.name === drink.name);
};
</script>

<template>
  <aside class="col-md-4">
    <div class="list-group">
      <a
        v-for="drink in drinks"
        :key="drink.id"
        href="#"
        class="list-group-item list-group-item-action"
        :class="{ disabled: isInCart(drink) }"
        @click.prevent="isInCart(drink) ? null : addToCart(drink)">
        <div class="d-flex w-100 justify-content-between">
          <h5 class="mb-1">{{ drink.name }}</h5>
          <small>${{ drink.price }}</small>
        </div>
        <p class="mb-1">{{ drink.description }}</p>
      </a>
    </div>
  </aside>
</template>
