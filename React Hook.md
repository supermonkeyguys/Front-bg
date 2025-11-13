

## React hooks 解决了什么问题


### 1. 函数组件无法使用 state 和 生命周期

- Class 组件：可通过 `this.state` 和 生命周期方法管理状态和副作用
- 函数组件（Hooks 前）：纯函数，无状态，无生命周期，只能做展示型组件
- Hooks 解决方案：
	- `useState` -> 管理状态
	- `useEffect` -> 替代生命周期（`componentDidMount`/`componentDidUpdate`/`componentWillUnmount`）

### 2. 逻辑复用困难（HOC / Render Props 的嵌套地狱）

- Class 时代复用逻辑：
	- 高阶组件（HOC）：`withAuth(withLogger(Component))`
	- Render Props：`<DataProvider render={data => <Child data={data} />} />`
- 问题：组件嵌套深、props 层层传递、调试困难
- Hooks 解决方案：
	- 自定义 Hook：将逻辑抽离为函数（如 `useAuth()`、`useFetch()`）
	- 扁平化调用：直接在函数组件中使用、无嵌套

```js
// 自定义 Hook 示例
function useCounter(initialValue) {
  const [count, setCount] = useState(initialValue);
  const increment = () => setCount(c => c + 1);
  return { count, increment };
}

// 使用
function App() {
  const { count, increment } = useCounter(0);
  return <button onClick={increment}>{count}</button>;
}
```


### 3. 相关逻辑被生命周期割裂


#### Class 组件示例：

- 订阅和取消订阅逻辑分散在三个生命周期中。

```js
class FriendStatus extends React.Component {
  componentDidMount() {
    ChatAPI.subscribe(this.props.friend.id);
  }
  componentDidUpdate(prevProps) {
    ChatAPI.unsubscribe(prevProps.friend.id);
    ChatAPI.subscribe(this.props.friend.id);
  }
  componentWillUnmount() {
    ChatAPI.unsubscribe(this.props.friend.id);
  }
}
```


#### Hooks 解决方案：

- **相关逻辑聚合**，更易理解和维护。

```js
function FriendStatus({ friend }) {
  useEffect(() => {
    ChatAPI.subscribe(friend.id);
    return () => ChatAPI.unsubscribe(friend.id); // 清理函数
  }, [friend.id]); // 依赖数组
}
```


### 函数组件 vs 类组件

| 特性      | 函数组件（+ Hooks）                        | 类组件                                                     |
| ------- | ------------------------------------ | ------------------------------------------------------- |
| 状态管理    | `useState`，`useReducer`              | `this.state`                                            |
| 副作用     | `useEffect`                          | `componentDidMount` 等生命周期                               |
| 性能优化    | `React.memo`、`useCallback`、`useMemo` | `shouldComponentUpdate`，`PureComponent`                 |
| 逻辑复用    | 自定义 Hook（扁平、无嵌套）                     | HOC / Render Props（嵌套地狱）                                |
| 代码体积    | 更小（无 class 语法开销）                     | 较大                                                      |
| this 指向 | 无 `this`，避免绑定问题                      | 需手动绑定 `this`（如 `onClick={this.handleClick.bind(this)}`） |
| 未来趋势    | **React 官方推荐**，新特性优先支持               | 逐渐被取代（但会长期支持）                                           |

### Hooks 的额外优势

#### 1. 更易测试

- 自定义 Hook 是纯函数，可独立于组件测试
- 无需模拟 class 实例或生命周期

#### 2. 更小的打包体积

- 函数组件编译后代码更少（无 class 语法糖）
- Tree-shaking 友好（未使用的 Hook 不会打包）

#### 3. 与函数式编程范式契合

- 避免 `this` 的复杂性
- 组合优于继承（通过 Hook 组合能力）

### 注意事项（Hooks 规则）

1. **只能在函数组件顶层调用**
    - 不能在循环、条件、嵌套函数中调用。
2. **只能在 React 函数中调用**
    - 不能在普通 JS 函数中调用（自定义 Hook 除外）。
3. **依赖数组需完整**
    - `useEffect` / `useCallback` 的依赖必须包含所有外部变量，否则可能闭包陷阱。