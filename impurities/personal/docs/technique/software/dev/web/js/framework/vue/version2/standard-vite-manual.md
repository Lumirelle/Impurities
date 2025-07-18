# Vue 2 规范手册（Vite） / Vue 2 Standard Manual (Vite)

Project Running requires (for docker image): node@'^14.18.0 || >=16.0.0', npm@>=6.

Project Developing requires (for us): node@'^18.20.0 || ^20.10.0 || >=22.0.0', npm@>=9, pnpm@>=7, yarn@>=1.

This article is based on node@18.20.8, npm@10.9.2, corepack@0.33.0, pnpm@10.13.1.

Main dependencies:

- vue@^2.7.16, vite@^4 (@vitejs/plugin-legacy@^4)
- eslint@latest, stylelint@latest
- simple-git-hooks@latest, lint-staged@latest, commitlint@latest

## 🔧 更新 vscode 配置和 git 配置

### 快速配置

shell（For command `icp`, please see [README.md#command_setup](/README.md#command_setup)）

```shell
# vscode 配置
# -- 推荐扩展
icp vue/extensions.json .vscode/ -f
# -- 工作区设置
icp vue/settings.json .vscode/ -f
# -- js 编译器设置
icp vue2/jsconfig.json -f
# -- editor config
icp .editorconfig -f

# git 配置
# -- 文件属性
icp .gitattributes -f
# -- 忽略文件
icp nodejs.gitignore .gitignore -f
```

### 手动配置

.vscode/extensions.json

See [here](/impurities/personal/preferences/editor/vscode/workspace/vue/extensions.json).

.vscode/settings.json

See [here](/impurities/personal/preferences/editor/vscode/workspace/vue/settings.json).

jsconfig.json

See [here](/impurities/personal/preferences/project/vue2/jsconfig.json).

.editorconfig

See [here](/impurities/personal/preferences/editor/.editorconfig).

.gitattributes

See [here](/impurities/personal/preferences/vcs/git/.gitattributes).

.gitignore

See [here](/impurities/personal/preferences/vcs/git/nodejs.gitignore).

## 📦 配置包管理器和 .npmrc

### 前置任务

shell

```shell
npm i corepack@latest -g
npm i @antfu/ni@latest -g
```

### 快速配置

shell（This syntax of command `npm pkg set` requires npm@>=10.9.2）

```shell
corepack use pnpm@latest-10

# Project running requires
npm pkg set 'engines.node=^12.22.0 || ^14.17.0 || ^16.10.0 || >=18.0.0' 'engines.npm=>=6'

icp npm/.npmrc -f
```

### 手动配置

package.json

```json
{
  // ...

  // Used by corepack
  "packageManager": "pnpm@10.13.1",
  "engines": {
    "node": "^12.22.0 || ^14.17.0 || ^16.10.0 || >=18.0.0",
    "npm": ">=6"
  }

  // ...
}
```

.npmrc

See [here](/impurities/personal/preferences/package-manager/npm/.npmrc).

## 🥡 基础依赖

shell

```shell
# vue
# vue, vue-router, vuex
ni vue@^2.7.16 vue-router@legacy vuex@^3.6.2
# @vitejs/plugin-vue2 provide the ability to compiler vue template
# We don't need vue-template-compiler anymore

# builder & it's plugins
# vite, @vitejs/plugin-legacy, @vitejs/plugin-vue2
ni vite@^4 @vitejs/plugin-legacy@^4 @vitejs/plugin-vue2@latest

# Others
# terser
ni terser@latest -d
```

## 🌟 设置代码检查与格式化

> 真心期待前端有一个大统一的、完整的生态工具链！！！

### 前置任务

shell

```shell
# eslint
ni eslint@latest -d

# eslint config
# Since the version of 4.15.0, `@antfu/eslint-config` requires node@>=20
ni @antfu/eslint-config@~4.14.1 -d

# eslint & prettier plugin
ni eslint-plugin-format@latest @prettier/plugin-xml@latest -d
```

### 快速配置

shell

```shell
icp vue2/eslint.config.mjs -f
```

### 手动配置

eslint.config.mjs

See [here](/impurities/personal/preferences/linter/eslint/vue2/eslint.config.mjs).

## ✨ 设置样式检查与格式化

> 真心期待前端有一个大统一的、完整的生态工具链！！！

### 前置任务

shell

```shell
# stylelint
ni stylelint@latest -d

# stylelint config for html
ni stylelint-config-html@latest -d
# stylelint config for scss
# Since the version of 15.0.0, `stylelint-config-standard-scss` requires node@>=20
ni stylelint-config-standard-scss@^14.0.0 -d
# stylelint config for vue
ni stylelint-config-standard-vue@latest -d
# stylelint config for stylistic
# Since the version of 7.0.0, `stylelint-config-recess-order` requires node@>=20
ni @stylistic/stylelint-config@latest stylelint-config-recess-order@^6.1.0 -d
```

### 快速配置

shell

```shell
icp vue/stylelint.config.mjs -f
```

### 手动配置

stylelint.config.mjs

See [here](/impurities/personal/preferences/linter/stylelint/vue/stylelint.config.mjs).

## 📜 配置 npm 快速检查与格式化脚本

### 前置任务

shell

```shell
# Since the version of 8.0.0, `npm-run-all2` requires node@>=20
ni npm-run-all2@^7.0.2 -d
```

### 快速配置

shell

```shell
npm pkg set 'scripts.lint=run-s lint:*'
npm pkg set 'scripts.lint:js=eslint --cache --quiet .'
npm pkg set 'scripts.lint:style=stylelint --cache --quiet **/*.{css,postcss,scss,html,vue}'
npm pkg set 'scripts.fix=run-s fix:*'
npm pkg set 'scripts.fix:js=eslint --cache --fix --quiet .'
npm pkg set 'scripts.fix:style=stylelint --cache --fix --quiet **/*.{css,postcss,scss,html,vue}'
```

### 手动配置

package.json

```json
{
  // ...
  "scripts": {
    // ...

    "lint": "run-s lint:*",
    "lint:js": "eslint --cache --quiet .",
    "lint:style": "stylelint --cache --quiet **/*.{css,postcss,scss,html,vue}",
    "fix": "run-s fix:*",
    "fix:js": "eslint --cache --fix --quiet .",
    "fix:style": "stylelint --cache --fix --quiet **/*.{css,postcss,scss,html,vue}"

    // ...
  }
}
```

## 🤖 配置提交检查与格式化

### 前置任务

shell

```shell
# The performance of `simple-git-hooks` is much better than `husky`
ni simple-git-hooks@latest -d

# lint-staged & commitlint
ni lint-staged@latest @commitlint/cli@latest @commitlint/config-conventional@latest -d
```

### 快速配置

shell

```shell
npm pkg set 'scripts.prepare=simple-git-hooks'
npm pkg set 'simple-git-hooks.pre-commit=npx lint-staged'
npm pkg set 'simple-git-hooks.commit-msg=npx commitlint --edit $1'
npm pkg set 'lint-staged.*=eslint --cache --fix'
npm pkg set 'lint-staged[*.{css,postcss,scss,html,vue}]=stylelint --cache --fix'

icp commitlint/commitlint.config.mjs -f
```

### 手动配置

package.json（配置 simple-git-hooks）

```json
{
  // ...

  "scripts": {
    // ...
    "prepare": "simple-git-hooks"
  },

  // ...

  "simple-git-hooks": {
    "pre-commit": "npx lint-staged",
    "commit-msg": "npx commitlint --edit $1"
  },
  "lint-staged": {
    "*": "eslint --cache --fix",
    "*.{css,postcss,scss,html,vue}": "stylelint --cache --fix"
  }

  // ...
}
```

commitlint.config.mjs

See [here](/impurities/personal/preferences/linter/commitlint/commitlint.config.mjs).

## 💪🏼 使用 Dart Sass 提供 Sass 支持，移除 Node Sass

### 前置任务

shell

```shell
# 限制 node 版本的罪魁祸首！
nun node-sass

# sass 和 sass-loader
ni sass@latest sass-loader@version-10 -d
```

### 手动配置

vue.config.js

```js
module.exports = {
  // ...

  css: {
    loaderOptions: {
      scss: {
        sassOptions: {
          // scss 支持本身不需要任何配置
          // 只有代码中使用到大量的弃用 API 时，才需要禁用警告（因为警告输出实在是太多咧）
          silenceDeprecations: [
            'legacy-js-api',
            'mixed-decls',
            'import',
            'slash-div',
            'global-builtin',
            'function-units',
          ],
        },
      },
    },

    // ...
  },

  // ...
}
```

## 🧹 项目兼容性 & 可维护性

### [taze](https://www.npmjs.com/package/taze)

#### 使用

```shell
# taze：帮你轻松完成依赖升级
nlx taze minor -rIw
```
