## 搭建前工具准备

1. VSCode: 编辑器
2. Chrome: 浏览器
3. Node.js & npm: 本地开发环境，Node.js 版本需要大于等于 12.0.0
4. Vue.js devtools: 浏览器调试插件
5. Vue Language Features(Volar): VSCode 插件
6. Vue 3 Snippets: VSCode 插件

## 技术栈

## 使用 Vite 初始化项目雏形

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

   1. 创建 Prettier 配置文件

      Prettier 支持多种格式的[配置文件](https://link.juejin.cn?target=https%3A%2F%2Fprettier.io%2Fdocs%2Fen%2Fconfiguration.html)，比如 `.json`、`.yml`、`.yaml`、`.js`等。

      在本项目根目录下创建 `.prettierrc` 文件。

   2. 配置 `.prettierrc`

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

   3. Prettier 安装且配置好之后，就能使用命令来格式化代码

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

## 提交规范

 