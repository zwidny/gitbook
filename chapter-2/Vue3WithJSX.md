# Vue 局部组件 + JSX

---

本文将展示如何将component.vue 改写称jsx
### 开始之前
1. 安装需求package
  ```
npm install \
  @vue/babel-preset-jsx \
  @vue/babel-helper-vue-jsx-merge-props \
  --save-dev
  ```

2. 调整.babelrc
  ```
  presets: [
    "@vue/babel-preset-jsx"
  ],
  ```
3. 修改webpack 配置文件
   ```json
   rules: [
      {
        test: /\.jsx?$/,
        loader: 'babel-loader'
      }
   ]
   
   ```

### 修改部分
已有的文件
```
// Hello.vue
<template>
  <h1>{{ message }}</h1>
</template>

<script>
  export default {
    data: () => ({
      message: "Hello, JSX!"
    })
  };
</script>
```
改写之后的文件
```
// Hello.jsx
export default {
  data: () => ({
    message: "Hello, JSX!"
  }),

  render (h) {
    // eslint-disable-next-line no-console
    console.log(h);
    return <h1>{this.message}</h1>;
  }
};
```
### 如果你使用webpack.config.js
```
{ test: /\.jsx?$/, loader: 'babel-loader' },
```

### 主程序
```
//App.vue
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
    <HW></HW>
  </div>
</template>

<script>
import HelloWorld from './components/Hello.vue'
import HW  from "./components/Hello.jsx"

export default {
  name: 'app',
  components: {
    HelloWorld,
    HW,
  }
}
</script>
```
```
//main.js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')

```
---
#### 参考
[vue-templates-in-jsx](https://sebastiandedeyne.com/vue-templates-in-jsx/)

