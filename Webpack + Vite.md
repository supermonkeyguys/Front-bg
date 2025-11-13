


# Webpack 


## Webpack 原理


**Webpack 是一个静态模块打包器（Static Module Bundler），它的核心目标是：**
##### **将项目中分散的模块（JS、CSS、图片等）及其依赖关系，分析、转换、合并，最终输出浏览器可运行的静态资源。**


### 核心思想：一切皆模块（Everything is a Module）

Webpack 打破了传统“JS 是脚本”的观念，认为：

- JavaScript 文件是模块（ESM / CommonJS）
- CSS 是模块（`import './style.css'`）
- 图片是模块（`import logo from './logo.png'`）
- 甚至 HTML 模板、字体文件都可以是模块

> ✅ **模块 = 可被 import/require 的资源**



### 四大核心概念


#### 1. Entry（入口）

- Webpack 从哪个文件开始分析依赖
- 可以是但入口（SPA）或 多入口（MPA）

```js
// webpack.config.js
module.exports = {
	entry: './src/index.js' // 从这开始构建依赖图
}
```

#### 2. Output（输出）

- 打包后文件输出的路径和名称

```js
output: {
	path: path.resolve(__dirname, 'dist')
	filename: 'bundle.js'
}
```

#### 3. Loader（加载器）

- **作用**：将 非 JS 模块 转为 JS 模块（因为 Webpack 只认识 JS）
- **工作方式**：从左到右，从下到上链式调用

```js
module: {
  rules: [
    {
      test: /\.css$/,
      use: ['style-loader', 'css-loader'] // 先 css-loader → 再 style-loader
    },
    {
      test: /\.js$/,
      use: 'babel-loader' // 转换 ES6+ 语法
    }
  ]
}
```

- 常见 Loader：
    - `css-loader`：解析 `@import` 和 `url()`
    - `style-loader`：将 CSS 注入 `<style>` 标签
    - `file-loader` / `url-loader`：处理图片、字体
    - `babel-loader`：转译 JavaScript

#### 4. Plugin（插件）

- **作用**：在 Webpack 构建生命周期的特定时机注入自定义功能（比 Loader 更强大）
- **可操作整个构建过程**（如：优化、压缩、生成文件等）

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

plugins: [
  new HtmlWebpackPlugin({
    template: './src/index.html' // 自动生成 HTML 并注入 bundle
  }),
  new webpack.DefinePlugin({
    'process.env.NODE_ENV': JSON.stringify('production')
  })
]
```

- 常见 Plugin：
    - `HtmlWebpackPlugin`：生成 HTML
    - `MiniCssExtractPlugin`：提取 CSS 到单独文件
    - `DefinePlugin`：定义全局常量
    - `CleanWebpackPlugin`：清空输出目录


### Webpack 构建流程（5个核心阶段）


#### 阶段一：初始化（Initialization）

- 读取 `webpack.config.js` 配置
- 创建 `compiler` 对象（全局单例，贯穿整个构建生命周期）

#### 阶段二：编译（Compilation）

- 从 `entry` 开始，递归解析依赖，生成 依赖图（Dependency Graph）
	- 通过 AST（抽象语法树），分析 `import` / `require`
	- 对应每个模块调用对应的 Loader 进行转换
- 生成 `compilation` 对象（包含本次构建的所有模块、依赖、资源）

**依赖图示例**：

``` plainext
index.js
├── utils.js
│   └── lodash.js
└── style.css
    └── bg.png
```

#### 阶段三：优化（Optimization）

- **Tree shaking**：移除未引用的 ES6 代码（需 `mode: 'production`）
- **Scope Hoisting**：将多个模块合并到一个函数中，减少闭包开销
- **代码分割（Code Splitting）**：通过 `SplitChunksPlugin` 拆分公共代码
- **压缩**：Terser 压缩 JS 代码， CssMinimizer 压缩 CSS


#### 阶段四：生成资源（Emit Assets）

- 将 module 转换为 chunk（代码块）
- 根据 `output: filename` 生成最终文件（如 `bundle.js`）
- Plugin 在此阶段可修改输出（如 `HtmlWebpackPlugin` 注入 script 标签）


#### 阶段五：输出（Output）

- 将最总的文件写入磁盘（或内存，如 `webpack-dev-server`）



#### 关键机制


##### 1. 模块解析（Module Resolution）

- Webpack 使用 enhanced-resolve 库解析路径
- 支持别名（`alias`）、扩展名（`extensions`）、模块目录（`modules`）

```js
resolve: {
  alias: {
    '@': path.resolve(__dirname, 'src')
  },
  extensions: ['.js', '.jsx', '.json']
}
```

##### 2. Hot Module Replacement （HMR）

- 开发时替换模块而不刷新页面
- 原理：
	1. Webpack Dev Server 监听文件变化
	2. 将更新的模块通过 WebSocket 推送给浏览器
	3. 运行时接管模块更新逻辑（需手动编写 `module.hot.accept`）


##### 3. Code Splitting（代码分割）

- 三种方式：
	1. 入口分割：配置多个 `entry`
	2. 动态导入：`import()` 语法（推荐）
	3. SplitChunksPlugin：自动提取公共依赖

```js
optimization: {
  splitChunks: {
    chunks: 'all',
    cacheGroups: {
      vendor: {
        test: /[\\/]node_modules[\\/]/,
        name: 'vendors',
        chunks: 'all'
      }
    }
  }
}
```




### 其他工具相比


**Webpack 优势**：生态完善（5000+ Loader/Plugin），适合大型复杂项目。

| 工具      | 定位      | 特点                         |
| ------- | ------- | -------------------------- |
| Webpack | 模块打包工具  | 复杂生态，Loader / Plugin 机制强大  |
| Vite    | 新一代构建工具 | 基于 ES Module 原生支持，开发启动快    |
| Rollup  | 库打包器    | Tree shaking 更优，适合打包 npm 包 |
| Parcel  | 零配置打包器  | 开箱即用，但是定制性弱                |

**Webpack 从入口文件出发，递归构建依赖图，通过 Loader 转换非 JS 模块，利用 Plugin 在构建生命周期中注入自定义逻辑，最终输出优化后的静态资源。**




# Vite 


前端构建工具

**利用现代浏览器原生支持的 ES 模块（ESM）能力，在开发阶段实现“按需编译”，彻底告别打包式开发服务器的等待。**


## Vite 核心原理



### 1. 开发阶段：基于原生 ESM 的 "按需加载"

- **传统打包工具（如 Webpack）**：启动时先打包整个应用 -> 再启动 Dev Server -> 修改文件后重新打包受影响部分（即使只改一行，也可能耗时几秒）

- **Vite**：
	- 启动时不做打包，直接启动一个开发服务器（基于 esbuild + Rollup）
	- 浏览器请求 `index.html` -> 服务器返回 HTML
	- HTML 中的 `<script type="module" src="/src/main.js">` 触发浏览器原生 ESM 加载
	- Vite 服务器拦截这些 ESM 请求，实时编译（如 TS -> JS 、Vue SFC -> JS）并返回
	- 依赖（node_modules）：启动时用 esbuild 预构建（极快，因为 esbuild 是 GO 写的），后续直接提供 ESM 格式
	- 源码（src/）：按需编译，只编译当前页面用到的模块

>**冷启动** **毫秒级**（与项目大小无关）
  **热更新** **瞬间完成**（只更新修改的模块）




### 生产构建：基于 Rollup 打包

- Vite 不自己实现生产打包，而是调用 Rollup （因其 Tree Shaking 和代码分割更优）
- 通过统一的插件 API，开发和生产使用同一套插件（如：`@vitejs/plugin-vue)

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
	plugins: [vue()],
	build: {
		// 生产构建工具（底层是 Rollup）
		target: 'es2015',
		minify: 'terser',
	}
})
```



### 核心技术点


#### 1. 依赖预构建（Dependency Pre-Bundling）

- 问题：`node_modules` 中的库通常是 CommonJS 或 UMD 格式，浏览器无法直接运行
- Vite 解决方案：
	1. 启动时扫描 `import` 语句，找出依赖（如 `vue`，`lodash`）
	2. 用 esbuild 将它们转化为 ESM 格式，并合并高频依赖（减少 HTTP 请求）
	3. 存到 `node_modules/.vite` 缓存，下次启动直接复用

#### 2. 按需编译（On-Demand Compilation）

- 只有当浏览器请求某个模块时（如 `/src/App.vue`），Vite 才会编译它
- 修改文件后，通过 WebSocket 通知浏览器重新请求该模块（HMR）


#### 3. 插件系统

- 兼容 Rollup 插件（大部分可以直接用）
- 提供 Vite 特有钩子（如 `configureServer` 有用于扩展 Dev Server）
- 官方插件：Vue、React、Preact、Lit 等一应俱全


## 对比 Webpack


**Vite 的哲学:  开发环境追求极致速度，生产环境追求极致优化**

| 特性        | Vite                      | Webpack                       |
| --------- | ------------------------- | ----------------------------- |
| 开发服务器启动速度 | 极快                        | 慢                             |
| 热更新速度     | 瞬间（只更新模块）                 | 较慢（需重新构建依赖图）                  |
| 依赖预构建     | 用 esbuild （Go 编写，块）       | 无，每次解析 node_modules           |
| HMR 实现    | 原生 ES + Websocket         | 自定义模块热替换协议                    |
| 生产构建      | Rollup（轻量、Tree Shaking 优） | Webpack（功能全、生态大）              |
| 配置复杂度     | 简单（约定优于配置）                | 复杂（需理解 loader / plugin）       |
| 浏览器兼容性    | 需支持 ESM （Chrome 61+）      | 支持 IE 11（通过 Babel / polyfill） |
| 生态        | 快速增长（官方 + 社区插件）           | 大（5000 +loader/ plugin）       |

**Webpack 是“先打包再运行”，Vite 是“边运行边编译”**。  
Vite 利用现代浏览器的 ESM 能力，彻底重构了开发体验，成为下一代构建工具的标杆。

### 适用场景


### **强烈推荐使用 Vite 的场景**

- 新项目（尤其是 Vue 3 / React 18+）
- 中小型应用（追求开发体验）
- 现代浏览器环境（无需兼容 IE）
- 需要快速原型开发

### ⚠️ **谨慎使用 Vite 的场景**

- 需要兼容 IE11
- 项目重度依赖 Webpack 特有 loader/plugin（如 `file-loader` 的特殊用法）
- 超大型遗留项目迁移成本高