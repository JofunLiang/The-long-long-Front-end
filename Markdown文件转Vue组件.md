# Markdown 文件转 Vue 组件

Markdown 是一种轻量级标记语言，是一种简单的格式化文本的方法，在任何设备上看起来都很棒。它不会做任何花哨的事情，比如改变字体大小、颜色或类型——只是基本的，使用你已经知道的键盘符号。由于 Markdown 的轻量化、易读易写特性，并且对于图片，图表、数学式都有支持，许多网站都广泛使用 Markdown 来撰写帮助文档或是用于论坛上发表消息。

Vue 项目中怎么使用 Markdown 文档？这里介绍如何通过 Webpack 将 Markdown 文件转成 Vue 组件，实现 Vue 项目中混用用 Markdown 文件。

## 配置 loader

Webpack 加载 Markdown 文件需要安装 vue-markdown-loader：
```
# For Vue1
npm install vue-markdown-loader@0 -D

# For Vue2
npm install vue-markdown-loader -D
```

使用 Vue CLI 3 时，在 vue.config.js 文件中添加以下配置：
```js
module.exports = {
  chainWebpack: config => {
    config.module.rule('md')
      .test(/\.md/)
      .use('vue-loader')
      .loader('vue-loader')
      .end()
      .use('vue-markdown-loader')
      .loader('vue-markdown-loader/lib/markdown-compiler')
      .options({
        raw: true
      })
  }
}
```
查看更多使用方法或文档移步 [vue-markdown-loader](https://github.com/QingWei-Li/vue-markdown-loader)。

然后，在需要使用 Markdown 文件的地方直接 import 文件即可，如：
```js
import Vue from 'vue'
import Post from './post.md'

new Vue({
  el: '#app',
  render: h => h(Post)
})
```

## 美化样式

使用 Github 的 Markdown 文档样式 [github-markdown-css](https://github.com/sindresorhus/github-markdown-css) 对组件样式进行美化。安装样式包：
```
npm install github-markdown-css
```
然后，将 github-markdown-css 引入到项目中，在 Markdown 文档内容的父容器中增加 markdown-body 样式类：
```js
import Vue from 'vue'
import Post from './post.md'
import 'github-markdown-css'

new Vue({
  el: '#app',
  render: h => h(Post, { class: 'markdown-body' })
})
```

## 代码高亮

代码高亮第一个就想到了 [highlight.js](https://github.com/highlightjs/highlight.js)，highlight.js 是用 JavaScript 编写的语法高亮插件，vue-markdown-loader 在转换时已经添加了代码高亮的样式类，所有只需要使用 highlight.js 的样式文件就可以了。

先安装：
```
npm install highlight.js
```
然后，在项目中引入 highlight.js 的样式文件：
```js
import Vue from 'vue'
import Post from './post.md'
import 'github-markdown-css';
import 'highlight.js/styles/github.css'

new Vue({
  el: '#app',
  render: h => h(Post, { class: 'markdown-body' })
})
```


