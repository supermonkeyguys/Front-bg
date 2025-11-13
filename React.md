

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
