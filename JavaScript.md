

## 数据类型

基本数据类型：`string`(字符串)、`number`(数字) `boolean`(布尔值) `undefined`(未定义) `null`(空值) `symbol`(符号，ES6 新增，唯一且不可变)、bigInt

引用数据类型：`object`(对象，包括普通对象、数组、日期、正则等)、`fucntion`


## 类型隐式转换

当运算符、函数或语句需要特定类型的值时，JS 引擎自动将不同类型的值转换为目标类型的过程。这种转换是 JS 弱类型特性的体现，核心规则集中在 `number` `string` `boolean` 之间的转换

**隐式转换的触发场景**

最常见的触发场景是 **运算符操作**（如 `+` `==` `if` 条件判断等），不同运算符会触发不同的转换规则：

1. 算术运算符（`+`、`-` 、`*`、`/`、`%`）

	- `-` `*` `/` `%`：一律将操作数转换为 **数字** 后计算

```JavaScript
console.log(5 - '3'); // 2（'3' 转为数字 3，5-3=2）
console.log('10' * 2); // 20（'10' 转为 10，10*2=20）
console.log(8 / '2'); // 4（'2' 转为 2）
console.log(5 % '2'); // 1（'2' 转为 2）
```

   - `+` ：规则特殊，分两种情况：

   - 若有一个操作数是**字符串**，则另一个操作数会转为字符串，执行**拼接**；

   - 否则，所有操作数转为**数字**，执行加法。

```JavaScript
// 字符串拼接（有一个操作数是字符串）
console.log('5' + 3); // "53"（3 转为 "3"，拼接）
console.log(5 + '3'); // "53"（5 转为 "5"，拼接）
console.log(true + 'a'); // "truea"（true 转为 "true"，拼接）

// 数字加法（无字符串时）
console.log(true + 1); // 2（true 转为 1，1+1=2）
console.log(null + 5); // 5（null 转为 0，0+5=5）
console.log(undefined + 1); // NaN（undefined 转为 NaN）
```

2. 相等运算符（`==`，非严格相等） 

`==` 会先将两边的值 **隐式转换为同一类型**，再比较是否相等（`===` 不会转换，直接比较类型和值）。

**核心转换规则**：

- 若一边为数字，另一边是字符串：字符串转为数字
- 若一边为布尔值：布尔值转为数字（`true`- 1，`false`-0）
- 若一边为对象，另一边是基本类型：对象转为基本类型（调用 `valueOf()` 或 `toString()`）
- `null == undefined` 为 true（特殊规则），但是它们与其他值比较均为 `false`

```JavaScript
console.log(5 == '5'); // true（'5' 转为 5）
console.log(true == 1); // true（true 转为 1）
console.log(false == 0); // true（false 转为 0）
console.log(null == undefined); // true（特殊规则）
console.log([] == 0); // true（[] 转为 ""，再转为 0）
console.log({} == '[object Object]'); // true（{} 转为 "[object Object]"）
```


3. 判断逻辑（`if`、`&&`、`||`、`!`）

条件判断中，值会被隐式转换为 布尔值 （遵循 假值 和 真值 规则）

- 假值：(转换为 `false`)：`0` 、`""` 、`null`、`undefined`、`NaN`、`false`
- 真值：(转换为 `true`)：除假值外的所有值（包括 `1`、`""`、`[]`、`{}`等）

```JavaScript
if (0) { console.log('执行'); } // 不执行（0 是假值）
if ('') { console.log('执行'); } // 不执行（空字符串是假值）
if ([]) { console.log('执行'); } // 执行（数组是真值）
if ({}) { console.log('执行'); } // 执行（对象是真值）

// 逻辑运算符
console.log(0 || '默认值'); // "默认值"（0 是假值，返回后者）
console.log('有值' && '结果'); // "结果"（前者是真值，返回后者）
console.log(!0); // true（0 转为 false，取反为 true）
```


## var 、let 、const 区别

### var

在 ES5 中，顶层对象的属性和全局变量是等价的，用 `var` 声明的变量既是全局变量，也是顶层变量

注意：顶层对象，在浏览器环境中指的是 `window` 对象，在 `Node` 指的是 `global` 对象

- 存在 **变量提升**
- 能够对一个变量进行多次声明，后面声明的变量会覆盖先前命名的变量声明
- 在函授中使用 `var` 声明变量的时候，该变量是局部的
- 而如果在函数内不使用 `var`，该变量是全局的

### let

- 用法类似于 `var`，但是所声明的变量，只在 `let` 命令所在的代码块内有效
- 不存在变量提升
- 只要在块级作用域内存在 `let` 命令，这个区域就不再受外部的影响
- `let` 不允许在相同作用域中重复声明
- 不能在函数内部重新声明参数
‘
### const

- `const` 声明一个只读的变量，一旦声明，常量的值就不能改变
- `const` 一旦声明变量，就必须立即初始化，不能留到以后赋值
- 如果之前用 `var` 或 `let` 声明过变量，再用 `const` 声明同样会报错
- `const` 实际上保证的并不是变量的值不得改动，而是**变量所指向的内存地址不可改变**


## 作用域

变量（变量作用域又称作上下文）和函数生效（能被访问）的区域或集合。作用域决定了代码区块中变量和其他资源的可见性

#### **全局作用域**

任何不在函数中或是大括号中声明的变量，都是在全局作用域下，全局作用域下声明的变量可以在程序的任意位置访问

#### 函数作用域

函数作用域也叫做局部作用域，如果一个变量在函数内部声明的它就在一个函数作用域下面。这些变量只能在函数内部访问，不能在函数外部去访问

#### 块级作用域

ES6 引入了 `let` 和 `const` 关键字，和 `var` 关键字不同，在大括号中使用 `let` 和 `const` 声明的变量存在于块级作用域中。在大括号之外不能访问这些变量

```JavaScript
{
  // 块级作用域中的变量
  let greeting = 'Hello World!';
  var lang = 'English';
  console.log(greeting); // Prints 'Hello World!'
}
// 变量 'English'
console.log(lang);
// 报错：Uncaught ReferenceError: greeting is not defined
console.log(greeting);
```

#### 作用域链

当在 `JavaScript` 中使用一个变量的时候，首先 `JavaScript` 引擎会尝试在当前作用域下去寻找该变量，如果没有找到，再找到它的上层作用域寻找，以此类推直到找到该变量或是已经到了全局作用域

如果在全局作用域里仍然找不到该变量，他就会在全局范围内隐式声明该变量（非严格模式下）或是直接报错

```JavaScript
var sex = '男';
function person() {
    var name = '张三';
    function student() {
        var age = 18;
        console.log(name); // 张三
        console.log(sex); // 男 
    }
    student();
    console.log(age); // Uncaught ReferenceError: age is not defined
}
person();
```



## 原型链

原型链（Prototype Chain）是实现继承和属性共享的核心机制，也是理解 JS 面向对象特性的关键点。解决了属性和方法复用的问题

### **原型链核心概念**

1. 实例：通过构造函数创建的对象（如 `new Person()` 生成的 `person1`）
2. 原型对象（Prototype）：每个函数（尤其是构造函数）都有 `prototype` 属性，指向一个对象（原型对象），用于存储所有实例共享的属性和方法
3. `__proto__`属性：每个实例对象都有 `__proto__`属性（非标准但是浏览器普遍支持），指向其构造函数的 `prototype`（即实例的 "原型"）
4. 关系为：实例.`__proto__` === 构造函数.`prototype`

### **链的形成**

当访问一个对象的属性 / 方法时，JS 会先在对象自身查找；若找不到，则通过 `__proto__` 去其原型链上查找；若仍然找不到，继续通过对象的原型对象的 `__proto__` 向上查找... 直到找到 `null`（原型链的终点）。这种 "**对象 -> 原型 -> 原型的原型 -> ... -> null**" 的链式结构，就是 **原型链**

##### 查找规则：链式追溯

#### 核心作用：实现继承

**JS 的继承本质是通过 "原型链" 实现的** ---- 子构造函数的原型对象指向父构造函数的实例，从而继承父类的属性和方法

**constructor**

`constructor` 是原型对象（`prototype`）上的一个特殊属性。`constructor` 的值是一个函数引用，指向 **该原型对象所属的构造函数**

```JavaScript
// 定义构造函数
function Person(name) {
  this.name = name;
}

// 构造函数的 prototype 上有 constructor 属性
console.log(Person.prototype.constructor === Person); // true

// 通过构造函数创建实例
const person1 = new Person('Alice');

// 实例的原型（通过 __proto__ 访问）指向构造函数的 prototype
console.log(person1.__proto__ === Person.prototype); // true

// 实例可通过原型链访问到 constructor，指向构造函数
console.log(person1.constructor === Person); // true
```

`constructor` **可判断实例是由哪个构造函数创建的**，但由于 `constructor` 是可以被修改的，不是只读属性，所以判断类型时最好不使用 `constructor`，使用 `typeof` 或者 `instanceof`

```JavaScript
 console.log(person1.constructor === Person); // true（明确是 Person 类型）
console.log([].constructor === Array); // true（数组实例的构造函数是 Array）
console.log({}.constructor === Object); // true（对象实例的构造函数是 Object）

const arr = []; arr.constructor = Object; // 手动修改 
console.log(arr.constructor === Array); // false（已被篡改） 
console.log(arr instanceof Array); // true（instanceof 更可靠）
```

 为避免以上情况发生，我们还可以在手动修改原型对象的时候，手动修复原型指向

```JavaScript
 // 错误示例：修改原型后，constructor 指向被覆盖的对象
Person.prototype = {
  sayHello() { console.log('Hello'); }
};
console.log(Person.prototype.constructor === Person); // false（此时指向 Object）

// 正确做法：手动修复 constructor 指向
Person.prototype = {
  constructor: Person, // 显式指向原构造函数
  sayHello() { console.log('Hello'); }
};
console.log(Person.prototype.constructor === Person); // true
```

**常见问题与注意事项**

1. 属性屏蔽：若实例自身存在与原型链中同名的属性，会优先使用自身属性（屏蔽原型链中的属性）
2. 原型链修改影响所有实例：修改原型对象的属性 / 方法，会影响所有基于该原型创建的实例（共享特性）
3. `null` 是原型链的终点：`Object.prototype.__proto__ === null`，表示 "没有更上层的原型" 查找至此结束（返回 `undefined`）
4. `instanceof` 基于原型链判断：`A instanceof B` 的原理是 "检查 B 的 prototype 是否在 A 原型链上"

**总结**

- 一切对象都是继承自 `Object` 对象，`Object` 对象直接继承根源对象 `null`
```js
Object.prototype.__proto__ === null
```

- 一切的函数对象（包括 `Object` 对象），都是继承自 `Function` 对象

- `Object` 对象直接继承自 `Function` 对象
```js
Object.__proto__ === Function.prototype
```

- `Function` 对象的 `__proto__` 会指向自己的原型对象，最终还是继承自 `Object` 对象


## 继承


### 传统继承方式


在 ES6 之前 JS 没有 "类" 的概念，继承本质是通过原型链和构造函数调用实现的

#### 原型链继承

**原理**：将子类的 `prototype` 指向父类的实例，让子类实例通过原型链访问父类的属性和方法

**缺点**：

- 父类的 **引用类型实例属性** 会被所有子类实例共享（如上诉：`arr`），一个实例修改会影响所有实例
- 子类实例化时无法向父类构造函数传参（如 `Dog` 无法直接给 `Animal` 的 `name` 传值）

```JavaScript
// 父类：动物
function Animal(name) {
  this.name = name; // 实例属性
  this.arr = [1, 2, 3]; // 引用类型实例属性
}
Animal.prototype.eat = function() { // 原型方法
  console.log(`${this.name} 吃食物`);
};

// 子类：狗（原型链继承）
function Dog() {}
Dog.prototype = new Animal(); // 子类原型指向父类实例
Dog.prototype.constructor = Dog; // 修复 constructor 指向（否则指向 Animal）

// 测试
const dog1 = new Dog("阿黄");
const dog2 = new Dog("小黑");
dog1.eat(); // 阿黄 吃食物（复用父类方法）
dog1.arr.push(4);
console.log(dog1.arr); // [1,2,3,4]
console.log(dog2.arr); // [1,2,3,4]（引用类型属性被所有子类实例共享，踩坑点！）
```


#### 构造函数继承

**原理**：在子类构造函数中，通过 `call` / `apply` 调用父类构造函数，强制将父类的实例属性绑定到子类实例中

**缺点**：无法复用父类的 **原型方法**（如 `eat`），每个子类实例都会重新创建父类构造函数中的方法（若父类有方法定义在构造函数内，会造成内存浪费）

```JavaScript
// 父类：动物
function Animal(name) {
  this.name = name;
  this.arr = [1, 2, 3];
}
Animal.prototype.eat = function() {
  console.log(`${this.name} 吃食物`);
};

// 子类：狗（构造函数继承）
function Dog(name) {
  Animal.call(this, name); // 调用父类构造函数，绑定 this 为子类实例
}

// 测试
const dog1 = new Dog("阿黄");
const dog2 = new Dog("小黑");
dog1.arr.push(4);
console.log(dog1.arr); // [1,2,3,4]
console.log(dog2.arr); // [1,2,3]（引用类型属性不再共享，解决原型链问题）
dog1.eat(); // 报错！（无法访问父类原型方法，因为没走原型链）
```


#### 组合继承（原型链 + 构造函数）

**原理**：结合 "原型链继承"（复用父类原型方法）和 "构造函数继承"（独享父类实例属性），取长补短。

**缺点**：父类构造函数会被调用两次（一次 `new Animal()` 绑定原型，一次 `Animal.call()` 初始化实例），导致子类原型上会冗余一份父类的实例属性（如 `name`、`arr`，虽是子类实例的属性覆盖，但仍存在于原型上，轻微浪费内存）


```JavaScript
// 父类：动物
function Animal(name) {
  this.name = name;
  this.arr = [1, 2, 3];
}
Animal.prototype.eat = function() {
  console.log(`${this.name} 吃食物`);
};

// 子类：狗（组合继承）
function Dog(name) {
  Animal.call(this, name); // 1. 构造函数继承：独享实例属性 + 传参
}
Dog.prototype = new Animal(); // 2. 原型链继承：复用原型方法
Dog.prototype.constructor = Dog; // 修复 constructor

// 测试
const dog1 = new Dog("阿黄");
const dog2 = new Dog("小黑");
dog1.eat(); // 阿黄 吃食物（复用原型方法）
dog1.arr.push(4);
console.log(dog1.arr); // [1,2,3,4]
console.log(dog2.arr); // [1,2,3]（无共享问题）
```


#### 寄生组合继承（优化组合继承的缺陷）

**原理**：对 "组合继承" 的优化 ---- 通过 "寄生方式" 创建父类原型的副本，再将子类 `prototype` 指向该副本，避免父类构造函数被调用两次。

>**实例数据走构造函数（`call`），方法共享走原型链（代理继承）**  
  这就是寄生组合继承被称为“**最理想的继承模式**”的原因。

```JavaScript
// 父类：动物
function Animal(name) {
  this.name = name;
  this.arr = [1, 2, 3];
}
Animal.prototype.eat = function() {
  console.log(`${this.name} 吃食物`);
};

// 工具函数：创建父类原型的副本（寄生核心）
function createProto(Parent) {
  function F() {} // 空构造函数（避免调用父类构造函数）
  F.prototype = Parent.prototype; // 空函数原型指向父类原型
  return new F(); // 返回父类原型的副本实例
}

// 子类：狗（寄生组合继承）
function Dog(name) {
  Animal.call(this, name); // 1. 构造函数继承：仅调用一次父类构造函数
}
const proto = createProto(Animal); // 2. 寄生获取父类原型副本
proto.constructor = Dog; // 修复副本的 constructor
Dog.prototype = proto; // 3. 子类原型指向副本

// 测试
const dog1 = new Dog("阿黄");
dog1.eat(); // 正常复用方法
console.log(Dog.prototype.hasOwnProperty("name")); // false（原型上无冗余属性，优化成功）
```


## ES6 class 继承


ES6 引入 `class` 关键字和 `extends` 语法，本质是对 "**寄生组合继承**" 的 **语法糖**（简化写法，底层逻辑不变），让 JS 继承更贴近传统面向对象语言（如 JAVA）。

```JavaScript
// 父类：Animal（用 class 定义）
class Animal {
  // 构造函数（对应 ES5 的构造函数）
  constructor(name) {
    this.name = name;
    this.arr = [1, 2, 3];
  }

  // 原型方法（自动挂载到 Animal.prototype）
  eat() {
    console.log(`${this.name} 吃食物`);
  }

  // 静态方法（挂载到 Animal 类上，子类无法继承，需通过类名调用）
  static sayHi() {
    console.log("动物类的静态方法");
  }
}

// 子类：Dog（用 extends 继承）
class Dog extends Animal {
  constructor(name, age) {
    // 必须先调用 super()：相当于 ES5 的 Animal.call(this, name)，初始化父类实例属性
    super(name); 
    this.age = age; // 子类独有的实例属性
  }

  // 子类重写父类方法（多态）
  eat() {
    console.log(`${this.name}（${this.age}岁）吃骨头`);
  }

  // 子类静态方法
  static sayHi() {
    super.sayHi(); // 静态方法中用 super 调用父类静态方法
    console.log("狗类的静态方法");
  }
}

// 测试
const dog1 = new Dog("阿黄", 3);
dog1.eat(); // 阿黄（3岁）吃骨头（重写生效）
dog1.arr.push(4);
const dog2 = new Dog("小黑", 2);
console.log(dog2.arr); // [1,2,3]（无共享问题）

// 静态方法调用
Animal.sayHi(); // 动物类的静态方法
Dog.sayHi(); // 动物类的静态方法 → 狗类的静态方法（super 调用父类静态方法）
```



## Promise 


### Promise

`Promise` 是 ES6 引入的异步操作编程解决方案，用于解决传统回调函数嵌套（"回调地狱"）问题，使异步代码的逻辑更清晰、更易于维护

`Promise` 代表一个异步操作的最终完成（或失败）及其结果值，有三种状态：

- pending（等待中）：初始状态，既不是成功也不是失败
- fulfilled（已成功）：异步操作完成，会触发 `then` 方法
- reject（已失败）：异步操作出错，会触发 `catch` 方法

状态一旦改变（从 pending 到 fulfilled 或 rejected），就会永久保持该状态，不会再改变

```JavaScript
// 创建 Promise 实例
const promise = new Promise((resolve, reject) => {
  // 异步操作（如网络请求、定时器等）
  setTimeout(() => {
    const success = true;
    if (success) {
      resolve("操作成功的结果"); // 成功时调用 resolve，状态变为 fulfilled
    } else {
      reject("操作失败的原因"); // 失败时调用 reject，状态变为 rejected
    }
  }, 1000);
});

// 处理结果
promise
  .then(result => {
    console.log("成功：", result); // 接收 resolve 传递的值
  })
  .catch(error => {
    console.log("失败：", error); // 接收 reject 传递的值
  })
  .finally(() => {
    console.log("无论成功失败都会执行"); // 可选，用于清理操作
  });
```


#### 主要方法

1. `then()`：处理成功的结果，返回一个新的 Promise，可链式调用
2. `catch()`：处理失败的原因，也返回新的 Promise，常用于捕获整个链式操作的错误
3. `finally()`：无论成功失败都会执行，不接收参数
4. 静态方法：
	- `Promise.resolve(value)`：快速创建一个成功状的 Promise
	- `Promise.reject(error)`：快速创建一个失败状态的 Promise
	- `Promise.all(iterable)`：将多个 `Promise` 实例，包装一个新的 `Promise` 实例。等待所有 Promise 都成功才返回成功结果数组，只要有一个失败就返回失败。`注意：` 如果作为参数的 `Promise` 实例，自己定义了 `catch` 方法，那么它一旦被 `rejected`，并不会触发 `Promise.all()` 的 `catch()` 方法！
		- `Promise.race(iterable)`：同样是将多个 Promise 实例，包装一个新的 Promise 实例。返回第一个改变状态的 Promise 的结果（无论成功失败）
		- `allSettled()`：接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例只有等到所有这些参数都返回结果，不管是 `fulfilled` 还是 `rejected`，包装实例才会结束。

**优点**：

- **解决回调地狱**：通过链式调用代替嵌套回调，使代码线性化
- **错误处理统一**：可在链式末尾用用 `catch` 捕获所有前面的错误
- **便于异步操作组合**：通过 `all`、`race` 等方法灵活管理多个异步操作


### async / await 

1. **`async` 关键字**：
	- 用于修饰函数，使其返回一个 Promise 对象
	- 函数内部的 `return` 值会被自动包装为 `Promise.resolve(return值)`

2. **`await` 关键字**：
	- 只能在 `async` 函数内部使用
	- 用于等待一个 Promise 对象状态变为 resolved，并获取其结果
	- 会暂停当前 `async` 函数的执行，直到等待的 Promise 完成


```JavaScript
// 异步函数（返回 Promise）
function fetchData() {
	return new Promise(resolve => {
		setTimeout(() => {
			resolve("获取到的数据")
		},1000)
	})
}

// 使用 async/await
async function processData() {
	console.log("开始处理")
	
	// 等待 Promise 完成并获取结果
	const data = await fectchData()
	console.log("获取到数据: ",data)
	
	return "处理完成"
}

// 调用 async 函数（返回 Promise）
processData().then(result => {
  console.log(result); // "处理完成"
});
```

**错误处理**

使用 `try/catch` 捕获异步操作中的错误，替代 Promise 的 `catch()` 方法

```javaScript
async function handleErrors() {
	try {
		const data = await fetchData() // 可能失败的异步操作
		console.log(data)
	} catch(err) {
		console.log("发生错误: ", error) // 捕获所有错误
	}
}
```

**与 Promise 对比的优势**

1. **代码更简洁**：避免 `.then()` 链式调用，逻辑更线性
2. **错误处理更直观**：使用同步风格的 `try/catch`
3. **调试更方便**：可以直接在 `await` 行设置断点
4. **条件判断更自然**：在异步操作之间插入条件逻辑更简单

**注意事项**：

1. `await` 只能在 `async` 函数中使用，否则会报错
2. `async` 函数返回的总是 Promise，即使没有显示返回
3. 多个 `await` 会按顺序执行，如需并行执行应配合 `Promise.all()`
4. 未处理的错误会导致整个 `async` 函数返回的 Promise 变为 rejected



## This 对象

this 的指向

- 在函数调用的时候，JavaScript 会默认给 this 绑定一个值
- this 的绑定和定义的位置（即编写的位置）没有关系
- this 的绑定和调用方式以及调用的位置有关系
- this 是在运行时才被绑定的

### 默认绑定

- 独立调用的函数 this 指向 window，但在严格模式下独立调用的函数中的 this 指向的是 Undefined （"use strict"）

```JavaScript
function person() {
	console.log(this) // 这里 this 指向 window
	return this.name
}

person()
```


### 隐式绑定

函数作为某个对象的方法调用，这时 `this` 为隐式绑定，`this` 指向调用它的位置的 **上一级** 对象

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <script>

    // 例1
    function foo() {
      console.log(this) //这里的this绑定到obj对象
    }
    var obj = {
      bar: foo
    }
    obj.bar() // { bar: [Function: foo] }
    
    //例2
    function foo2() {
        console.log(this.a) // 这里的this绑定到它的上一级b对象，所以this.a为undefined 
    }
    var obj2 = {
        a: 10,
        b: {
	        bar2: foo2
        }
    }
    obj2.b.bar2()
    
  </script>
</body>
</html>
```


**特殊情况**：

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <script>
    
	function foo2() {
		console.log(this.a) // undefined
		console.log(this) // Window
	}

	var obj2 = {
		a: 10,
		b: {
			bar2: foo2
		}
	}
	
	var j = obj2.b.bar2()
	
	j()
	
  </script>
</body>
</html>

```

在这里我们发现 `this.a`是undefined，而`this`指向的是 `window`。这是为什么呢？我们需要知道， `this` 永远指向的是最后调用它的对象，虽然 `bar2` 是 `b`对象的方法，但是 `bar2`赋值给 `j`的时候没有执行，而是 `j()`单独调用的，所以这里我们的 `this`其实是默认绑定指向的就是 `window`

### new 绑定

通过 `new` 关键字调用构造函数时，`this` 指向 **新创建的实例对象**

**new 调用时的底层执行步骤：**

1. **创建新对象**：生成一个空的新对象（如 `{}`）
2. **绑定原型**：将新对象的 `__proto__` 指向构造函数的 `prototype`（建立原型链关联）
3. **绑定** this：将构造函数中的 `this` 指向这个新对象
4. **执行构造函数**：运行构造函数内部代码，为新对象添加属性 / 方法（通过 `this.xxx`）
5. **返回新对象**：若构造函数没有显示返回对象类型的值，则默认返回这个新对象


通过构建函数 `new` 关键字生成一个实例对象，此时 `this` 指向这个实例对象

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  
  <script>

    function foo() {
      console.log(this); 
      this.name = "why"
    }

    new foo() //this绑定foo

  </script>

</body>
</html>
```


---

```javascript
function test() {
	console.log(this) // this 绑定 obj
	this.x = 1
}

var obj = new test()
console.log(obj.x) // 1
```


上述情况 this 指向实例对象，是建立在函数没有返回对象上
如果函数 `return` 一个 **对象**，那么现在的 `this` 就指向返回的对象


```js
function fn() {
	this.user = 'xxx'
	return {}
}

var a = new fn()
console.log(a.user) // undefined
```
  

如果函数只是返回一个 **简单类型和 null** 的时候，`this` 还是指向实例对象

```js
function fn() {
	this.user = "xxx"
	return 1
}

var a = new fn
console.log(a.user) // xxx

function fn2() {
	this.user2 = "xxx"
	return null
}

var b = new fn2
console.log(b.user2) // xxx
```


### 显示绑定


`apply()、call()、bind()` 是函数的一个方法，作用是改变函数的调用对象，它的第一个参数就表示改变后的调用这个函数的对象。因此，此时 `this` 指的就是这第一个参数

#### call

- **语法**：`function.call(thisArg,arg1,arg2,...)`
- **作用**：立即调用函数，将函数内的 `this` 绑定到 `thisArg`，并传入后续的参数（逗号分隔）

```js
function sayHi() {
	console.log(`Hi, I'm ${this.name}`)
}

const person = { name: 'Cookie' }

// 通过 call 显示绑定 this 为 person
sayHi.call(person) // Hi, I'm Cookie
```

#### apply 

- **语法**：`function.apply(thisArg,[arg1,arg2,...])`
- **作用**：与 `call` 功能一致（立即调用函数，绑定 `this`），但参数需以 **数组或类数组对象** 的形式传递

```js
function sum(a,b) {
	return a + b + this.base // this 指向手动指定对象
}

const context = { base: 10 }

// 通过 apply 绑定 this 为 context, 参数以数组传递
const result = sum.apply(context,[2,3])
console.log(result) // 15 (2 + 3 + 10, this 指向 context)
```

#### bind

- **语法**：`var/let/const newFunction = function.bind(thisArg, arg1, arg2, ...)`
- **作用**：不立即调用函数，而是返回一个 **新函数**，新函数的 `this` 被永久绑定到 `thisArg`，且可以预先传入部分参数

```js
function greet(message) {
	console.log(`${message}, ${this.name}`)
} 

const user = { name: "Cookie" }

// 通过 bind 绑定 this 为 user, 返回新函数
const greetUser = greet.bind(uesr) 
greetUser("Hello") // 输出 "Hello, Cookie" (this 固定为 user)

// 预先传入部分参数(柯里化)
const greetUserHi = greet.bind(user, "Hi")
greetUserHi() // 输出 "Hi Cookie"
```


#### 显示绑定特点

- **强制指定 this**：**无论函数在何处调用，显示绑定的 `this` 都不会改变（`bind` 甚至是 "硬绑定"，无法被其他方式覆盖）**

- **参数传递灵活**：**`call` 和 `apply` 适合立即执行并传递参数，`bind` 适合延迟执行或预先固定部分参数（如事件回调、定时器）**


### 绑定优先级


- **显示绑定优先级高于隐式绑定和默认绑定**
- **new 绑定优先级高于显示绑定（`call` / `apply`），但低于 `bind` 硬绑定**



### 箭头函数的 this


上述几种 this 绑定都是在 **函数调用** 时才进行绑定，而箭头函数没有自己的 `this`，它的 `this` 是在定义时继承自外层（父级）作用域的 `this`，且终身不变

```JavaScript
const obj1 = { name: 'obj1' };
const obj2 = { name: 'obj2' };

// 普通函数：this 可被显式绑定改变
function normalFn() {
  console.log(this.name);
}
normalFn.call(obj1); // "obj1"（this 指向 obj1）
normalFn.call(obj2); // "obj2"（this 指向 obj2）

// 箭头函数：this 继承自定义时的外层（这里是全局作用域，非严格模式下为 window）
const arrowFn = () => {
  console.log(this.name);
};
arrowFn.call(obj1); // undefined（this 仍为 window，不受 call 影响）
arrowFn.call(obj2); // undefined（同上）
```

#### 常见场景

1. 解决回到函数 `this` 丢失问题：

```js
const timerObj = {
	count: 0,
	start: function() {
		// 普通函数作为回调：this 默认绑定 指向 window (非严格模式)
		setInterval(function () {
			// this.count 会报错 (window 没有 count 属性)
		})
		
		// 箭头函数作为回调: this 继承自 start 的 this (即 timerObj)
		setInterval(() => {
			this.count ++
			console.log(this.count) // 正确累加 (this 指向 timerObj)
		},1000)
	}
}

timerObj.start()
```


2. **简化对象方法中的嵌套函数**：在对象方法中，若需要嵌套函数访问对象自身的属性，箭头函数可直接继承外层方法的 `this`，无需用 `that = this` 或 `bind` 手动保存

```js
const user = {
	name: "Cookie",
	friends: ["Bob", "Alice"],
	greetFriends: function() {
		const that = this
		this.friends.forEach(function(friend) {
			console.log(`${that.name} greets ${friend}`)
		})
		
		// 箭头函数：直接继承外层 this (即 user)
		this.friends.forEach(friend => {
			console.log(`${this.name} greets ${friend}`) // 更简洁
		})
	}
}

user.greetFriends()
```


#### 注意事项

1. **不能作为构造函数**：箭头函数没有自己的 `this`，因此不能用 `new` 调用（会报错）
2. **不适合作为对象的方法**：若对象方法用箭头函数定义，其 `this` 会继承外层的作用域（通常是全局），而非调用该方法的对象。

```js
const obj = {
	name: 'obj',
	// 错误：箭头函数的 this 指向的是全局, 而非 obj
	getName: () => {
		return this.name
	}
}

console.log(obj.getName()) // undefined (this 是 window, 无 name 属性)
```

3. **没有 `argument` 对象**：需通过剩余参数 `...arg` 获取传入的参数

```js
// 普通函数有 argument
function normalFn() {
	console.log(arguments) // [1,2,3]
}

normalFn(1,2,3)

// 箭头函数无 arguments, 需用 ...args
const arrowFn = (...args) => {
	console.log(args) // [1,2,3] (通过剩余参数获取)
}

arrowFn(1,2,3)
```



## 事件循环


#### **核心背景：JS 是单线程语言**

JavaScript 是单线程的（同一时间只能执行一段代码），这是为了避免多线程操作 DOM 导致的冲突，但单线程会遇到问题：如果执行耗时操作（如网络操作、定时器），会阻塞后续代码，造成页面卡顿

在 `JavaScript` 中，所有任务都可以分为

- 同步任务：立即执行的任务，同步任务一般会直接进入到主线程中执行
- 异步任务：异步执行任务，比如 `ajax` 网络请求，`setTimeout` 定时函数等

#### **关键概念：**

1. **执行栈（Execution Stack）用于执行同步代码的栈结构，代码按顺顺序进入栈中执行，执行完后出栈**
2. **任务队列（Task Queue）异步任务的回调函数会被放入任务队列等待执行，分为两类**
	- **宏任务（Mascro Task）：包括 `setTimeout`、`setInterval`、DOM 事件回调、I/O 操作、页面加载（`script` 标签代码）等**
	- **微任务（Micro Task）：包括 `Promise.then/catch.finally`、`async/await`（本质是 Promise）、`queueMicrotask` 等**


#### 执行流程

事件循环遵循严格的执行规则，可概括为：

先 **执行同步代码** -> **清空微任务队列** -> **执行一个宏任务** -> **重复**

具体步骤：

1. 先执行 **同步代码** ，遇到 **异步任务** 时，将其回调按类型放入 **宏任务队列** 或 **微任务队列**
2. 同步代码执行完毕（执行栈为空）后， **清空微任务队列**：按顺序执行所有微任务（包括执行过程中新增的微任务）。
3. 微任务队列清空后，执行 **一个宏任务**（从宏任务队列取第一个）
4. 重复步骤 2 和 3，形成循环

```js
console.log('1')

setTimeout(() => {
	console.log('2')
},0)

new Promise((resolve) => {
	console.log('3')
	resolve()
}).then(() => {
	console.log('4')
})

console.log('5')
``` 

_执行顺序解析_：

1. 执行同步代码：打印 `1` → 遇到 `setTimeout` 回调（放入宏任务队列）→ 执行 `Promise` 构造函数（打印 `3`）→ `then` 回调（放入微任务队列）→ 打印 `5`（同步代码结束）。
    
2. 清空微任务队列：执行 `then` 回调，打印 `4`。
    
3. 执行一个宏任务：执行 `setTimeout` 回调，打印 `2`。
    

_最终输出_：`1 → 3 → 5 → 4 → 2`


#### 核心结论

- 事件循环是 JS 单线程下实现异步的核心机制，通过 "同步 -> 微任务 -> 宏任务" 的循环执行代码
- 微任务优先级高于宏任务，同一轮循环中，微任务会全部执行完毕后再处理宏任务


## 垃圾回收 GC 

JavaScript 具有自动回收垃圾机制（GC：Garbge Collections），也就是说，执行环境会负责管理代码执行过程中使用的内存

原理：垃圾收集器会定期（周期性）找到那些不在继续使用的变量，然后释放其内存

实现方式：引用计数、标记清除

**引用计数**：语言引擎有一张 "引用表"，保存了内存里面所有的资源（通常是各种值）的引用次数。如果一个值的引用次数是 `0`，就表示这个值不再用到了，因此可以将这块内存释放

如果一个值不再需要了，引用数却不为 `0`，垃圾回收机制无法释放这块内存，就会导致内存泄漏

```js
const arr = [1,2,3,4]
console.log("Hello World")
```

上面代码中，数组 `[1,2,3,4]` 是一个值，会占用内存，变量 `arr` 是仅有的对这个值的引用，因此引用次数为 `1`，尽管后面的代码没有用到 `arr`，它还是会持续占用内存

如果需要这块内存被垃圾回收机制释放，只需要设置如下：

```js
arr = null
```

通过设置 `arr` 为 `null` ，就解除了对数组 `arr` 的引用，引用次数变为 0 ，就被垃圾回收了


#### 标记清除：`JavaScript` 最常用的垃圾回收机制
 
- **标记阶段：**从跟对象出发，遍历所有对象，为其打上 "使用中" 的标记
- **清除标记：**遍历堆内存中所有对象，清除未被标记的对象（不可达），并释放其占用的内存

弊端：标记和清除过程会暂停 JS 执行（"全停顿"），若堆内存过大，可能导致页面卡顿


#### 常规内存泄露

- 意外的全局变量：为声明的变量会被挂载到全局对象 `window` 上，成为根对象的属性，永远不会被回收

```js
function fn() {
	// 未声明的变量, 自动成为 window 的属性
	unusedVar = "一直存在"
}
```


- 被遗忘的定时器 / 事件监听：定时器或事件监听未被清除，其回调函数及内部的对象会一直保持可达状态

```js
var someResource = getData()
setInterval(function () {
	var = node = document.getElementById("Node")
	if(node) {
		// 处理 node 和 someSource
		node.innerHTML = JSON.stringify(someResource)
	}
}, 1000)
// 如果 id 为 Node 的元素从 DOM 中移除，该定时器仍会存在. 同时, 因为回调函数中包含对 'someResource' 的引用 
```

- 闭包引用：闭包若长期持有外部变量的引用（尤其是大对象），会导致这些变量无法被回收

```js
function bindEvent() {
	var obj = document.createElement('xxx')
	var unused = function() { 
		console.log(obj,"闭包内部引用obj, obj不会被释放")
	}
	obj = null // 解决方法
}
```


- *DOM元素引用未清除*：移除 DOM 元素后，若 JS 中仍保留对该元素的引用，元素无法被回收。

```js
const div = document.getElementById('myDiv');
const list = [div]; // 数组持有 div 引用

// 移除 DOM 元素，但 JS 引用仍存在
div.remove(); 
// 需手动清除引用：list = null 或 list.splice(0, 1)
```


## 函数柯里化


函数柯里化（Currying）是一种将 "接受多个参数的函数" 转换为 "一系列接受单个参数的函数" 的技术。其核心思想是 "分步接收参数"，每次调用函数只传入部分参数，并返回一个新函数等待处理剩余参数，直到所有参数收集完毕后执行最总逻辑。

- **普通函数**：一次性接收所有参数并执行

```js
function sum(a,b,c) { 
	return a + b + c
}
console.log(sum(1,2,3))
```

- 柯里化函数：分步骤接收参数，每次接收部分参数后返回新函数，直到参数齐全

```js
function currySum(a) { 
	return function(b) {
		return function(c) {
			return a + b + c
		}
	}
}

currySum(1)(2)(3)

const sum1 = currySum(1)
const sum2 = sum1(2)

console.log(sum2(3))
```


#### 自动柯里化

手动编写柯里化函数不够灵活，实际开发中常用 **通用柯里化工具** 将任意多参数函数转换为柯里化形式

```js
function curry(fn) {
	const argLength = fn.length
	
	return function curried(...args) {
		// 若收集的参数数量 >= 原函数所需参数，执行原函数
		if(args.length >= argLength) {
			return fn.apply(this.args)
		} else {
			// 否则返回新函数，继续收集参数（合并已有参数和新参数）
			return function(...nextArgs) {
				return curried.apply(this,[...args, ...nextArgs])
			}
		}
	}	
}

const curriedSum = curry(sum)
curriedSum(1)(2)(3)
curriedSum(1,2)(3)
curriedSum(1)(2,3)
```



#### 优点

1. 参数复用：将多次调用中重复的参数 "固定"，减少冗余代码

```js
// 格式化日期 (固定格式, 复用参数)
function formatData(format, date) {
	// 简化版日期格式化逻辑
	return `${format} ${date.getFullYear()}-${date.getMonth() + 1}`
}

const formatWithYearMonth = curry(fomartDate)('YYYY-MM')

// 复用固定格式, 只需传入不同日期
formatWithYearMonth(new Date(2025,0))
formatWithYearMonth(new Date(2025,11)) 
```


2. 延迟执行：将函数的执行推迟到所有参数收集完毕后，适合需要分步处理参数的场景（如：表单验证、事件监听）。

```js
function validateUser(username, password) {
	return username === 'admin' && password === '123456'
}

const curriedValidate = curry(validateUser)
const validateAdmin = curriedValidate('admin')

validateAdmin('123456') // true
validateAdmin('wrong') // false
```

3. 函数组合：柯里化后函数更容易与其他函数组合，构建复杂逻辑（**函数式编程中的常见技巧**）


## typeof 、instanceof 、toString

在 JavaScript 中 ，`typeof`、`instanceof` 和 `Object.prototype.toString` 都是用于判断数据类型的工具，但是他们的原理、适用场景各不相同。


### typeof 

`typeof` 是一元运算符，用于返回一个表示值的数据类型的字符串

核心特点：

- 语法：`typeof 变量 / 值`
- 返回值：`undefined` `boolean` `number` `string` `symbol` `bigint` `function` `object`

适用场景：快速判断 **基本数据类型**（除 null 外）和函数

```JavaScript
typeof undefined; // 'undefined'
typeof true;      // 'boolean'
typeof 123;       // 'number'
typeof 'abc';     // 'string'
typeof Symbol();  // 'symbol'
typeof 123n;      // 'bigint'
typeof function() {}; // 'function'

// 局限性：对对象（除函数外）和 null 均返回 'object'
typeof {};        // 'object'
typeof [];        // 'object'（数组本质是对象）
typeof null;      // 'object'（历史遗留 bug）
typeof new Date();// 'object'
``````


**局限性：**

- 无法区分对象的具体类型（如数组、日期、正则等，均返回 `object`）。
- 对 `null` 的判断错误（返回 `object`）

### instanceof

`instanceof` 是二元运算符，用于判断 **对象** 是否为某个构造函数的实例（基于原型链查找）

**核心原理**：检查构造函数的 `protoytype` 是否在对象的原型链上。

- 语法：`对象 instanceof 构造函数`

适用场景：判断 **引用类型**（对象、数组、函数等）的具体类型

```JavaScript
// 数组：是 Array 的实例
[] instanceof Array;      // true
[] instanceof Object;     // true（Array.prototype 继承自 Object.prototype）

// 日期对象：是 Date 的实例
new Date() instanceof Date; // true

// 自定义对象：是其构造函数的实例
function Person() {}
const p = new Person();
p instanceof Person; // true

// 局限性：基本数据类型（非对象）返回 false
'abc' instanceof String; // false（字符串字面量不是 String 实例）
new String('abc') instanceof String; // true（包装对象是实例）
```


**局限性**：

- 不能判断基本数据类型（仅适用于对象）。
- 存在原型链污染风险（若修改构造函数的 `prototype`，可能导致判断错误）。
- 跨 iframe 场景下可能失效（不同 iframe 有独立的原型链）。


### Object.prototype.toString.call()

`Object.prototype.toString.call()` 是对象的原型方法，调用后返回一个表示对象类型的字符串（格式：`"[object 类型名]"`）

**核心原理**：通过对象内部的 `[[Class]]` 属性（引擎内部标识）判断类型，不受原型链影响

使用方法：`Object.prototype.toString.call(值)`

适用场景：几乎所有类型的精准判断（包括基本类型、引用类型、`null`、`undefined` 等）。


```JavaScript
// 基本类型
Object.prototype.toString.call(undefined); // '[object Undefined]'
Object.prototype.toString.call(null);      // '[object Null]'
Object.prototype.toString.call(123);       // '[object Number]'
Object.prototype.toString.call('abc');     // '[object String]'
Object.prototype.toString.call(true);      // '[object Boolean]'

// 引用类型
Object.prototype.toString.call([]);        // '[object Array]'
Object.prototype.toString.call({});        // '[object Object]'
Object.prototype.toString.call(function() {}); // '[object Function]'
Object.prototype.toString.call(new Date());// '[object Date]'
Object.prototype.toString.call(/reg/);     // '[object RegExp]'
```

_优势：_

- 覆盖所有数据类型，包括 `null`、`undefined` 和各种对象子类型。
    
- 判断结果精准，不受原型链修改或包装对象的影响。



### 总结

- 简单判断基本类型用 `typeof`（注意避开 `null` 的坑）。
    
- 判断对象的具体类型或继承关系用 `instanceof`。
    
- 任何场景下的精准类型判断，优先用 `Object.prototype.toString.call(值)`（几乎无死角）。

实际开发中，可以封装 `toString()` 方法作为通用类型判断工具

```js
function getType(value) {
	return Object.prototype.toString.call(value).slice(8,-1)
} 

getType([]); // 'Array' 
getType(null); // 'Null' 
getType(new Date()); // 'Date'
```


## null 、undefined 、NaN


### undefined

`undefined` 表示 "变量已声明但未赋值" 或 "访问不存在的属性 / 元素" 时的默认值，是 JS 原始数据类型之一。


1. 自动赋值：变量声明但未被赋值时，变量默认值为 `undefined`

```js
let a
console.log(a) // undefined
```


2. 缺失的属性 / 参数：
	- 访问对象不存在的属时返回 `undefined`
	- 函数参数未传递时，默认值为 `undefined`

```js
const obj = {}

console.log(obj.xxx) // undefined

function fn(params) {
	console.log(params)
}

fn() // undefined
 ```

3. 与 `null` 的关系：`undefined == null` 为 `true`（抽象相等的时认为两者 "空值" 等价）

但 `undefined === null` 为 `false`（类型不同）

```js
console.log(undefined == null) // true
console.log(undefined === null) // false
```

4. 类型判断：`typeof undefined` 返回 `'undefined'`（唯一能被 `typeof` 精确判断的特殊值)

```js
console.log(typeof undefined) // undefined
```

### null

`null` 表示 “刻意赋值为空”，即变量原本应该指向一个对象，但目前没有指向任何有效对象，是 JS 原始数据类型之一。


1. _主动赋值_：`null` 必须手动赋值，不会被 JS 自动分配。

```JavaScript
let a = null; // 主动表示“空值”
console.log(a); // null
```


2. _语义含义_：通常用于表示 “空对象引用”，例如：
    - 初始化对象变量（后续会赋值为对象）；
    - 手动释放对象引用（让垃圾回收器回收内存）。

```JavaScript
let user = null; // 初始化：后续可能赋值为用户对象
// ... 使用后释放
user = null; // 告诉引擎：该对象可回收
```

3. _类型判断的坑_：`typeof null` 会返回 `'object'`（历史遗留 bug），需用 `===` 精准判断。

```JavaScript
console.log(typeof null); // 'object'（错误结果）
console.log(null === null); // true（正确判断方式）
```

4. _与_ `undefined` 的区别*：`null` 是 “主动清空”，`undefined` 是 “被动未定义”。

### NaN

`NaN` 表示 “数值运算失败的结果”，即本应返回数字的操作无法得到有效数字，是 JS 中唯一不等于自身的值 `NaN === NaN (false)`。

1. **产生场景**：主要出现在无效的数值运算中。

```JavaScript
console.log(10 / 'abc'); // NaN（字符串无法转为数字）
console.log(Number('非数字')); // NaN（类型转换失败）
console.log(Math.sqrt(-1)); // NaN（无效的数学运算）
```

2. **不等于自身**：`NaN` 是 JS 中唯一 `x !== x` 成立的值，这是判断 `NaN` 的关键。

```JavaScript
console.log(NaN === NaN); // false（特殊特性）
```

3. **正确判断方式**：需使用全局函数 `isNaN()` 或 `Number.isNaN()`（更精准）。
    - `isNaN(x)`：先将 `x` 转为数字，再判断是否为 `NaN`（可能误判非数字类型）；
    - `Number.isNaN(x)`：仅当 `x` 类型为 `number` 且值为 `NaN` 时返回 `true`（推荐）。

```JavaScript
isNaN('abc'); // true（'abc' 转数字为 NaN，误判）
Number.isNaN('abc'); // false（'abc' 类型不是 number）
Number.isNaN(10 / 'abc'); // true（正确判断）
```


4. **类型判断**：`typeof NaN` 返回 `'number'`（因为 `NaN` 属于数字类型的特殊值）。

```JavaScript
console.log(typeof NaN); // 'number'
```



## ES 6 新增

ES6 新增了**块级作用域（let/const）**、**箭头函数**、**class 类与继承**、**模块化（import/export）**、**解构赋值**、**模板字符串**、**Promise**、**Set/Map** 等特性，大幅提升了 JavaScript 的开发效率和代码可读性。


## CommonJS 与 ES Module


### CommonJS

CommonJS 采用 **运行时同步加载** 的方法，模块在加载时会阻塞程序的执行，直到加载完成，最初作为 **Node.js** 服务端设计

使用 `require()` 函数来导入模块，使用 `module.exports` 或 `exports` 对象来导出对象

**特点**：

- **同步加载**：适合服务端环境，因为模块通常都在本地文件系统中，加载速度快
- **运行时加载**：`require()` 可以在代码的任何位置调用，可以根据条件动态加载模块
- **缓存机制**：`require()` 加载的模块会被缓存，第二次导入时会直接从缓存中读取，避免重复加载



### ES Module

ESM 是 JavaScript 官方的模块化标准，采用 **编译时异步加载**，是浏览器 和 Node.js 应用的首选
使用 `import` 语句导入模块，使用 `export` 语句导出模块

在 Node.js 中使用 ESM ，需要在 **package.json** 文件中添加 `"type": "module"` ，或者将文件扩展名改为 `.mjs`

ESM 支持两种导出方式：**具名导出** 和 **默认导出**

**特点**：

- **异步加载**：默认是异步加载，不会阻塞主线程，更适合浏览器环境，但在 Node.js 中通常表现为同步加载
- **静态分析**：`import` 和 `export` 语句代码执行之前就可以确定模块的依赖关系
- **严格模式**：ESM 模块默认在严格模式下运行


| 特性       | CommonJS                     | ES Module             |
| -------- | ---------------------------- | --------------------- |
| **标准来源** | Node.js 自定义                  | ES6 官方标准              |
| **加载时机** | 运行时同步加载                      | 编译时静态加载               |
| **导出方式** | `module.exports` / `exports` | `export`（命名 / 默认）     |
| **导入方式** | `require()`                  | `import`              |
| **路径要求** | 可省略后缀，支持模块名                  | 需完整路径，推荐带后缀           |
| **循环依赖** | 返回缓存的未完成结果                   | 基于绑定引用，自动同步           |
| **适用场景** | Node.js 传统项目                 | 前端项目、Node.js 新项目      |
| **优势**   | 动态加载、Node 生态成熟               | 静态分析、tree-shaking、标准化 |

## 深拷贝、浅拷贝

**浅拷贝**：仅复制对象 / 数组的 **表层结构**（第一层属性）
- 对于基于类型属性，会复制其值
- 对于引用类型属性，只复制其内存地址，新对象和原对象共享嵌套引用类型数据

**深拷贝**：递归复制对象 / 数组的 **所有层级结构**

- 无论嵌套多深，所有基本类型和引用类型都会被完整复制
- 新对象和原对象完全独立，修改任何层级的数据都不会相互影响

```JavaScript
// 原对象（包含嵌套引用类型）
const obj = {
  name: "原对象",
  info: { age: 18 }, // 嵌套对象（引用类型）
  hobbies: ["阅读"]  // 嵌套数组（引用类型）
};


// 浅拷贝（如 Object.assign 或扩展运算符）
const shallowCopy = { ...obj };

// 基本类型：修改浅拷贝对象的表层基本类型属性，不影响原对象
shallowCopy.name = "浅拷贝对象";
console.log(obj.name); // 仍为 "原对象"（不受影响）

// 引用类型：修改浅拷贝对象的嵌套引用类型，会影响原对象
shallowCopy.info.age = 20;
shallowCopy.hobbies.push("运动");
console.log(obj.info.age); // 20（被修改）
console.log(obj.hobbies); // ["阅读", "运动"]（被修改）


// 深拷贝（如 JSON.parse(JSON.stringify())）
const deepCopy = JSON.parse(JSON.stringify(obj));

// 无论层级，修改深拷贝对象都不影响原对象
deepCopy.name = "深拷贝对象";
deepCopy.info.age = 25;
deepCopy.hobbies.push("游戏");

console.log(obj.name); // "原对象"（不受影响）
console.log(obj.info.age); // 18（不受影响）
console.log(obj.hobbies); // ["阅读"]（不受影响）
```

**实现方式**

**浅拷贝**：

- 对象： `Object.assign({}, 原对象)` 或扩展运算符 `{...原对象}`

- 数组： `[...原数组]` 或 `原数组.slice()` 或 `Array.from(原数组)`

**深拷贝**：

- 简单场景： `JSON.parse(JSON.stringify(原对象))`。这种方法无法复制函数、正则、Symbol、循环引用等

- 复杂场景：自定义递归函数

- 第三方库：lodash.cloneDeep()


## 本地存储

本地存储有 **cookie** 、**localStorage** 、**localforage** 、**sessionStorage** 、**indexedDB**

### cookie

- 容量小，4KB
- 可手动设置过期时间，不设置则为 **会话级**（即关闭页面就清除）
- 仅支持字符串

>注意：现代前端开发中，越来越多使用 `Authorization: Bearer <token>` 替代 cookie 做身份验证（尤其 SPA + JWT），以避免 CSRF 风险。

### localStorage

- 容量较大，5MB
- 永久存储，除非手动删除
- 近支持字符串

>使用技巧：存储对象需 `JSON.stringify()`，读取需 `JSON.parse()`，注意异常处理（如解析失败）。
### sessionStorage

- 容量较大，5MB
- 会话级存储
- 仅支持字符串

>`localStorage` 的关键区别：**生命周期不同** —— `sessionStorage` 是“一次打开浏览器窗口”的范围。
### indexedDB

- 封装了 `indexedDB`、`localStorage`、`WebSQL`（降级）
- 容量很大，几十MB 到 GB 级别
- 永久存储，需要手动删除
- 支持复杂数据类型（对象、二进制等）

>**优势**：功能强大，接近关系型数据库；劣势：API 复杂，学习成本高。
### localforage

- 容量很大，几十MB 到 GB 级别
- 取决于底层存储（默认跟 localStorage）
- 支持数组对象、数组等


| 存储方式             | 适用场景                         | 数据类型        | 容量  | 生命周期  | 异步  |
| ---------------- | ---------------------------- | ----------- | --- | ----- | --- |
| `cookie`         | 身份验证、跨域共享、简单标记               | 字符串         | 4KB | 手动/会话 | 否   |
| `localStorage`   | 用户偏好、缓存静态数据、离线暂存             | 字符串         | 5MB | 永久    | 否   |
| `sessionStorage` | 表单暂存、会话内状态、临时过滤              | 字符串         | 5MB | 会话    | 否   |
| `indexedDB`      | 离线应用、大数据缓存、多媒体、游戏存档          | 对象 / Blob 等 | GB级 | 永久    | 是   |
| `localforage`    | 通过复杂数据存储、兼容性要求高、简化 indexedDB | 对象 / Blob 等 | GB级 | 永久    | 是   |



## 防抖 节流

防抖和节流是两个常用的优化技术，用于处理频繁触发的事件（滚动、输入框输入等）

### 节流

无论事件触发的频率有多高，节流会规定一个固定的时间间隔，**确保函数在这个事件间隔内只会执行一次**，适用于 **页面滚动事件** 、**调整窗口大小** 、**按钮点击**

```js
function throttle(fn, wait) {
	let lastTime = 0
	return function() {
		const now = new Date().getTime()
		if(now - lastTime >= wait) {
			fn()
			lastTime = now
		}
	}
}
```


### 防抖 

当事件被连续触发的时候，只有**在事件触发停止后的特定延长时间才会执行目标函数**。如果事件在延迟时间内再次触发，之前的执行将被取消，重新计时。适用于**输入框的实时搜索**、**按钮的点击防抖**、**表单提交的防抖**


```js
function debounce(fn, delay) {
	let timer = null
	return function() {
		const context = this
		const args = arguments
		
		clearTimeout(timer)
		
		timer = setTimeout(function() {
			fn.apply(context,args)
		}, delay)
	}
}
```