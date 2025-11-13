

## React 18 有哪些更新


- 并发模式
- 更新 render API
- 自动批处理
- Suspense 支持 SSR
- startTransition
- useTransition
- useDeferredValue
- useId
- 提供给第三方的 Hook


## JSX 是什么，他和 JS 有什么区别


**JSX 是 JavaScript 语法的扩展，它允许编写类似于 HTML 的代码。它可以编译为常规的 JavaScript 函数调用，从而为创建组件标记提供了一种更好的方法。**

**JSX 代码：**

```jsx
<div className="sidebar" />
```

**转为 JS 代码：**

```js
React.createElement(
	'div',
	{ className: 'sidebar' }
)
```



## 简述 React 生命周期



### Class 组件生命周期（传统）

#### 1. 挂载阶段（Mounting）

- `constructor()` 初始化 state 和 绑定方法
- `static getDerivedStateFromProps()`（不常用）：根据 props 更新 state
- `render()`：必须实现：返回 JSX
- `componentDidMount()`：组件挂载后调用（适合：发起网络请求、订阅事件、操作 DOM）

#### 2. 更新阶段（Updating）

- `static getDerivedStateFromProps()`
- `shouldComponentUpdate()：返回布尔值，决定是否重新渲染（性能优化）
- `render()`
- `getSnapshotBeforeUpdate()`（不常用）：获取更新前的 DOM 状态（如滚动位置）
-  `componentDidUpdate()`：**更新后调用**（适合：操作更新后的 DOM、发起新请求）

#### 3. 卸载阶段（Unmounting）

- `componentWillUnmount()`：组件销毁前调用（适合：清理定时器、取消订阅、移除事件监听）

**已废弃的生命周期**（React 16.3+）：  
`componentWillMount`、`componentWillUpdate`、`componentWillReceiveProps`  
（因异步渲染可能导致问题）


### Hooks 组件生命周期（现代）

Hooks 用 `useEffect` 统一代替 Class 生命周期：


| Class 生命周期             | Hooks 等效写法                                      |
| ---------------------- | ----------------------------------------------- |
| `componentDidMount`    | `useEffect(() => { ... }, [])`                  |
| `componentDidUpdate`   | `useEffect(() => { ... })`（无依赖数组）或指定依赖          |
| `componentWillUnmount` | `useEffect(() => { return () => { ... } }, [])` |

**Hooks 优势**：

- 逻辑复用更简单（自定义 Hooks）
- 避免生命周期割裂（相关逻辑写在一起）


```jsx
// 挂载 + 卸载
useEffect(() => {
  // componentDidMount
  const timer = setInterval(() => {}, 1000);

  return () => {
    // componentWillUnmount
    clearInterval(timer);
  };
}, []);

// 更新（依赖变化时）
useEffect(() => {
  // componentDidUpdate
  document.title = `Count: ${count}`;
}, [count]); // 依赖数组
```



### 生命周期对比

**Class 组件用生命周期方法，Hooks 组件用 `useEffect` + 依赖数组**，  
**核心思想不变：在正确时机执行副作用（请求、订阅、DOM 操作）**。

| 阶段   | Class 组件                | Hooks                                     |
| ---- | ----------------------- | ----------------------------------------- |
| 挂载   | `componentDidMount`     | `useEffect(fn, [])`                       |
| 更新   | `componentDidUpdate`    | `useEffect(fn, [deps])`                   |
| 卸载   | `componentWillUnmount`  | `useEffect(() => { return cleanup }, [])` |
| 条件更新 | `shouldComponentUpdate` | `React.memo`（包裹组件）                        |




## React 路由缓存


### 什么是路由缓存 ？

**路由缓存是指在 单页应用（SPA）中，切换路由时保留组件状态（如滚动位置、表单位置、加载数据），避免重渲染导致状态丢失、从而提升用户体验**

**示例：**

- 从「商品列表页」进入「商品详情页」，返回时列表保持原滚动位置；
- 表单填写一半跳转，返回后内容不丢失。

Vue 原生支持（`keep-alive`）

Vue Router 与 Vue 的响应式系统深度集成，**通过 `<keep-alive>` 内置组件轻松实现缓存**：
```vue
<router-view v-slot="{ Component }">
  <keep-alive :include="['ProductList']">
    <component :is="Component" />
  </keep-alive>
</router-view>
```

**特点：**

**本质**：Vue 的缓存是**基于组件实例的内存保留**。

- **组件实例被缓存**（不销毁 `data`、`mounted` 等）；
- **`activated` / `deactivated`** 生命周期钩子控制缓存行为；
- **动态 `include`** 可编程控制哪些路由需要缓存；
- **不同参数路由自动隔离**（如 `/user/1` 和 `/user/2` 视为不同组件）。

### React 的路由缓存（原生无，手动实现）


**三种方案：**

- 状态提升 + 路由参数作为 Key（中小项目）
- 自定义缓存容器（模拟 `keep-alive`）
- 使用第三方库（推荐生产环境，[react-activation - npm](https://www.npmjs.com/package/react-activation)）


#### 方案一（状态提升）

- 原理：将组件状态提升到父组件或全局组件状态（如 Redux 、Zustand），路由切换时状态不丢失
- 关键点：用路由参数作为组件 `key`，确保不同参数视为不同实例

**优点**：简单、可控、符合 React 哲学  
**缺点**：需手动管理状态，复杂组件状态结构难维护

```jsx
// 父组件（缓存状态）
function App() {
  const [listState, setListState] = useState({});

  return (
    <Routes>
      <Route 
        path="/list/:id" 
        element={<ListPage key={id} id={id} state={listState[id]} setState={s => setListState(prev => ({...prev, [id]: s}))} />} 
      />
    </Routes>
  );
}

// ListPage 组件
function ListPage({ id, state, setState }) {
  const [data, setData] = useState(state?.data || []);
  // 状态通过 props 传递，不受组件卸载影响
}
```


#### 2. 方案二

- 原理：用 `Map` 存储组件的 React Element 或 **状态快照**，切换路由时隐藏/显示而非销毁

**优点**：组件实例常驻，状态自动保留  
**缺点**：
- 内存泄漏风险（需手动清理缓存）
- 无法响应外部状态变化（如全局 theme 切换）
- Hooks 闭包陷阱（缓存的组件使用旧的 props/state）

```jsx
// 缓存容器组件
const RouteCache = ({ children, routeKey }) => {
  const [cache] = useState(new Map());

  if (!cache.has(routeKey)) {
    cache.set(routeKey, children);
  }

  return <>{cache.get(routeKey)}</>;
};

// 使用
<Route
  path="/detail/:id"
  element={
    <RouteCache routeKey={location.pathname}>
      <DetailPage />
    </RouteCache>
  }
/>
```


#### 3. 方案三

[react-activation - npm](https://www.npmjs.com/package/react-activation)：最接近 Vue `<keep-alive>` 的实现
[react-router-cache-route - npm](https://www.npmjs.com/package/react-router-cache-route)：专为 React Router 设计


**优点**：开箱即用，支持缓存/激活生命周期  
**缺点**：增加包体积，需额外学习 API


```jsx
// 使用 react-activation
import { KeepAlive, AliveScope } from 'react-activation';

function App() {
  return (
    <AliveScope>
      <Routes>
        <Route 
          path="/list" 
          element={
            <KeepAlive name="ListPage">
              <ListPage />
            </KeepAlive>
          } 
        />
      </Routes>
    </AliveScope>
  );
}
```


### Vue 和 React 路由缓存的核心区别

**Vue 通过 `<keep-alive>` 原生支持路由缓存，而 React 因其“状态驱动”哲学需手动实现——要么提升状态到全局，要么用第三方库模拟缓存，核心在于平衡内存占用与用户体验。**

| 维度        | Vue                       | React                |
| --------- | ------------------------- | -------------------- |
| **原生支持**  | `<keep-alive>`内置          | 无，手动实现               |
| **缓存单位**  | 组件实例（内存保留）                | 状态（需提升）或 Element（缓存） |
| **状态隔离**  | 自动（不同路由参数视为不同组件）          | 需手动用 `key` 隔离        |
| **生命周期**  | `activated`/`deactivated` | 无，需自定义 Hook          |
| **内存管理**  | 自动（超出`max`时 LRU 淘汰）       | 需手动清理（否则内存泄漏）        |
| **实现复杂度** | 低（几行代码）                   | 高（需设计状态管理或缓存策略）      |
1. **优先用状态提升**：  

    对于简单状态（如表单、列表数据），用 **Zustand / Redux Toolkit** 管理，比缓存组件更可靠。

2. **复杂场景用第三方库**：

    如需保留完整组件状态（如富文本编辑器），使用 `react-activation`。

3. **避免过度缓存**：  
    缓存太多页面会导致内存暴涨，建议限制缓存数量（如最多 3 个页面）。

4. **结合路由参数动态清理**：

```js
useEffect(() => {
  return () => {
    // 离开页面时清理缓存
    clearCache(location.pathname);
  };
}, [location.pathname]);
```