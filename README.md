![](https://i.imgur.com/9AnoArW.png)

# 六角學院 2024 Vue 前端新手營第三週作業

這是六角學院 2024 Vue 前端新手營第三週的作業，作者為 Aaron。使用 Vue 3 和 Vite 來製作。

- [線上部署連結](https://hex2024-vue-homework3.worksbyaaron.com/)
- [作業文件連結](https://hackmd.io/nX7LspikSmmglEb1Hyu2IQ)

## 專案簡介

- **Vue 3 Composition API**：使用 Vue 3 的 Composition API 和 `<script setup>` 語法糖。
- **Vite 構建工具**：快速構建和開發環境。
- **響應式資料**：使用 `ref` 來管理響應式資料。

## 文件結構

```
├── src
│ ├── data
│ │ └── items.js            品項資料
│ ├── components            元件存放區
│ │ └── itemLists.vue       品項列表元件
│ │ ├── itemCart.vue        品項購物車元件
│ │ └── itemOrder.vue       訂單元件
│ ├── App.vue               主要元件
│ ├── style.css             全域樣式表
│ └── main.js               入口文件
├── index.html              主 HTML 文件
├── package.json            專案配置文件
└── README.md               讀我檔案
```

## 快速開始

**專案設置（Project setup）**

將專案複製到本地端

```sh
$ git clone https://github.com/happyloa/Hex2024-Vue-Week3-Homework
```

套件安裝

```sh
$ cd Hex2024-Vue-Week3-Homework
$ npm install
```

**執行專案（Start the server）**

```sh
$ npm run dev
```

在瀏覽器上輸入

```
http://localhost:5173/
```

即可在本地端預覽專案
