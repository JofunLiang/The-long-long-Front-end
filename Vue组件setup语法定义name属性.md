# Vue 组件 setup 语法定义 name 属性

setup 语法是指在单文件组件中使用组合式 API 的编译时语法糖。相比于普通的 \<script\> 语法，它具有更多优势：
* 更少的样板内容，更简洁的代码。
* 能够使用纯 TypeScript 声明 props 和抛出事件。
* 更好的运行时性能 (其模板会被编译成与其同一作用域的渲染函数，没有任何的中间代理)。
* 更好的 IDE 类型推断性能 (减少语言服务器从代码中抽离类型的工作)。

setup 语法糖虽然有上面的优势，但与普通的 \<script\> 相比也存在很明显的劣势：
> 部分选项无法在 setup 语法中声明，例如 inheritAttrs 或通过插件启用的自定义的选项。

比如，本文所述的自定义组件的 name 属性，setup 语法糖无法自定义问题。

## 混用普通 \<script\> 语法

setup 语法支持与普通 \<script\> 语法一起混用，可以通过普通 \<script\> 语法声明组件的 name 属性。
```vue
<template>
  <button>按钮</button>
</template>

<script>
export default {
  name: 'VButton'
}
</script>

<script setup>
// do something
</script>
```
这样就可以修改组件名称。

## 插件实现

使用构建工具，如：Webpack、Vite。可以通过插件 [unplugin-vue-define-options](https://github.com/sxzz/unplugin-vue-define-options) 修改组件名称。以下介绍 Webpack 中使用 unplugin-vue-define-options 的方法。

### 安装插件

运行命令：
```
npm install unplugin-vue-define-options -D
```

### 配置插件

在 vue.config.js 中添加以下配置：
```js
// vue.config.js
const { defineConfig } = require('@vue/cli-service')

module.exports = defineConfig({
  configureWebpack: {
    plugins: [
      require('unplugin-vue-define-options/webpack')()
    ]
  }
})
```

### 配置 Typescript（未使用的忽略此步骤）

如果使用了 Typescript 开发，还需修改 tsconfig.json 配置。添加以下配置即可：
```json
{
  "compilerOptions": {
    "types": [
      "unplugin-vue-define-options",
      ...
    ]
  }
}
```

### 声明选项

在组件中使用 defineOptions 函数声明选项。

如修改组件 name 属性：
```vue
<template>
  <button>按钮</button>
</template>

<script setup>
defineOptions({
  name: 'VButton'
})
</script>
```


unplugin-vue-define-options 的使用方法及 vite 的配置方法可以移步到 [unplugin-vue-define-options 文档](https://github.com/sxzz/unplugin-vue-define-options)。



