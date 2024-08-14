<script setup>
import { defineProps } from "vue";

const props = defineProps({
  drinks: Array,
  addToCart: Function,
});
</script>

<template>
  <aside class="col-md-4">
    <div class="list-group">
      <a
        v-for="drink in drinks"
        :key="drink.id"
        href="#"
        class="list-group-item list-group-item-action"
        @click.prevent="addToCart(drink)">
        <div class="d-flex w-100 justify-content-between">
          <h5 class="mb-1">{{ drink.name }}</h5>
          <small>${{ drink.price }}</small>
        </div>
        <p class="mb-1">{{ drink.description }}</p>
      </a>
    </div>
  </aside>
</template>
