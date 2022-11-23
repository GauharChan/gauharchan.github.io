---
title: note
date: 2019-09-03 12:02:41
categories: JavaScript
---

<Banner />

![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1567421585499&di=59341acf2f3f63305a82607355a37b68&imgtype=0&src=http%3A%2F%2Fwww.21wenju.com%2Fupfile%2Fimages%2Fbig%2F200601090000000331.jpg)

## 微擎

### 目录结构

> [详细结构](http://s.w7.cc/index.php?c=wiki&do=view&id=1&list=15)

| addons       | 微擎模块              |
| ------------ | --------------------- |
| api          | 对接外部系统接口      |
| app          | 微站 （Mobile / App） |
| attachment   | 附件目录              |
| framework    | 微擎框架              |
| payment      | 支付调用目录          |
| tester       | 测试用例              |
| upgrade      | 升级脚本              |
| web          | 后台管理              |
| api.php      | 微信 api 接口         |
| index.php    | 系统入口              |
| install.php  | 安装文件              |
| password.php | 密码重置              |

### 创建一个新的文件

首先找到可以运行的文件的目录下，新建一个 html 文件。比如路径：`/public_html/addons/sz_yi/template/mobile/style1/fuli`，新建 center.html 文件

然后在`/public_html/addons/sz_yi/core/mobile`下对应的目录下，比如：`fuli`。创建一个与 html 文件同名的 php 文件。然后 php 文件中输入一下代码：

```php
<?php

if (!defined('IN_IA')) {
	exit('Access Denied');
}
global $_W, $_GPC;


include $this->template('fuli/center'); // html文件模板的路径，格式：文件夹/html文件名
?>

```

### 创建一个新的目录

需要在`/public_html/addons/sz_yi/site.php`文件下新增代码

`doMobile`+ 目录名，**首字母大写**作为方法名

`'center'`是 html 文件名

```php
public function doMobileFuli(){ $this->_exec(__FUNCTION__,'center',false); }
```

## jQuery

`$(window).height()`：屏幕的高度(可视化区域的高度)

`$(window).scrollTop()`：滚动的距离

`$(document).height()`：页面的实际高度

可用于判断滚动触底

```js
$(document).height() <= $(window).height() + $(window).scollTop();
```

## JavaScript

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
> 可以用 `arguments.length` 检测函数的参数个数

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

## 谷歌浏览器快捷键 🔑

[官网链接](https://support.google.com/chrome/answer/157179?hl=zh-Hans)

常用按键

| 功能                   | 按键        |     |
| ---------------------- | ----------- | --- |
| 打开新的标签页         | ctrl + t    |     |
| 关闭当前标签页         | ctrl + w    |     |
| 搜索栏获取焦点         | ctrl + l    |     |
| 当前页面的历史浏览记录 | alt + (+ -) |     |

## ES6

### Generator 函数

> `yield`读音：u 的

执行 Generator 函数返回的是一个遍历器对象，必须调用遍历器对象的`next`方法，使得指针移向下一个状态。

Generator 函数是分段执行的，`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行。

```js
function* helloworld() {
  yield "hello";
  yield "world";
  return "end";
}
let h = helloworld();
console.log(h.next()); // {value: "hello", done: false}
console.log(h.next()); // {value: "world", done: false}
console.log(h.next()); // {value: "end", done: true}
```

总结一下，调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。以后，每次调用遍历器对象的`next`方法，就会返回一个有着`value`和`done`两个属性的对象。`value`属性表示当前的内部状态的值，是`yield`表达式后面那个表达式的值；`done`属性是一个布尔值，表示是否遍历结束。

在普通函数中不可以使用`yield`关键字，`yield`表达式如果用在另一个表达式之中，**必须放在圆括号里面**。

`yield`表达式用作函数参数或放在赋值表达式的右边，可以不加括号

```js
function* demo() {
  foo(yield "a", yield "b"); // OK
  let input = yield; // OK
}
```
