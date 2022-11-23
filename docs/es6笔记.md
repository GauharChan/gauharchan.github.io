<Banner />
## 字符串扩展

### 1. Unicode 表示法

允许采用`\uxxxx`形式表示一个字符

```js
"\u0061"; // "a"
```

但是，这种表示法只限于码点在`\u0000`~`\uFFFF`之间的字符。超出这个范围的字符，必须用两个双字节的形式表示。

```js
"\uD842\uDFB7";
// "𠮷"

"\u20BB7"; // 超出范围
// " 7"
```

为解决上面的超出范围， ES6 对这一点做出了改进，只要将码点放入大括号，就能正确解读该字符。

```js
"\u{20BB7}";
// "𠮷"
```

### 2.字符串的 for...of 循环

ES6 为字符串添加了遍历器接口，所以可以使用`for of`， 这个遍历器最大的优点是可以识别大于`0xFFFF`的码点 。

### 3. 模板字符串

`${变量}`，这个语法里面可以进行运算

```js
let x = 1;
let y = 2;

`${x} + ${y} = ${x + y}`;
// "1 + 2 = 3"
```

模板字符串甚至还能嵌套。

```javascript
const tmpl = (addrs) => `
  <table>
  ${addrs
    .map(
      (addr) => `
    <tr><td>${addr.first}</td></tr>
    <tr><td>${addr.last}</td></tr>
  `
    )
    .join("")}
  </table>
`;
```

### 4. 标签模板

标签模板其实不是模板，而是函数调用的一种特殊形式。“标签”指的就是函数，紧跟在后面的模板字符串就是它的参数。

```javascript
alert`123`;
// 等同于
alert(123);
```

### 5. 方法扩展

#### 5.1 String.raw

ES6 还为原生的 String 对象，提供了一个`raw()`方法。该方法返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，往往用于模板字符串的处理方法。

```js
String.raw`Hi\n${2 + 3}!`;
// 实际返回 "Hi\\n5!"，显示的是转义后的结果 "Hi\n5!"
```

#### 5.2 实例方法：includes(), startsWith(), endsWith()

- **includes()**：返回布尔值，表示是否找到了参数字符串。
- **startsWith()**：返回布尔值，表示参数字符串是否在原字符串的头部。
- **endsWith()**：返回布尔值，表示参数字符串是否在原字符串的尾部。

```js
let str = `hello world`;
console.log(str.startsWith("h")); // true
console.log(str.includes("o")); // true
console.log(str.endsWith("m")); // false
```

这三个方法都支持第二个参数，表示开始搜索的位置。

```javascript
let s = "Hello world!";

s.startsWith("world", 6); // true
s.endsWith("Hello", 5); // true
s.endsWith("lo", 5); // true
s.includes("Hello", 6); // false
```

上面代码表示，使用第二个参数`n`时，`endsWith`的行为与其他两个方法有所不同。它针对前`n`个字符，而其他两个方法针对从第`n`个位置直到字符串结束。

#### 5.3 repeat(num)

> 返回一个新字符串，表示将原字符串重复`n`次。

```javascript
"x".repeat(3); // "xxx"
"hello".repeat(2); // "hellohello"
"na".repeat(0); // ""
```

参数如果是小数，会被取整。

```javascript
"na".repeat(2.9); // "nana"
```

如果`repeat`的参数是负数或者`Infinity`，会报错。

```javascript
"na".repeat(Infinity);
// RangeError
"na".repeat(-1);
// RangeError
```

但是，如果参数是 0 到-1 之间的小数，则等同于 0，这是因为会先进行取整运算。0 到-1 之间的小数，取整以后等于`-0`，`repeat`视同为 0。

```javascript
"na".repeat(-0.9); // ""
```

参数`NaN`等同于 0。

```javascript
"na".repeat(NaN); // ""
```

如果`repeat`的参数是字符串，则会先转换成数字。

```javascript
"na".repeat("na"); // ""
"na".repeat("3"); // "nanana"
```

#### 5.4 padStart(),padEnd()

> padStart() 头部补全
>
> padEnd() 尾部补全
>
> 两个参数： 第一个参数是字符串补全生效的最大长度，第二个参数是用来补全的字符串。

```javascript
总共长度是5，在x的前面补指定的字符串
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

如果原字符串的长度，等于或大于最大长度，则字符串补全不生效，返回原字符串。

**如果省去第二个参数，默认使用空格补全**

#### 5.5 trimStart()，trimEnd()

> 和 trim 一样，这两个方法一个是去除头部空格、一个是尾部
>
> 浏览器还部署了额外的两个方法，`trimLeft()`是`trimStart()`的别名，`trimRight()`是`trimEnd()`的别名。

## 变量的解构赋值

### 数组解构：根据**索引位置**赋值。

**解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。**

只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值，**可以解构数组的属性**

```js
// 数组解构
let [foo] = ["a", "aa"];
console.log(foo); // 输出：a

// 对象解构
let { foo } = { fo: "a", foo: "aa" };
console.log(foo); // 输出：aa
```

数组解构应该对应数组，对象同理

```js
let [foo] = { foo: "a" };
console.log(foo); // 报错

// 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。所以没有报错，而是返回undefined
let { foo } = ["foo:12", "aa"];
console.log(foo); // undefined

// 这时是根据数组的索引值解构的，foo是一个变量名
let { 0: foo } = ["12", "aa"];
console.log(foo); // 12
```

可以指定默认值,注意，ES6 内部使用严格相等运算符（`===`），判断一个位置是否有值。所以，**只有当一个数组成员严格等于**`undefined`，默认值才会生效。

```js
let [x, y = "b"] = ["a", undefined]; // x='a', y='b'
```

### 对象解构：根据**键名**赋值。

对象的解构赋值，可以很方便地将现有对象的**方法**，赋值到某个变量

```js
let { log } = console; // 解构赋值得到console.log()方法
log(123); // 调用方法，控制台输出123
```

**嵌套**

> 注意每层的结构要对应

```js
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p: [x, { y }] } = obj;   // 这个时候的p是键名，只是匹配模式
相当于[x,{ y } ] = ['Hello', { y: 'World' }]
x // "Hello"
y // "World"
----------------------------------------------------
const node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};

let { loc, loc: { start }, loc: { start: { line }} } = node;
line // 1
loc  // Object {start: Object}
start // Object {line: 1, column: 5}
```

**对象的结构赋值可以获取继承的属性**

```js
const obj1 = {};
const obj2 = { foo: "bar" };

// 通过setPrototypeOf方法将obj2设置为obj1的原型对象
Object.setPrototypeOf(obj1, obj2);
// obj1继承了 obj2的属性
const { foo } = obj1;
foo; // "bar"
```

### **字符串解构**

字符串也可以解构赋值。这时因为此时，字符串被转换成了一个类似数组的对象。

```js
const [a, b, c, d, e] = "hello";
a; // "h"
b; // "e"
c; // "l"
d; // "l"
e; // "o"

// 类似数组的对象有length属性
let { length: len } = "hello";
len; // 5
```

### 数值和布尔值的解构

> 解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。即转换为 Number 对象，和 Boolean 对象

```js
let { toString: s } = 123;
s === Number.prototype.toString; // true

let { toString: s } = true;
s === Boolean.prototype.toString; // true
```

可以解构对应的对象属性、方法

### 函数参数解构

> 在实参传递给形参的时候就会被解构

```js
function add([x, y]) {
  return x + y;
}

add([1, 2]); // 3
```

当然也可以设置默认值，**但注意是给参数的值(也就是变量 x,y)** 设置默认值

```js
function move({ x = 0, y = 0 } = {}) {
  return [x, y];
}

move({ x: 3, y: 8 }); // [3, 8]
move({ x: 3 }); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```

在声明语句中不可使用`()`括号

赋值语句的非模式部分，可以使用圆括号。

## Set

> 类似于数组，但成员都是唯一的，没有重复

**属性**：

`Set.prototype.constructor`：构造函数，默认就是`Set`函数。

`Set.prototype.size`：返回`Set`实例的成员总数。

**方法**

- `Set.prototype.add(value)`：添加某个值，返回 Set 结构本身。
- `Set.prototype.delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `Set.prototype.has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `Set.prototype.clear()`：清除所有成员，没有返回值。

`Set`函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。

```js
let set = new Set([1,2,3,2])
[...set] // [1,2,3]
```

或者使用 add()方法添加成员，通常配合循环遍历使用

```js
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach((x) => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4
```

向 Set 加入值得时候不会进行类型转换，如：5 和 '5' 是不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（`===`），主要的区别是向 Set 加入值时认为`NaN`等于自身，而精确相等运算符认为`NaN`不等于自身。

```js
let set = new Set([5, "5"]);
console.log(set); // [5,"5"]

let set = new Set([NaN, NaN]);
console.log(set); // [NaN]
```

对象和数组总是不相等的

```js
let set = new Set([{ a: 1 }, { a: 1 }, [2, 1], [2, 1]]);
console.log(set.size); // 4
```

`Array.from`方法可以将 Set 结构转为数组。

**总结几种数组去重方法**

```js
let arr = [1, 2, 2, 1];
// 1
let set = [...new Set(arr)]; // [1,2]
// -------------------
// 2
let set = new Set();
arr.forEach((e) => {
  set.add(e);
});
set = [...set]; // [1,2]
// -------------------------
// 3
let set = new Set(arr);
set = Array.from(set);
// 这个是上面的简写版
let set = Array.from(new Set(arr)); // [1,2]

//4
let set = new Set(arr);
let [a, b] = set;
let arr2 = [a, b];
console.log(arr2); // [1,2]
```

**keys()，values()，entries()**

这三个方法返回的都是遍历器对象。由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以`keys`方法和`values`方法的行为完全一致

```js
let set = new Set(["red", "green", "blue"]);
// keys() 和 values() 返回的结果是一样的
for (let item of set.values()) {
  // 默认生成的遍历器是values()，所以这里也可以省略
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```

## WeakSet

> 主要是更好的进行`js`的垃圾回收

`Set`是强类型引用，因此并不会进行垃圾回收。而`WeakSet`是弱类型引用，WeakSet 里面的引用，都不计入垃圾回收机制

```js
// weakSet
const weakSet = new WeakSet()
let b = { name: 'gauhar' }
weakSet.add(b)
b = null // b被回收了
```

与`Set`的区别

- 与`Set`相比，`WeakSet` 只能是**对象的集合**，而不能是任何类型的任意值。
- `WeakSet`持弱引用：集合中对象的引用为弱引用。 如果没有其他的对`WeakSet`中对象的引用，那么这些对象会被当成垃圾回收掉。 这也意味着`WeakSet`中没有存储当前对象的列表。 正因为这样，`WeakSet` 是不可枚举的。

## Map

JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用**字符串**当作键。这给它的使用带来了很大的限制。

Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，**各种类型的值（包括对象）都可以当作键。**

### 对象去重

> 利用对象中的唯一标识(id)作为键名。当键名一样时，后面的数据会覆盖前面的数据，达到去重的目的

```js
let designData = [
  {
    id: 1,
    name: "🍇",
  },
  {
    id: 2,
    name: "🌰",
  },
];
let temp = [
  {
    id: 1,
    name: "🐂",
  },
];
let map = new Map();
let arr = designData.concat(temp);
arr.map((item) => map.set(item.id, item)); // payment_id唯一标识作为键名，去重
designData = [...map.values()];
console.log(designData);
```

## Promise 对象

### 1. 3 个状态

1. pending：进行中
2. fulfilled：已成功
3. rejected：已失败

**实例**

```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```

`then`可以接受两个回调函数作为参数，第一个为`resolved`，第二个为`rejected`

```js
promise.then(
  function (res) {},
  function (error) {}
);
```

### 2. promise 新建之后会立即执行

> <font color="red">**无法中途取消**</font>

```js
let promise = new Promise((resolve, reject) => {
  console.log("promise");
  resolve("done");
});
promise.then((res) => {
  console.log(res); // 异步  将在当前脚本所有同步任务执行完才会执行
});
console.log("Hi"); // 主线程

// promise
// Hi
// done
```

### 3.promise.all()

`Promise.all()`方法用于将多个 Promise 实例，包装成一个新的 Promise 实例

```javascript
const p = Promise.all([p1, p2, p3]);
p.then(res => {
    ...
}).catch(err => {
    console.log(err)
})
```

只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`，`res`返回的是一个数组。若`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`，此时`err`是第一个被`reject`的实例的返回值（错误信息）

如果作为参数的 Promise 实例，自己定义了`catch`方法，那么它一旦被`rejected`，并不会触发`Promise.all()`的`catch`方法。**所以参数 Promise 实例最好不要定义`catch`**

## class 类

### es5 实现类的方法

```js
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}
Person.prototype.name = function () {
  console.log(this.firstName + " " + this.lastName);
};

new Person("gauhar", "chan").name(); // gauhar chan
```

### es6

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  name() {
    console.log(this.firstName + " " + this.lastName);
  }
}

new Person("gauhar", "chan1").name(); // gauhar chan1
```

**类的数据类型就是函数，类本身就指向构造函数。**

### 修改，增加类的属性和方法

> 由于类的方法都定义在`prototype`对象上面，所以类的新方法可以添加在`prototype`对象上面。`Object.assign`方法可以很方便地一次向类添加多个方法。

```js
Person.prototype.age = 12;
let person1 = new Person("gauhar", "chan1");
console.log(person1.age); // 12
Person.prototype.age = 1234;
console.log(person1.age); // 1234

Person.prototype.sayHi = () => {
  console.log("hello");
};
new Person("gauhar", "chan1").sayHi(); // hello

Object.assign(Point.prototype, {
  toString() {},
  toValue() {},
});
```

**类的内部所有定义的方法，都是不可枚举的**

与 ES5 一样，类的所有实例共享一个原型对象。

```javascript
var p1 = new Point(2, 3);
var p2 = new Point(3, 2);

p1.__proto__ === p2.__proto__;
//true
```

> <font color="red">`__proto__` 并不是语言本身的特性，这是各大厂商具体实现时添加的私有属性，虽然目前很多现代浏览器的 JS 引擎中都提供了这个私有属性，但依旧不建议在生产中使用该属性，避免对环境产生依赖。生产环境中，我们可以使用 **`Object.getPrototypeOf`** 方法来获取实例对象的原型，然后再来为原型添加方法/属性。</font>
>
> 注意修改原型，“类”的原始定义，影响到所有实例

### class 表达式

```js
const MyClass = class Me {
  constructor(name) {
    this.name = name;
  }
  getName() {
    return Me.name; // Me 类的名字
  }
};
let my = new MyClass();
```

**这个类的名字是`Me`，但是`Me`只在 Class 的内部可用，指代当前类。在 Class 外部，这个类只能用`MyClass`引用。**

## module

### export

> 输出多个

```js
const str = "gauhar";
const fun = function () {
  return 123;
};
export { str, fun };

// 引入
import { str, fun } from "./index.js";
```

### export default

> 输出一个

```js
const str = "gauhar";
export default str;

// 引入  不一定是str，可以是任意的名字
import str from "./index.js";
```

## 编程风格

### 对象动态属性名

```js
const obj = {
  [getKey()]: 1,
};
function getKey() {
  return "number";
}
console.log(obj);
```

### 初始化对象

> 最好把对象静态化

```js
const a = { x: null };
a.x = 3;
```
