<script setup>
import { ref, watch, defineProps, defineEmits } from "vue";

// 定義從父元件傳入的 props
const props = defineProps({
  cart: Array,
  sum: Number,
  description: String,
});

const emit = defineEmits([
  "update:cart",
  "update:description",
  "orderCreated",
  "itemRemoved",
]);

// 本地的 description 綁定
const localDescription = ref(props.description);
const localCart = ref(props.cart);

// 監聽 description 的變化並發送事件到父元件
watch(localDescription, (newValue) => {
  emit("update:description", newValue);
});

// 監聽 cart 的變化並發送事件到父元件
watch(localCart, (newCart) => {
  emit("update:cart", newCart);
});

// 方法: 計算單項小計
const itemSubtotal = (item) => {
  return item.price * item.quantity;
};

// 方法: 更新購物車項目的數量
const updateCart = (item) => {
  localCart.value = localCart.value.map((cartItem) => {
    if (cartItem.id === item.id) {
      cartItem.quantity = parseInt(item.quantity);
    }
    return cartItem;
  });
};

// 方法: 從購物車中移除項目
const removeFromCart = (id) => {
  localCart.value = localCart.value.filter((cartItem) => cartItem.id !== id);
  emit("itemRemoved", id); // 發送 itemRemoved 事件到父元件
};

// 方法: 建立訂單
const createOrder = () => {
  const order = {
    id: new Date().getTime(),
    cart: localCart.value,
    description: localDescription.value,
    sum: props.sum,
  };
  // 重置購物車與備註
  localCart.value = [];
  localDescription.value = "";
  // 發送訂單已建立事件到父元件
  emit("orderCreated", order);
};
</script>

<template>
  <main class="col-md-8">
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
        <tr v-for="item in localCart" :key="item.id">
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
      v-if="localCart.length === 0"
      class="alert alert-primary text-center"
      role="alert">
      請選擇商品
    </div>
    <div v-else>
      <div class="text-end mb-3">
        <h5>
          總計: <span>${{ props.sum }}</span>
        </h5>
      </div>
      <textarea
        class="form-control mb-3"
        rows="3"
        placeholder="備註"
        v-model="localDescription"></textarea>
      <div class="text-end">
        <button class="btn btn-primary" @click.prevent="createOrder">
          送出
        </button>
      </div>
    </div>
  </main>
</template>
