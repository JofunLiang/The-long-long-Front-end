# Git 钩子构建代码检查工作流

什么是 Git 钩子（Git Hooks）？

[Git 钩子](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90)是一些自定义的脚本，Git 能在特定的重要动作发生时触发自定义脚本。Git 钩子都被存储在 Git 目录下的 hooks 子目录中。也即绝大部分项目中的 .git/hooks。用 git init 初始化一个新版本库时，Git 默认会在 hooks 目录中放置一些示例脚本，这些示例脚本文件都是以 .sample 后缀结尾，如果启用这些钩子，需要先移除文件后缀。

Git 钩子主要分为以下两种：
* 客户端钩子：由诸如提交和合并这样的操作所调用，如：pre-commit、prepare-commit-msg、commit-msg、post-commit等这些钩子。主要用于控制客户端 Git 的提交工作流。
* 服务器端钩子：由推送到服务器之前和之后的联网操作所调用，如：pre-receive、update、post-receive。

## 使用 husky 启用钩子

由上可知，启用 Git 钩子还是相对繁琐的。[husky](https://github.com/typicode/husky) 可以很方便的向项目中添加 Git 钩子。通常情况下，只需要如下两步就可在项目中引入并设置好 husky。

1、将 husky 添加到项目的开发依赖中：
```shell
npm install husky@4.3.8 -D
```

2、在 package.json 中设置需要的 Git 钩子：
```json
{
  "husky": {
    "hooks": {
      "pre-commit": "npm run lint", // 在 commit 之前先执行 npm run lint 命令
    }
  }
}
```

> 这里使用 husky v4 的最后一个版本，v5 版本以后的 husky 配置方式有所不同。

## 只检查改动文件

检查所有文件不失为一种选择，但是这样做较为损耗性能。实际上多数人在项目中运用新工具都希望是渐进式的，而不是推倒重来式的。因此，每次提交时只检查本次提交所修改的文件是很有必要的。[lint-staged](https://github.com/okonet/lint-staged) 就是基于这个想法的优秀工具，只对 staged 中的文件起作用（其中，staged 是 Git 里面的概念，指暂存区，使用 git commit -a，或者先 git add 然后 git commit 的时候，被修改的代码文件都会经过暂存区）。

安装 lint-staged：
```shell
npm install lint-staged -D
```

在 package.json 中添加 lint-staged 的配置：
```json
{
  "lint-staged": {
    "src/**/*.{js,jsx,vue}": [
      "vue-cli-service lint"
    ],
    "*.{html,css,less,vue}": [
      "npm run lint:css"
    ]
  }
}
```

修改 Git 钩子 pre-commit 的配置，在提交代码前执行 lint-staged 的操作：
```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  }
}
```