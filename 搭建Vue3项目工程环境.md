## 搭建前工具准备

1. VSCode: 编辑器
2. Chrome: 浏览器
3. Node.js & npm: 本地开发环境，Node.js 版本需要大于等于 12.0.0
4. Vue.js devtools: 浏览器调试插件
5. Vue Language Features(Volar): VSCode 插件
6. Vue 3 Snippets: VSCode 插件

## 架构搭建

### 使用 Vite 初始化项目雏形

在 CMD 中输入以下命令

```bash
npm init @vitejs/app
```

接下来按照终端提示完成以下操作：

1. 输入项目名称

2. 选择模板：`vue-ts`

3. 安装依赖

   ```bash
   npm install
   ```

4. 启动项目

   ```bash
   npm run dev
   ```

### 修改 Vite 配置文件

Vite 配置文件 `vite.config.ts` 位于根目录下，项目启动时会自动读取。

本项目先做一些简单配置，例如：设置 `@` 指向 `src` 目录、 服务启动端口、打包路径、代理等。

```tsx
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
// 如果编辑器提示 path 模块找不到，则可以安装一下 @types/node -> npm i @types/node -D
import { resolve } from 'path'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src') // 设置 `@` 指向 `src` 目录
    }
  },
  base: './', // 设置打包路径
  server: {
    port: 4000, // 设置服务启动端口号
    open: true, // 设置服务启动时是否自动打开浏览器
    cors: true // 允许跨域

    // 设置代理，根据我们项目实际情况配置
    // proxy: {
    //   '/api': {
    //     target: 'http://xxx.xxx.xxx.xxx:8000',
    //     changeOrigin: true,
    //     secure: false,
    //     rewrite: (path) => path.replace('/api/', '/')
    //   }
    // }
  }
})
```

## 约束代码风格

### 集成 Prettier 配置

1. 安装 Prettier

   ```bash
   npm i prettier -D
   ```

2. 创建 Prettier 配置文件

   Prettier 支持多种格式的[配置文件](https://link.juejin.cn?target=https%3A%2F%2Fprettier.io%2Fdocs%2Fen%2Fconfiguration.html)，比如 `.json`、`.yml`、`.yaml`、`.js`等。

   在本项目根目录下创建 `.prettierrc` 文件。

3. 配置 `.prettierrc`

   在本项目中，我们进行如下简单配置，关于更多配置项信息，请前往官网查看 [Prettier-Options](https://link.juejin.cn?target=https%3A%2F%2Fprettier.io%2Fdocs%2Fen%2Foptions.html) 。

   ```json
   {
     "useTabs": false,
     "tabWidth": 2,
     "printWidth": 100,
     "singleQuote": true,
     "trailingComma": "none",
     "bracketSpacing": true,
     "semi": false
   }
   ```

4. Prettier 安装且配置好之后，就能使用命令来格式化代码

   ```bash
   # 格式化所有文件（. 表示所有文件）
   npx prettier --write .
   ```

   注意：

   VSCode 编辑器使用 Prettier 配置需要下载插件 **Prettier - Code formatter** 。

   Prettier 配置好以后，在使用 VSCode 等编辑器的格式化功能时，编辑器就会按照 Prettier 配置文件的规则来进行格式化，避免了因为大家编辑器配置不一样而导致格式化后的代码风格不统一的问题。

### 集成 ESLint 配置

1. 安装 ESLint

   推荐在本地安装。

   ```bash
   npm i eslint -D
   ```

2. 配置 ESLint

   ESLint 安装成功后，执行 `npx eslint --init`，然后按照终端操作提示完成设置。

   根据上面的选择，ESLint 会自动去查找缺失的依赖，如果自动安装依赖失败，那么需要手动安装。

   ```bash
   npm i @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-airbnb-base eslint-plugin-import eslint-plugin-vue -D

   ```

3. ESLint 配置文件 `.eslintrc.js`

   完成上一步操作后，项目根目录下自动生成`.eslintrc.js` 配置文件。

   ```js
   module.exports = {
     env: { browser: true, es2021: true, node: true },
     extends: ['plugin:vue/vue3-essential', 'airbnb-base'],
     parserOptions: { ecmaVersion: 12, parser: '@typescript-eslint/parser', sourceType: 'module' },
     plugins: ['vue', '@typescript-eslint'],
     rules: {}
   }
   ```

   虽然，现在编辑器已经给出错误提示和修复方案，但需要我们一个一个去点击修复，还是挺麻烦的。很简单，我们只需设置编辑器保存文件时自动执行 `eslint --fix` 命令进行代码风格修复。

   - VSCode 在 `settings.json` 设置文件中，增加以下代码：

     ```js
      "editor.codeActionsOnSave": {
         "source.fixAll.eslint": true
      }
     ```

### 解决 Prettier 和 ESLint 的冲突

通常大家会在项目中根据实际情况添加一些额外的 ESLint 和 Prettier 配置规则，难免会存在规则冲突情况。

本项目中的 ESLint 配置中使用了 Airbnb JavaScript 风格指南校验，其规则之一是*代码结束后面要加分号*，而我们在 Prettier 配置文件中加了*代码结束后面不加分号*的配置项，这样就有冲突了，会出现用 Prettier 格式化后的代码，ESLint 检测到格式有问题的，从而抛出错误提示。

解决两者冲突问题，需要用到 **eslint-plugin-prettier** 和 **eslint-config-prettier**。

- `eslint-plugin-prettier` 将 Prettier 的规则设置到 ESLint 的规则中。
- `eslint-config-prettier` 关闭 ESLint 中与 Prettier 中会发生冲突的规则。

最后形成优先级：`Prettier 配置规则` > `ESLint 配置规则`。

- 安装插件

  ```bash
  npm i eslint-plugin-prettier eslint-config-prettier -D
  ```

- 在 `.eslintrc.js` 添加 prettier 插件

  ```js
  module.exports = {
    ...
    extends: [
      'plugin:vue/vue3-essential',
      'airbnb-base',
      'plugin:prettier/recommended' // 添加 prettier 插件
    ],
    ...
  }

  ```

这样，我们在执行 `eslint --fix` 命令时，ESLint 就会按照 Prettier 的配置规则来格式化代码，轻松解决二者冲突问题。

### 集成 husky 和 lint-staged

我们在项目中已集成 ESLint 和 Prettier，在编码时，这些工具可以对我们写的代码进行实时校验，在一定程度上能有效规范我们写的代码，但团队可能会有些人觉得这些条条框框的限制很麻烦，选择视“提示”而不见，依旧按自己的一套风格来写代码，或者干脆禁用掉这些工具，开发完成就直接把代码提交到了仓库，日积月累，ESLint 也就形同虚设。

所以，我们还需要做一些限制，让没通过 ESLint 检测和修复的代码禁止提交，从而保证仓库代码都是符合规范的。

为了解决这个问题，我们需要用到 Git Hook，在本地执行 `git commit` 的时候，就对所提交的代码进行 ESLint 检测和修复（即执行 `eslint --fix`），如果这些代码没通过 ESLint 规则校验，则禁止提交。

实现这一功能，我们借助 [husk](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Ftypicode%2Fhusky) + [lint-staged](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fokonet%2Flint-staged) 。

> [husky](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ftypicode%2Fhusky) —— Git Hook 工具，可以设置在 git 各个阶段（`pre-commit`、`commit-msg`、`pre-push` 等）触发我们的命令。
> [lint-staged](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fokonet%2Flint-staged) —— 在 git 暂存的文件上运行 linters。

### 配置 husky

- 自动配置（推荐）

  使用 `husky-init` 命令快速在项目初始化一个 husky 配置。

  ```bash
  npx husky-init && npm install
  ```

  这行命令做了四件事：

  1. 安装 husky 到开发依赖
  2. 在项目根目录下创建 `.husky` 目录
  3. 在 `.husky` 目录创建 `pre-commit` hook，并初始化 `pre-commit` 命令为 `npm test`
  4. 修改 `package.json` 的 `scripts`，增加 `"prepare": "husky install"`

> **特别注意：本项目使用 husky 6.x 版本，6.x 版本配置方式跟之前的版本有较大差异。目前网上大部分有关 husky 的教程都是 6 以前的版本 ，跟本文教程不太一样，当发现配置方法不一致时，一切以 [husky 官网](https://link.juejin.cn/?target=https%3A%2F%2Ftypicode.github.io%2Fhusky%2F%23%2F%3Fid%3Dusage)为准。**

到这里，husky 配置完毕，现在我们来使用它：

husky 包含很多 `hook`（钩子），常用有：`pre-commit`、`commit-msg`、`pre-push`。这里，我们使用 `pre-commit` 来触发 ESLint 命令。

修改 `.husky/pre-commit` hook 文件的触发命令：

```bash
eslint --fix ./src --ext .vue,.js,.ts
```

上面这个 `pre-commit` hook 文件的作用是：当我们执行 `git commit -m "xxx"` 时，会先对 `src` 目录下所有的 `.vue`、`.js`、`.ts ` 文件执行 `eslint --fix` 命令，如果 ESLint 通过，成功 `commit`，否则终止 `commit`。

但是又存在一个问题：有时候我们明明只改动了一两个文件，却要对所有的文件执行 `eslint --fix`。假如这是一个历史项目，我们在中途配置了 ESLint 规则，那么在提交代码时，也会对其他未修改的“历史”文件都进行检查，可能会造成大量文件出现 ESLint 错误，显然不是我们想要的结果。

我们要做到只用 ESLint 修复自己此次写的代码，而不去影响其他的代码。所以我们还需借助一个神奇的工具 **lint-staged** 。

### 配置 lint-staged

lint-staged 这个工具一般结合 husky 来使用，它可以让 husky 的 `hook` 触发的命令只作用于 `git add`那些文件（即 git 暂存区的文件），而不会影响到其他文件。

接下来，我们使用 lint-staged 继续优化项目。

1. 安装 lint-staged

   ```bash
   npm i lint-staged -D
   ```

2. 在 `package.json`里增加 lint-staged 配置项

   ```json
   "lint-staged": {
     "*.{vue,js,ts}": "eslint --fix"
   },
   ```

   这行命令表示：只对 git 暂存区的 `.vue`、`.js`、`.ts` 文件执行 `eslint --fix`。

3. 修改 `.husky/pre-commit` hook 的触发命令为：`npx lint-staged`

至此，husky 和 lint-staged 组合配置完成。

现在我们提交代码时就会变成这样：

假如我们修改了 `scr` 目录下的 `test-1.js`、`test-2.ts` 和 `test-3.md` 文件，然后 `git add ./src/`，最后 `git commit -m "test..."`，这时候就会只对 `test-1.js`、`test-2.ts` 这两个文件执行 `eslint --fix`。如果 ESLint 通过，成功提交，否则终止提交。从而保证了我们提交到 Git 仓库的代码都是规范的。
