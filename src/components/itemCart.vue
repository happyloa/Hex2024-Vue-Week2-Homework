<script setup>
// 從 Vue 引入 ref、watch、defineProps 和 defineEmits 方法，用於管理響應式狀態、監聽變化、定義父元件傳入的 props 和定義事件
import { ref, watch, defineProps, defineEmits } from "vue";

// 定義從父元件傳入的 props
// cart: 包含購物車中的品項陣列
// sum: 購物車中所有品項的總金額
// description: 訂單的備註
const props = defineProps({
  cart: Array,
  sum: Number,
  description: String,
});

// 定義 emits，這些事件將從此元件發送到父元件
// update:cart: 更新購物車內容
// update:description: 更新訂單備註
// orderCreated: 訂單已建立
// itemRemoved: 某項目已從購物車中移除
// resetCart: 重置購物車
const emit = defineEmits([
  "update:cart",
  "update:description",
  "orderCreated",
  "itemRemoved",
  "resetCart",
]);

// 本地的 description 綁定到父元件傳入的 description
const localDescription = ref(props.description);

// 監聽 localDescription 的變化，當本地 description 變化時，發送 update:description 事件到父元件
watch(localDescription, (newValue) => {
  emit("update:description", newValue);
});

// 方法: 計算單項小計
// 接收一個購物車項目 item 作為參數，返回該項目的小計金額（單價乘以數量）
const itemSubtotal = (item) => {
  return item.price * item.quantity;
};

// 方法: 更新購物車項目的數量
// 接收一個購物車項目 item 作為參數，根據 item 的 id 找到該項目並更新其數量
// 發送 update:cart 事件到父元件，通知購物車內容已更新
const updateCart = (item) => {
  const index = props.cart.findIndex((cartItem) => cartItem.id === item.id);
  if (index !== -1) {
    props.cart[index].quantity = parseInt(item.quantity);
    emit("update:cart", props.cart); // 發送更新購物車的事件
  }
};

// 方法: 從購物車中移除項目
// 接收一個項目的 id 作為參數，根據該 id 找到對應項目並從購物車中移除
// 發送 update:cart 事件更新購物車，並發送 itemRemoved 事件通知父元件該項目已被移除
const removeFromCart = (id) => {
  const index = props.cart.findIndex((cartItem) => cartItem.id === id);
  if (index !== -1) {
    props.cart.splice(index, 1);
    emit("update:cart", props.cart); // 發送更新購物車的事件
    emit("itemRemoved", id); // 發送 itemRemoved 事件到父元件
  }
};

// 方法: 建立訂單
// 建立一個訂單對象，包含唯一的 id、購物車內容、備註和總金額
// 清空購物車和備註，並發送 orderCreated 事件通知父元件訂單已建立
// 同時發送 resetCart 事件通知父元件購物車已被清空
const createOrder = () => {
  const order = {
    id: new Date().getTime(),
    cart: [...props.cart], // 使用展開運算符建立新購物車項目，避免直接修改原陣列
    description: localDescription.value,
    sum: props.sum,
  };
  // 重置購物車與備註
  props.cart.splice(0); // 保持引用，清空陣列內容
  localDescription.value = "";
  emit("orderCreated", order); // 發送訂單已建立事件到父元件
  emit("resetCart"); // 發送重置購物車事件，讓父元件知道購物車已被清空並允許重新添加品項
};

// 方法: 添加備註文字
// 接收一個文字片段，將其追加到 localDescription 中
const addNote = (note) => {
  // 如果備註欄已有內容，新增的內容前加一個空格
  localDescription.value += localDescription.value ? ` ${note}` : note;
};
</script>

<template>
  <!-- 使用 main 標籤來包裹主要內容，設置 col-md-8 類別以控制寬度 -->
  <main class="col-md-8">
    <!-- 使用 table 標籤來展示購物車中的項目列表 -->
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
        <!-- 使用 v-for 指令遍歷 cart 陣列，為每個項目渲染一行 -->
        <tr v-for="item in props.cart" :key="item.id">
          <td>
            <!-- 刪除按鈕，點擊時觸發 removeFromCart 方法移除該項目 -->
            <button
              type="button"
              class="btn btn-sm"
              @click="removeFromCart(item.id)">
              x
            </button>
          </td>
          <td>{{ item.name }}</td>
          <!-- 顯示項目的名稱 -->
          <td>
            <small>{{ item.description }}</small>
            <!-- 顯示項目的描述 -->
          </td>
          <td>
            <!-- 下拉選單，用於選擇購買數量，綁定 item 的 quantity，並在變更時觸發 updateCart 方法 -->
            <select
              class="form-select"
              v-model="item.quantity"
              @change="updateCart(item)">
              <!-- 使用 v-for 指令生成選項，允許選擇 1 到 10 的數量 -->
              <option v-for="n in 10" :key="n" :value="n">{{ n }}</option>
            </select>
          </td>
          <td>{{ item.price }}</td>
          <!-- 顯示項目的單價 -->
          <td>{{ itemSubtotal(item) }}</td>
          <!-- 顯示項目的小計金額 -->
        </tr>
      </tbody>
    </table>

    <!-- 如果購物車為空，顯示提示訊息 -->
    <div
      v-if="props.cart.length === 0"
      class="alert alert-primary text-center"
      role="alert">
      請選擇商品
    </div>

    <!-- 如果購物車不為空，顯示總計和訂單提交表單 -->
    <div v-else>
      <!-- 顯示總金額，並排版為右對齊 -->
      <div class="text-end mb-3">
        <h5>
          總計: <span>${{ props.sum }}</span>
        </h5>
      </div>
      <!-- 備註文本框，允許用戶輸入訂單的額外備註訊息 -->
      <textarea
        class="form-control mb-3"
        rows="3"
        placeholder="備註"
        v-model="localDescription"></textarea>
      <div class="d-flex justify-content-between gap-2">
        <!-- 甜度冰塊按鈕，讓常見的搭配種類可以直接被加進備註中 -->
        <div class="d-flex flex-wrap gap-2">
          <!-- 每個按鈕的點擊事件都會將對應的文字追加到備註欄中 -->
          <button
            type="button"
            class="btn btn-info"
            @click="addNote('全糖正常冰')">
            全糖正常冰
          </button>
          <button
            type="button"
            class="btn btn-primary"
            @click="addNote('少糖半冰')">
            少糖半冰
          </button>
          <button
            type="button"
            class="btn btn-success"
            @click="addNote('微糖半冰')">
            微糖半冰
          </button>
          <button
            type="button"
            class="btn btn-warning"
            @click="addNote('微糖溫')">
            微糖溫
          </button>
        </div>
        <!-- 送出訂單按鈕，點擊時觸發 createOrder 方法建立訂單 -->
        <button
          type="button"
          class="btn btn-primary"
          @click.prevent="createOrder">
          送出
        </button>
      </div>
    </div>
  </main>
</template>
