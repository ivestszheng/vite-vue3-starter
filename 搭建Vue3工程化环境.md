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
       root: true,
       globals: {
           defineEmits: 'readonly',
           defineProps: 'readonly',
       },
       extends: [
           'plugin:@typescript-eslint/recommended',
           'plugin:vue/vue3-recommended',
           'airbnb-base',      
       ],
       parser: 'vue-eslint-parser',
       plugins: [
           '@typescript-eslint',
       ],
       parserOptions: {
           parser: '@typescript-eslint/parser',
           ecmaVersion: 2020,
       },
       rules: {
           'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
           'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
           'no-bitwise': 'off',
           'no-tabs': 'off',
           'array-element-newline': ['error', 'consistent'],
           indent: ['error', 4, { MemberExpression: 0, SwitchCase: 1, ignoredNodes: ['TemplateLiteral'] }],
           quotes: ['error', 'single'],
           'comma-dangle': ['error', 'always-multiline'],
           'object-curly-spacing': ['error', 'always'],
           'max-len': ['error', 120],
           'no-new': 'off',
           'linebreak-style': 'off',
           'import/extensions': 'off',
           'eol-last': 'off',
           'no-shadow': 'off',
           'no-unused-vars': 'warn',
           'import/no-cycle': 'off',
           'arrow-parens': 'off',
           semi: ['error', 'never'],
           eqeqeq: 'off',
           'no-param-reassign': 'off',
           'import/prefer-default-export': 'off',
           'no-use-before-define': 'off',
           'no-continue': 'off',
           'prefer-destructuring': 'off',
           'no-plusplus': 'off',
           'prefer-const': 'off',
           'global-require': 'off',
           'no-prototype-builtins': 'off',
           'consistent-return': 'off',
           'vue/require-component-is': 'off',
           'prefer-template': 'off',
           'one-var-declaration-per-line': 'off',
           'one-var': 'off',
           'import/named': 'off',
           'object-curly-newline': 'off',
           'default-case': 'off',
           'import/order': 'off',
           'no-trailing-spaces': 'off',
           'func-names': 'off',
           radix: 'off',
           'no-unused-expressions': 'off',
           'no-underscore-dangle': 'off',
           'no-nested-ternary': 'off',
           'no-restricted-syntax': 'off',
           'no-mixed-operators': 'off',
           'no-await-in-loop': 'off',
           'import/no-extraneous-dependencies': 'off',
           'import/no-unresolved': 'off',
           'no-case-declarations': 'off',
           'template-curly-spacing': 'off',
           'vue/valid-v-for': 'off',
           '@typescript-eslint/no-var-requires': 'off',
           '@typescript-eslint/no-empty-function': 'off',
           'no-empty': 'off',
           '@typescript-eslint/no-explicit-any': 'off',
           'guard-for-in': 'off',
           '@typescript-eslint/ban-types': 'off',
           'class-methods-use-this': 'off',
           'no-return-await': 'off',
           'vue/html-indent': ['error', 2],
           'vue/html-self-closing': 'off',
           'vue/max-attributes-per-line': ['warn', {
               singleline: {
                   max: 3,
                   allowFirstLine: true,
               },      
               multiline: {
                   max: 1,
                   allowFirstLine: false,
               },
           }],
           'vue/singleline-html-element-content-newline': 'off',
       },
   }
   
   ```

   eslint 配置是基于 airbnb 规范的（除了缩进是 2 个空格，因为所在团队比较喜欢 2 个空格

   ### 

## 提交规范

 