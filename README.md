## MIGRATE VUE CLI TO VITE
And then first, jadi kita di sini punya program yg sudah jadi tapi masih menggunakan vue-cli dan kita akan migrasi dari vue cli ke vite dan kalo kalian tau vite ini adalah salaha satu front end tooling yg akan di gunakan selama beberapa tahun ke depan.

1. So, langkah pertama adalah kalian bisa remove semua vue cli service.
```json
"@vue/cli-plugin-babel": "4.5.0",
"@vue/cli-plugin-eslint": "4.5.0",
"@vue/cli-plugin-router": "4.5.0",
"@vue/cli-service": "4.5.0",
"sass-loader": "10.1.1"
```
Dan terlihat di sini gw juga menghapus sass-loader. karena, vite sudah mendukung semua css preposesor.

2. Langkah ke dua kalian instal Vite nya.
```bash
npm i -D vite
```
jika kalian menggunakan vue versi 2
```bash
npm i -D vite-plugin-vue2
```

3. And then kalian instal juga vite plugin vue nya.
```bash
npm i -D @vitejs/plugin-vue
```
kemudian jalankan npm prune karena telah merubah structur peckage.json secara manual.

4. Dan hapus juga beberapa peckage seperti
```json
"dependencies": {
    "core-js": "3.6.5"
},
"devDependencies": {
    "babel-eslint": "10.1.0"
}
```

5. kalian pergi ke file .eslintrc.js dan hapus bagian ini.
```js
parserOptions: {
    parser: "babel-eslint"
},
```
Dan replace bagian ini
```js
env: {
    node: true
    //replace dengan
    es2021: true
},
```

6. kemudian install eslint dan eslint plugin vue versi latest.
```bash
npm install eslint@8 eslint-plugin-vue@8
```
dan lakuakan npm prune lagi.

7. Selanjutnya kalian tambahkan vite.config.js file dan isi.
```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import { fileURLToPath, URL } from "node:url";

export default defineConfig({
  Plugin: [vue()],
  resolve: {
    alias: {
      "@": fileURLToPath(new URL("./src", import.meta.env)),
    },
  },
});
```

8. Kalian pindah index.html yang ada di public ke root folder project kalian and kalian replace ini.
```html
<link rel="icon" href="<%= BASE_URL %>favicon.png" />
<!-- replace menjadi -->
<link rel="icon" href="./public/favicon.png" />
<!-- dan add script di bawah div di dalam body -->
<div id="app" class="g-sidenav-show"></div>
<script type="module" src="/src/main.js"></script>
```

9. Update script di pekcage.json kalian.
```json
"scripts": {
    "dev": "vite",
    "build": "vite build",
    "serve": "vite preview",
    "lint": "eslint --ext .js,.vue --ignore-path .gitignore --fix src"
},
```

10. dan kalian bisa ganti di bagian router/index.js nya dengan
```js
const router = createRouter({
    history: createWebHistory(process.env.BASE_URL),
    //replace dengan
    history: createWebHistory(import.meta.env.BASE_URL),
    routes,
    linkActiveClass: "active",
});
```
11. add juga file bernama .env yg bertujuan untuk mengakses base url yang di berikan oleh back end.
```env
VITE_APP_BASE_URL=http://localhost:PORT
```

12. dan kembali lagi ke vite.config.js dan tambahkan extention.
```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import { fileURLToPath, URL } from "node:url";

export default defineConfig({
  Plugin: [vue()],
  resolve: {
    alias: {
      "@": fileURLToPath(new URL("./src", import.meta.env)),
    },
    extensions: [".mjs", ".js", ".ts", ".jsx", ".tsx", ".json", ".vue"],
  },
});

```

finally we are done.