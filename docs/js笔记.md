---
title: js笔记
date: 2019-09-03 12:02:41
categories: JavaScript
---

<Banner />

![](https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=852247412,628825777&fm=26&gp=0.jpg)

## JavaScript

### 事件循环

- 调用栈，后进先出
- 队列，先进先出
- **macro-task(宏任务 Task)**
  - script(整体代码)
  - setTimeout/setinterval
  - setlmmediate
  - I/O 操作
  - UI rendering
- **micro-task (微任务 Job)**
  - process.nextTick
  - Promise.then
  - MutationObserve
  - async/await

Javscript 的执行机制是: 首先事件循环从宏任务队列开始, 这个时候宏任务队列中, 只有一个script(整体代码)任务. 每一个任务的执行顺序, 都依靠**函数调用栈**来搞定, 而当遇到任务源时, 则会先分发任务到对应的队列中去, 先执行调用栈中的函数, 当调用栈中的执行上下文全部被弹出, **只剩下全局执行上下文的时候, 就开始执行 Job 执行队列**, Job 执行队列执行完成后就开始执行 Task 执行队列, 先进入的先执行, 后进入的后执行, 无论是 Task 还是 Job 都是通过函数调用栈来执行. Task 执行完一个, JavaScript 引擎会继续检查是否有 Job 需要执行. 就形成了 Task--Job--Task--Job 的循环, 这就行形成了事件循环 ( Event Loop).

所以大概就是`调用栈`->`微任务`->`宏任务`->`微任务`->`宏任务`->`微、宏一直重复`

其中重点是在执行微任务的时候遇到**微任务，会加入到微任务队列中并执行**

### isNaN()

> `isNaN()` 函数用于检查其参数是否是非数字值。
>
> 如果参数值为 `NaN` 或字符串、对象、undefined 等非数字值则返回 true, 否则返回 false。
>
> **如果参数是字符串，且内容是数字，会进行类型转换,使用的是`Number()`方法转换，转换为数字，所以返回 false**
>
> 0 是`false`，1 是`true`

- **1、数字形式的字符串**。例如 "123"、"-3.14"，虽然是字符串型，但被 isNaN() 判为数，返回 false。（"12,345,678"，"1.2.3" 这些返回 true）
- **2、空值**。null、空字符串""、空数组[]，都可被 Number()合法的转为 0，于是被 isNaN 认为是数字，返回 false。（undefined、空对象{}、空函数等无法转数字，返回 true）
- **3、布尔值**。Number(true)=1,Number(false)=0，所以 isNaN 对布尔值也返回 false。
- **4、长度为 1 的数组**。结果取决于其中元素，即：isNaN([a])=isNaN(a)，可递归。例如 isNaN([["1.5"]])=false。
- **5、数字特殊形式**。例如"0xabc"、"2.5e+7"，这样的十六进制和科学计数法，即使是字符串也能转数字，所以也返回 false

### URLSearchParams

假设浏览器的 url 参数是` "?name=蜘蛛侠&age=16"`

```js
new URLSearchParams(location.search).get("name"); // 蜘蛛侠
```

### clientWidth 、offsetWidth、scrollWidth

> scrollWidth：对象的实际内容的宽度，不包边线宽度，会随对象中内容超过可视区后而变大。
> clientWidth：对象内容的可视区的宽度，不包滚动条等边线，会随对象显示大小的变化而改变。
> offsetWidth：对象整体的实际宽度，包滚动条等边线，会随对象显示大小的变化而改变。

1. clientWidth = content + padding
2. offsetWidth = content + border + padding+ 垂直滚动条宽度
3. scrollWidth = content + padding

```js
function getViewport() {
  if (document.compatMode == "backCompat") {
    return {
      width: document.body.clientWidth,
      height: document.body.clientHeight,
    };
  } else {
    return {
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight,
    };
  }
}

/*document.compatMode:判断当前浏览器采用的渲染方式
 * BackCompat：标准兼容模式关闭。
 *CSS1Compat：标准兼容模式开启。
 * 当document.compatMode等于BackCompat时，浏览器客户区宽度是document.body.clientWidth；
 * 当document.compatMode等于CSS1Compat时，浏览器客户区宽度是document.documentElement.clientWidth。
 */
```

### js 窗口属性

网页可见区域宽：document.body.clientWidth
网页可见区域高：document.body.clientHeight
网页可见区域宽：document.body.offsetWidth (包括边线的宽)
网页可见区域高：document.body.offsetHeight (包括边线的宽)
网页正文全文宽：document.body.scrollWidth
网页正文全文高：document.body.scrollHeight
网页被卷去的高：document.body.scrollTop
网页被卷去的左：document.body.scrollLeft
网页正文部分上：window.screenTop
网页正文部分左：window.screenLeft
屏幕分辨率的高：window.screen.height
屏幕分辨率的宽：window.screen.width
屏幕可用工作区高度：window.screen.availHeight
屏幕可用工作区宽度：window.screen.availWidth

### offsetLeft、offsetParent、offsetTop

offsetLeft 是一个只读属性，返回当前元素*左上角*相对于 [`HTMLElement.offsetParent`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/offsetParent) 节点的左边界偏移的像素值。

**HTMLElement.offsetParent** 是一个只读属性，返回一个指向最近的（closest，指包含层级上的最近）包含该元素的**定位**元素。**如果没有定位的元素**，则 `offsetParent` 为**最近的 `table`, `table cell` 或`body`元素**。当元素的 `style.display` 设置为 "none" 时，`offsetParent` 返回 `null`。`offsetParent` 很有用，因为 [`offsetTop`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/offsetTop) 和 [`offsetLeft`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/offsetLeft) 都是相对于其内边距边界的。

**HTMLElement.offsetTop** 为只读属性，它返回当前元素相对于其 [`offsetParent`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/offsetParent) 元素的顶部内边距的距离。

### clientX

一般用于鼠标点击时触发的鼠标相对于可视化区域的水平距离，Y 则是垂直距离

### cssText

cssText 属性设置或返回作为字符串的样式声明的内容，可批量设置行内样式

```js
Object.style.cssText = "string";
```

### arguments 对象

> 不是数组，是类数组对象，只有 length 属性和索引元素，但可以通过`Array.from()`转换为数组
>
> 函数的参数的集合
>
> 可以用 `arguments.length` 检测函数的实参的个数
>
> `函数名.length` 检测函数的形参个数

```js
function test(name, age) {
  console.log(arguments); // Arguments(2) ["1", 2, callee: ƒ, Symbol(Symbol.iterator): ƒ]
  console.log(Object.prototype.toString.call(arguments)); // [object Arguments]
  console.log(arguments[0]); // '1'
  console.log(arguments[1]); // 2
}
test("1", 2);
```

### toFixed(2)

> 保留小数点后两位

### 空字符串`''`是`false` 空对象`{}`和空数组`[]`是`true`

## filter

**filter 的 callback 函数需要返回布尔值 true 或 false. **

**如果为 true 则表示通过啦！如果为 false 则失败。**

## cookie、session、localStorage

> 区别

1. cookie 数据始终在同源的 http 请求中携带（即使不需要）,数据不能超过 4k
2. sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；
3. localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；
4. cookie 只在设置的 cookie 过期时间之前一直有效，即使窗口或浏览器关闭。
5. **作用域不同**，sessionStorage**不在**不同的**浏览器窗口**中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie 也是在所有同源窗口中都是共享的。
6. session 存储在服务端

## 数组的 every 方法

every 需要所有的循环项都为 true 时才返回 true，否则返回 false。相当于全选状态

## 数组的 some 方法

some 只要有一个为 true 那么就会返回 true。

## 数组的 sort 方法

**浏览器根据回调函数的返回值来决定元素的排序：（重要）**

- 如果返回一个大于 0 的值，则元素会交换位置
- 如果返回一个小于 0 的值，则元素位置不变
- 如果返回一个 0，则认为两个元素相等，则不交换位置

### 实现冒泡排序

```js
// 冒泡排序
let arr = [1, 5, 2, 7, 99, 6];
arr.sort(function (a, b) {
  return a - b; // 如果a > b 则交换位置
});
console.log(arr); // [1,2,5,6,7,99]
```

## 数组的 reduce 方法

> 为数组中的每一个元素，依次执行回调函数。

**语法:**

```js
arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])
```

参数解释：

- accumulator：上一次调用回调函数时的返回值，或者初始值
- currentValue：当前正在处理的数组元素
- index：当前正在处理的数组元素下标
- array：调用 reduce()方法的数组
- initialValue：可选的初始值（作为第一次调用回调函数时传给 accumulator 的值）[如果不传值的话，默认是数组第一个元素]

例子：

```js
let arr = ["m", "y", " n", "a", "m", "e", " is gauhar"];
let person = arr.reduce(function (value, item) {
  return value + item;
});
console.log(person); // my name is gauhar
```

## 对象转换为数组

```js
Object.values(对象);
```

## focus 和 focusin 的区别

> blur 和 focusout 同理

当元素即将接收 `focus` 事件时，`focusin `事件被触发。 这个事件和 [focus](https://developer.mozilla.org/en-US/docs/Web/Events/focus) 事件的主要区别在于后者不会冒泡。

## js 计算当前时间点所在的周一和周日的时间戳

> 精髓在于利用`相差的天数`\*一天的时间戳计算

```js
let date = new Date();
let today = date.getTime(); // 获取今天的时间戳
let todayWeek = date.getDay(); //获取今天是星期几
let oneDayTime = 24 * 60 * 60 * 1000; //一天的时间戳
let mon = today - (todayWeek - 1) * oneDayTime; // 今天的时间戳 - 相差天数的时间戳
let sun = today + (7 - todayWeek) * oneDayTime; // 今天的时间戳 + 相差天数的时间戳
// 获取年月日再转时间戳
let monDate = new Date(mon);
let monDateDay = monDate.getDate();
let monDateMonth = monDate.getMonth() + 1;
let monDateYear = monDate.getFullYear();
let start = monDateYear + "/" + monDateMonth + "/" + monDateDay;
console.log(Date.parse(start)); //周一的时间戳

let sunDate = new Date(sun);
let sunDateDay = sunDate.getDate();
let sunDateMonth = sunDate.getMonth() + 1;
let sunDateYear = sunDate.getFullYear();
let end = sunDateYear + "/" + sunDateMonth + "/" + sunDateDay;
console.log(Date.parse(end)); //周日的时间戳
```

## 最简单的深拷贝

> 不能拷贝函数

```js
JSON.parse(JSON.stringify(变量));
```

## Json.stringify()不能把**函数**转换 ！！

```js
let fun = function () {
  console.log(111);
};
console.log(JSON.stringify(fun)); //undefined
```

## js 执行顺序

> `macrotask`宏任务队列
>
> `microtask`微任务队列

- macrotask：主代码块、setTimeout、setInterval 等（可以看到，事件队列中的每一个事件都是一个 macrotask，现在称之为宏任务队列）
- microtask：Promise、process.nextTick 等
- 在某一个**宏任务队列执行完后**，在重新渲染与开始下一个宏任务之前，就会将在它执行期间产生的所有`微任务`都执行完毕（在渲染前）。

promise 的 resolve 和 reject 是才是异步的回调。

创建了一个 promise 实例对象的时候，钻进去回调函数中，执行输出`2`，for 循环，调用 resolve 函数(异步，微任务)，执行输出`3`，代码往下面走，执行输出`5`，在渲染宏任务之前，完成微任务输出`4`，最后输出`1`

```js
setTimeout(() => {
  console.log(1);
}, 0);
new Promise((resolve) => {
  console.log(2);
  for (let i = 0; i < 10000; i++) {
    i == 9999 && resolve();
  }
  console.log(3);
}).then(() => {
  console.log(4);
});
console.log(5);
// 2 3 5 4 1
```

## js 定义变量时使用 var 关键字与不使用的区别

在全局作用域下，两者都是定义为全局变量。for 循环的使用 var 定义的变量也是全局的，在局部作用域下(function,class...)，

如果不使用关键字定义，则变量时全局变量。使用关键字定义，就是局部变量。

> 特别注意的是：函数身上有一个 name 属性，是函数名

```js
function Foo() {
  // 由于这个函数里面没有用关键字定义变量，所以当Foo函数被调用的时候，里面的变量变成了全局变量
  // 这个函数是变量
  getName = function () {
    this.say = "hello";
    console.log(1);
  };
  name = "123";
  a = "dd";
  return this; // 谁调用Foo函数，this就指向谁
}
// 函数也是对象，这里是给Foo函数对象定义了getName属性
Foo.getName = function () {
  console.log(2);
};
function bar() {}
bar.rrr = "123"; // 函数也是对象，这里是给bar函数定义了getName属性
// 这里是给Foo函数对象的原型身上绑定了一个getName属性，它是一个函数
Foo.prototype.getName = function () {
  console.log(3);
};
/**
     下面这两个函数都是全局作用域下的，都会变量提升
     区别在于，function关键字定义的，提升的优先级较高，而var在后
     所以var定义的函数会覆盖function定义的函数
*/
var getName = function () {
  console.log(4);
};
function getName() {
  console.log(5);
}

console.dir(Foo); //可以查看Foo函数对象，特别注意的是：函数身上有一个name属性，是函数名
console.log(window.a); //undefined 因为这个时候Foo函数还没有被调用
Foo.getName();
2; // 调用了Foo函数对象的getName方法
getName();
4; // 全局作用域下的函数，var最后覆盖

Foo().getName();
1; // 首先这里调用Foo函数之前，代码已经跑完，
// 所以Foo()执行之后，里面的getName方法覆盖了全局的var定义的getName方法

getName();
1; // 上面的一句代码改变了全局作用域下的变量
new Foo.getName();
2; //调用了Foo函数对象的getName方法，执行了方法，然后实例化了对象。所以这里是Foo.getName对象
new Foo().getName();
3; // new关键字创建了一个实例对象，然后调用了原型身上的getName方法

new new Foo().getName();
3; //new关键字创建了一个实例对象，然后调用了console.log(tt.say);
// 然后在通过new关键字，实例化了Foo原型身上的getName方法
// 所以这里是Foo.getName对象

window.getName();
1; // 和上面的getName()一样; 1 // 上面的一句代码改变了全局作用域下的变量
console.log(Foo.name); // Foo 函数身上有一个name属性，是函数名
console.dir(bar); // 查看bar函数对象

console.log(window.name); // 上面的代码调用了Foo函数，里面的变量变成了全局变量
console.log(window.a);
```

## Element.attributes

attributes 返回 dom 元素设置的属性，返回的是一个``NamedNodeMap`

> NamedNodeMap，一个类数组对象

**NamedNodeMap** 接口表示属性节点 [`Attr`](https://developer.mozilla.org/zh-CN/docs/Web/API/Attr) 对象的集合。尽管在 `NamedNodeMap` 里面的对象可以像数组一样通过索引来访问，但是它和 [`NodeList`](https://developer.mozilla.org/zh-CN/docs/Web/API/NodeList) 不一样，对象的顺序没有指定。

## scroll 滚动

> behavior: smooth，一下方法都有的参数，设置平滑滑动的过渡

scrollTo()方法，设置滚动到某一位置，相对于浏览器。

scrollBy()方法，相对于自己移动滚动条，`scrollBy(0,-100)`，表示相对于自己往上滚动 100

scrollIntoView()方法，可以指定位置

```js
// start出现在视口顶部、center出现在视口中央、end出现在视口底部
document.querySelector(".box").scrollIntoView({
  block: "start" || "center" || "end",
});
```

**css 设置平滑过渡**

```css
html {
  scroll-behavior: smooth; // 全局滚动具有平滑效果
}

// 或者所有
* {
  scroll-behavior: smooth;
}
```

### scrollingElement

该对象可以非常`兼容`地获取`scrollTop`、`scrollHeight`等属性，在`移动端`跟`PC端`都屡试不爽
