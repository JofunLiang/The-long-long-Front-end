# Vite项目构建代码检查工作流

## 集成 ESLint

### 安装依赖

```
npm install eslint --save-dev
```

### 初始化 ESLint 配置

```
npx eslint --init
```

### 选择模式(To check syntax, find problems, and enforce code style)

```
You can also run this command directly using 'npm init @eslint/config'.
npx: 40 安装成功，用时 14.189 秒
? How would you like to use ESLint? ... 
  To check syntax only
  To check syntax and find problems
> To check syntax, find problems, and enforce code style
```

### 选择语言模块(选JavaScript modules)

```
? What type of modules does your project use? ...
> JavaScript modules (import/export)
  CommonJS (require/exports)
  None of these
```

### 选择语言框架(选Vue.js)

? Which framework does your project use? ...
  React
> Vue.js
  None of these

### 是否使用 TypeScript (视自己情况而定,我这里不用选No)

```
? Does your project use TypeScript? » No / Yes
```

### 运行环境 (用空格选中 Browser + Node)

```
? Where does your code run? ...  (Press  to select,  to toggle all,  to invert selection)
√ Browser
√ Node
```

### 配置规范（Use a popular style guide）

```
? How would you like to define a style for your project? ... 
> Use a popular style guide
  Answer questions about your style
```

### 选择编码规范（? Which style guide do you want to follow? ... 

```
? Which style guide do you want to follow? ... 
  Airbnb: https://github.com/airbnb/javascript
> Standard: https://github.com/standard/standard
  Google: https://github.com/google/eslint-config-google
  XO: https://github.com/xojs/eslint-config-xo
```

### 配置文件格式 (选JavaScript)

```
? What format do you want your config file to be in? ...
> JavaScript
  YAML
  JSON
```

### 是否立即安装 (选择Yes)

```
? Would you like to install them now? » No / Yes
```

### 选用包管理器 (选择yarn)

```
? Which package manager do you want to use? ...
  npm
> yarn
  pnpm
```

安装完成后，在项目根目录会出现 .eslintrc.js 配置文件，配置如下：
```js
module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true
  },
  extends: [
    'plugin:vue/vue3-essential',
    'standard'
  ],
  overrides: [
  ],
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module'
  },
  plugins: [
    'vue'
  ],
  rules: {
  }
}
```

修改 package.json 配置文件，添加以下命令：
```
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
+   "lint": "eslint src/**/*.{js,vue} --fix"
  }
}
```

### 运行时检查

安装依赖：
```
npm install vite-plugin-eslint --save-dev
```

修改 vite.config.js 配置文件：
```diff
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
+import eslint from 'vite-plugin-eslint'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
+   eslint({
+     cache: false,
+     lintOnStart: true,
+     fix: true,
+     failOnError: false,
+     include: ['src/**/*.js', 'src/**/*.vue']
+   })
  ]
})
```
> 注意：当前使用的版本号，vite 使用 2.8.0 版本，vite-plugin-eslint 使用 1.8.1 版本。

## 集成 Prettier

安装依赖：
```bash
npm install prettier eslint-config-prettier eslint-plugin-prettier --save-dev
```
* prettier: 核心模块
* eslint-config-prettier: 关闭所有不必要或可能跟 prettier 产生冲突的规则
* eslint-plugin-prettier: 可以让 eslint 使用 prettier 规则进行检查

.eslintrc.json 文件中添加 extends 设置：
```diff
module.exports = {
  extends: [
    'plugin:vue/vue3-essential',
    'standard',
+   'plugin:prettier/recommended'
  ],
}
```

## 集成 Stylelint

安装依赖：
```
npm install stylelint@13.13.1 stylelint-scss@3.21.0 stylelint-config-standard@22.0.0 stylelint-config-prettier@9.0.3 --save-dev
```
> 如果项目没有集成 Prettier，可以不安装 stylelint-config-prettier。

在项目根目录添加 .stylelintrc.js 配置文件：
```js
module.exports = {
  extends: [
    'stylelint-config-standard',
    'stylelint-config-prettier'
  ],
  plugins: ['stylelint-scss']
}
```

修改 package.json 文件，添加以下命令：
```diff
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "eslint src/**/*.{js,vue} --fix",
+   "lint:style": "stylelint src/**/*.{html,css,scss,sass,vue} --fix"
  }
}
```
> 注意：目前 Vite 社区还没有 Stylelint 的运行时插件。