


## Node 事件循环



### Node.js 事件循环的 6 个阶段

**关键规则**：

- **每个阶段执行完所有回调后，才进入下一阶段**
- **微任务（Promise）在每个阶段结束后执行**（包括 timers 、poll 、check 等）

| 阶段                       | 执行内容                                                       | 关键 API                                |
| ------------------------ | ---------------------------------------------------------- | ------------------------------------- |
| 1. Timers                | 执行 `setTimeout` / `setInterval` 的回调（到达指定时间）                | `setTimeout`、`setInterval`            |
| 2. Pending I/O Callbacks | 执行系统操作的回调（如 TCP 错误、文件 I/O）                                 | `fs.readFile`（部分情况）                   |
| 3. Idle ，Prepare         | 内部使用，开发者不可见                                                | -                                     |
| 4. Poll（轮询）              | 核心阶段：执行 I/O 回调（如 `fs`、网络请求）处理新的 I/O 事件，若 timers 到期，进入下一个循环 | `fs`、`net`、`http`                     |
| 5. Check                 | 执行 `setImmediate` 回调                                       | `setImmediate`                        |
| 6. Close Callbacks       | 执行关闭事件的回调（如 `socket.on('close',...)`)                      | `socket.destory()`、`emitter.closse()` |



### 与浏览器事件循环比较


| 特性             | Node.js                            | 浏览器                                       |
| -------------- | ---------------------------------- | ----------------------------------------- |
| 驱动库            | libuv                              | HTML5 Event Loop（每个浏览器实现不同）               |
| 阶段划分           | 6 个阶段                              | 无明确阶段，只有任务队列                              |
| 宏任务类型          | `setTimeout`、`setImmediate`、I/O 回调 | `setTimeou`、`requestAnimationFrame`、UI 事件 |
| 微任务执行          | 每个阶段执行所有微任务                        | 每个宏任务后执行所有微任务                             |
| `setImmediate` | 支持（Check 阶段）                       | 不支持（部分 polyfill）                          |
| 渲染             | 无                                  | 有（每 16ms 帧）                               |



### 事件循环阶段执行顺序

**Node.js 事件循环由 libuv 驱动，分为 6 个阶段，微任务在每个阶段结束后执行，`process.nextTick` 优先级最高，`setImmediate` 保证在 I/O 后执行。**

1. **同步代码**执行
2. **`process.nextTick` 队列**清空
3. **微任务队列**（Promise, `queueMicrotask`）清空
4. **进入事件循环**：
    - **Timers 阶段** → 执行到期的 `setTimeout`/`setInterval`
    - **微任务**执行
    - **Pending I/O** → 系统回调
    - **微任务**执行
    - **Poll 阶段** → I/O 回调
    - **微任务**执行
    - **Check 阶段** → `setImmediate`
    - **微任务**执行
    - **Close 回调** → 关闭事件
    - **微任务**执行
5. **循环回到 Timers 阶段**



- IPC 通信会触发双方的事件循环