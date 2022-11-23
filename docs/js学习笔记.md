<Banner />

## 1.变量

> 本质：变量自身不可以存储数据，真正存储数据的是**内存(堆栈空间)**，所以变量只是一个指向该数据的一个内存地址
>
> 变量名大小写敏感，不可以使用关键字和保留字

使用关键字`var`声明变量，会存在变量提升的问题，会被提升到代码的头部，因为本身他的实际赋值过程也是如此。

```js
var a = 1;
```

代码的过程是

```js
var a;
a = 1;
```

所以可以单纯定义一个变量名

```js
var a; // undefined
// 此时并没有给变量a赋值，所以他的默认值是undefined
```

如果不使用`var`关键字声明，则该变量是**全局变量**

如果变量没有定义就使用的话，js 会报错`变量 is not defined`

### 1.1 命名规则

- 第一个字符，可以是任意 Unicode 字母（包括英文字母和其他语言的字母），以及美元符号（`$`）和下划线（`_`）。
- 第二个字符及后面的字符，除了 Unicode 字母、美元符号和下划线，还可以用数字`0-9`。

### 1.2 变量的重复赋值

如果变量的名字相同，且在同一作用域下，后面代码的赋值会改变原有的值

```js
var a = 0;
var a // 如果没有赋值，则不会改变
a = 1; ==> var a = 1 // 两种写法都会得到相同的结果
console.log(a) // 1
```

### 1.3undefined 和 not defined 的区别

**<font color="red">undefined：表示变量定义了，但没有赋值</font>**

**<font color="orange">not defined：表示变量未定义，没有声明</font>**

## 2.注释

**由于历史上 JavaScript 可以兼容 HTML 代码的注释，所以`<!--`和`-->`也被视为合法的单行注释。**

```js
x = 1; <!-- x = 2;
--> x = 3;
```

上面代码中，只有`x = 1`会执行，其他的部分都被注释掉了。

需要注意的是，`-->`只有在行首，才会被当成单行注释，否则会当作正常的运算。

## 3.条件语句

### 3.1if 结构

当只有一条语句的时候可以简写

```js
if (true) console.log(1);
// or
if (true) console.log(1);
```

**`else`代码块总是与离自己最近的那个`if`语句配对。**

```js
if(..){
    ...
}else if(..){
    ...
}else{
    ...
}
```

```js
var m = 1;
var n = 2;

if (m !== 1)
  if (n === 2) console.log("hello");
  else console.log("world");
```

实际上是这样的

```js
if (m !== 1) {
  if (n === 2) {
    console.log("hello");
  } else {
    console.log("world");
  }
}
```

### 3.2 swich 结构

```js
switch (fruit) {
  case "banana":
    // ...
    break;
  case "apple":
    // ...
    break;
  default:
  // ...
}
```

上面代码根据变量`fruit`的值，选择执行相应的`case`。如果所有`case`都不符合，则执行最后的`default`部分。需要注意的是，每个`case`代码块内部的`break`语句不能少，否则会接下去执行下一个`case`代码块，而不是跳出`switch`结构。

### 3.3 三元表达式

```js
var even = n % 2 === 0 ? true : false;
```

`n` 是偶数的时候，`even`的值为`true`，否则为`false`

## 4 循环

### 4.1while 循环

`While`语句包括一个循环条件和一段代码块，只要条件为真，就不断循环执行代码块。

```js
while (条件) 语句;

// 或者
while (条件) 语句;

// 或者
while (条件) {
  语句;
}
```

### 4.2for 循环

```js
for (初始化表达式; 条件; 递增表达式) 语句;

// 或者
for (初始化表达式; 条件; 递增表达式) {
  语句;
}
```

**`for`语句后面的括号里面，有三个表达式。**

- 初始化表达式（initialize）：确定循环变量的初始值，只在循环开始时执行一次。
- 条件表达式（test）：每轮循环开始时，都要执行这个条件表达式，只有值为真，才继续进行循环。
- 递增表达式（increment）：每轮循环的最后一个操作，通常用来递增循环变量。

```js
var x = 3;
for (var i = 0; i < x; i++) {
  console.log(i);
}
// 0
// 1
// 2
```

### 4.3do while 循环

<font color="red">`do...while`循环与`while`循环类似，唯一的区别就是先运行一次循环体，然后判断循环条件。</font>

```js
var x = 3;
var i = 0;

do {
  console.log(i);
  i++;
} while (i < x);
```

不管条件是否为真，`do...while`循环**至少运行一次**，这是这种结构最大的特点。另外，`while`语句后面的分号注意不要省略。

```js
var i = 0;

do {
  console.log(i);
  i++;
} while (false);
// 输出0
```

### 4.4 关键字

`break`：语句用于跳出代码块或循环；在循环体内结束整个循环过程

`continue` ：结束本次的循环，直接进行下一次的循环

`return` ：在循环中，直接跳出当前的方法,返回到该调用的方法的语句处,继续执行。

return 下面的代码不会继续执行。return 返回某个东西

```js
function aa() {
  var a = 1;
  return a; // 调用aa函数的时候，得到返回值 a的值
  console.log(b); // 这句代码不会执行
}
var bb = aa(); // 1
```

### 4.5 标签

JavaScript 语言允许，语句的前面有标签（label），相当于定位符，用于跳转到程序的任意位置，标签的格式如下。

```
label:
  语句
```

标签可以是任意的标识符，但不能是保留字，语句部分可以是任意语句。

标签通常与`break`语句和`continue`语句配合使用，跳出特定的循环。

```js
top: for (var i = 0; i < 3; i++) {
  for (var j = 0; j < 3; j++) {
    if (i === 1 && j === 1) break top;
    console.log("i=" + i + ", j=" + j);
  }
}
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
```

上面代码为一个双重循环区块，`break`命令后面加上了`top`标签（注意，`top`不用加引号），满足条件时，直接跳出双层循环。如果`break`语句后面不使用标签，则只能跳出内层循环，进入下一次的外层循环。

标签也可以用于跳出代码块。

```js
foo: {
  console.log(1);
  break foo;
  console.log("本行不会输出");
}
console.log(2);
// 1
// 2
```

上面代码执行到`break foo`，就会跳出区块。

`continue`语句也可以与标签配合使用。

```js
top: for (var i = 0; i < 3; i++) {
  for (var j = 0; j < 3; j++) {
    if (i === 1 && j === 1) continue top;
    console.log("i=" + i + ", j=" + j);
  }
}
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
// i=2, j=0
// i=2, j=1
// i=2, j=2
```

上面代码中，`continue`命令后面有一个标签名，满足条件时，会跳过当前循环，直接进入下一轮外层循环。如果`continue`语句后面不使用标签，则只能进入下一轮的内层循环。

## 5.数据类型

JavaScript 语言的每一个值，都属于某一种数据类型。JavaScript 的数据类型，共有六种。（ES6 又新增了第七种 Symbol 类型的值）

- 数值（number）：整数和小数（比如`1`和`3.14`）
- 字符串（string）：文本（比如`Hello World`）。
- 布尔值（boolean）：表示真伪的两个特殊值，即`true`（真）和`false`（假）
- `undefined`：表示“未定义”或不存在，即由于目前没有定义，所以此处暂时没有任何值
- `null`：表示空值，即此处的值为空。
- 对象（object）：各种值组成的集合。

通常，数值、字符串、布尔值这三种类型，合称为原始类型（primitive type）的值，即它们是最基本的数据类型，不能再细分了。对象则称为合成类型（complex type）的值，因为一个对象往往是多个原始类型的值的合成，可以看作是一个存放各种值的容器。至于`undefined`和`null`，一般将它们看成两个特殊值。

对象是最复杂的数据类型，又可以分成三个子类型。

- 狭义的对象（object）
- 数组（array）
- 函数（function）

狭义的对象和数组是两种不同的数据组合方式，除非特别声明，本笔记的“对象”都特指狭义的对象。**函数**其实是处理数据的方法，JavaScript 把它当成一种数据类型，可以**赋值给变量**，这为编程带来了很大的灵活性，也为 JavaScript 的“**函数式编程**”奠定了基础。

### 5.1typeof 运算符

| 类型      | 返回值    |
| --------- | --------- |
| 数值      | number    |
| 字符串    | string    |
| 布尔值    | boolean   |
| 函数      | function  |
| 对象      | object    |
| 数组      | object    |
| undefined | undefined |
| null      | object    |

`null`的类型是`object`，这是由于历史原因造成的。1995 年的 JavaScript 语言第一版，只设计了五种数据类型（对象、整数、浮点数、字符串和布尔值），没考虑`null`，只把它当作`object`的一种特殊值。后来`null`独立出来，作为一种单独的数据类型，为了兼容以前的代码，`typeof null`返回`object`就没法改变了。

### 5.2null 和 undefined

**在`if`语句中，它们都会被自动转为`false`，相等运算符（`==`）甚至直接报告两者相等。**

区别是这样的：`null`是一个表示“空”的对象，转为数值时为`0`；`undefined`是一个表示"此处无定义"的原始值，转为数值时为`NaN`。

```js
Number(null); // 0
5 + null; // 5

Number(undefined); // NaN
5 + undefined; // NaN
```

#### 5.2.1 用法

`null`表示空值，即该处的值现在为空。**调用函数时，某个参数**未设置任何值，这时就可以传入`null`，表示该参数为空。比如，某个函数接受引擎抛出的错误作为参数，如果运行过程中未出错，那么这个参数就会传入`null`，表示未发生错误。

### 5.3 布尔值

如果 JavaScript 预期某个位置应该是布尔值，会将该位置上现有的值自动转为布尔值。

**转换规则是除了下面六个值被转为`false`，其他值都视为`true`。**

- `undefined`
- `null`
- `false`
- `0`
- `NaN`
- `""`或`''`（空字符串）

注意，空数组（`[]`）和空对象（`{}`）对应的布尔值，都是`true`。

## 6.数值

### 6.1 整数和浮点数

JavaScript 内部，所有数字都是以 64 位浮点数形式储存，即使整数也是如此。所以，`1`与`1.0`是相同的，是同一个数。

这就是说，JavaScript 语言的底层根本没有整数，所有数字都是小数（64 位浮点数）。容易造成混淆的是，某些运算只有整数才能完成，此时 JavaScript 会自动把 64 位浮点数，转成 32 位整数

**由于浮点数不是精确的值，所以涉及小数的比较和运算要特别小心。**

```js
0.1 + 0.2 === 0.3;
// false

0.3 /
  0.1(
    // 2.9999999999999996

    0.3 - 0.2
  ) ===
  0.2 - 0.1;
// false
```

### 6.2 数值精度

根据国际标准 IEEE 754，JavaScript 浮点数的 64 个二进制位，从最左边开始，是这样组成的。

- 第 1 位：符号位，`0`表示正数，`1`表示负数
- 第 2 位到第 12 位（共 11 位）：指数部分
- 第 13 位到第 64 位（共 52 位）：小数部分（即有效数字）

符号位决定了一个数的正负，指数部分决定了数值的大小，小数部分决定了数值的精度。

指数部分一共有 11 个二进制位，因此大小范围就是 0 到 2047。IEEE 754 规定，如果指数部分的值在 0 到 2047 之间（不含两个端点），那么有效数字的第一位默认总是 1，不保存在 64 位浮点数之中。也就是说，有效数字这时总是`1.xx...xx`的形式，其中`xx..xx`的部分保存在 64 位浮点数之中，最长可能为 52 位。因此，JavaScript 提供的有效数字最长为 53 个二进制位。

```
(-1)^符号位 * 1.xx...xx * 2^指数部分
```

上面公式是正常情况下（指数部分在 0 到 2047 之间），一个数在 JavaScript 内部实际的表示形式。

精度最多只能到 53 个二进制位，这意味着，绝对值小于 2 的 53 次方的整数，即-253 到 253，都可以精确表示。

```js
Math.pow(2, 53); // 2的53次幂
// 9007199254740992

Math.pow(2, 53) + 1;
// 9007199254740992

Math.pow(2, 53) + 2;
// 9007199254740994

Math.pow(2, 53) + 3;
// 9007199254740996

Math.pow(2, 53) + 4;
// 9007199254740996
```

上面代码中，大于 2 的 53 次方以后，整数运算的结果开始出现错误。所以，大于 2 的 53 次方的数值，都无法保持精度。由于 2 的 53 次方是一个 16 位的十进制数值，

**所以简单的法则就是，JavaScript 对 15 位的十进制数都可以精确处理。**

### 6.3 数值范围

根据标准，64 位浮点数的指数部分的长度是 11 个二进制位，意味着指数部分的最大值是 2047（2 的 11 次方减 1）。也就是说，64 位浮点数的指数部分的值最大为 2047，分出一半表示负数，则 JavaScript 能够表示的数值范围为 21024 到 2-1023（开区间），超出这个范围的数无法表示。

<font color="red">如果一个数大于等于 2 的 1024 次方，那么就会发生“正向溢出”，即 JavaScript 无法表示这么大的数，这时就会返回`Infinity`。</font>

```js
Math.pow(2, 1024); // Infinity
```

如果一个数小于等于 2 的-1075 次方（指数部分最小值-1023，再加上小数部分的 52 位），那么就会发生为“负向溢出”，即 JavaScript 无法表示这么小的数，这时会直接返回 0。

```js
Math.pow(2, -1075); // 0
```

JavaScript 提供`Number`对象的`MAX_VALUE`和`MIN_VALUE`属性，返回可以表示的具体的最大值和最小值。

```js
Number.MAX_VALUE; // 1.7976931348623157e+308
Number.MIN_VALUE; // 5e-324
```

### 6.4 数字表示法

#### 6.4.1 科学计数法

`e`后面直接跟着数字`x`，表示省略了`+`号，和带`+`号的效果一样，表示小数点<font color="red">向右</font>移动`x`位数

如果`e`后面跟着`-`号，即表示小数点<font color="red">向左</font>移动`x`位数

> 注意，和数字原本的正负没关系，不要被数字前面的负号`-`给混乱了

```js
123e3; // 123000
123e-3 - // 0.123
  3.1e1; // -31
0.1e-2; // 0.001
```

**以下两种情况，JavaScript 会自动将数值转为科学计数法表示，其他情况都采用字面形式直接表示。**

1. **小数点前的数字多于 21 位。**

   ```js
   1234567890123456789012;
   // 1.2345678901234568e+21
   ```

2. **小数点后的零多于 5 个。**

   ```js
   0.0000003; // 3e-7
   ```

### 6.5 特殊数值

#### 6.5.1 正零和负零

JavaScript 的 64 位浮点数之中，有一个二进制位是符号位。这意味着，任何一个数都有一个对应的负值，就连`0`也不例外。

JavaScript 内部实际上存在 2 个`0`：一个是`+0`，一个是`-0`，区别就是 64 位浮点数表示法的符号位不同。它们是等价的。

唯一有区别的场合是，`+0`或`-0`当作分母，返回的值是不相等的。

```js
1 / +0 === 1 / -0; // false
infinity - infinity;
```

#### 6.5.2 NaN

> 非数字
>
> 它属于数字类型，'number'

`0`除以`0`也会得到`NaN`。

```js
0 / 0; //NaN
```

`NaN`不等于任何值，包括它本身。

```js
NaN === NaN; // false
```

数组的`indexOf`方法内部使用的是严格相等运算符，所以该方法对`NaN`不成立。

```js
[NaN].indexOf(NaN); // -1
```

#### 6.5.3 infinity

`Infinity`表示“无穷”，用来表示两种场景。一种是一个正的数值太大，或一个负的数值太小，无法表示；另一种是非 0 数值除以 0，得到`Infinity`。

`Infinity`有正负之分，`Infinity`表示正的无穷，`-Infinity`表示负的无穷。

`Infinity`大于一切数值（除了`NaN`），`-Infinity`小于一切数值（除了`NaN`）。

```js
Infinity >
  1000 - // true
    Infinity <
  -1000; // true
```

### 6.6 与数值相关的全局方法

#### 6.6.1parseInt

**`parseInt`方法用于将<font color="red">字符串</font>转为整数。**

- 如果字符串头部有空格，空格会被自动去除。

- <font color="blue">如果`parseInt`的参数不是字符串，则会先转为字符串再转换。</font>

- 字符串转为整数的时候，是一个个字符依次转换，如果遇到不能转为数字的字符，就不再进行下去，返回已经转好的部分。

- 如果字符串的第一位不是数字<font color="red">(除了`+`，`-`号)，</font>则返回的结果的`NaN`

  ```js
  parseInt("   81"); // 81

  parseInt(1.23); // 1
  // 等同于
  parseInt("1.23"); // 1

  parseInt("15e2"); // 15

  parseInt("a1"); // NaN
  parseInt("+1"); // 1
  ```

所以，`parseInt`的返回值只有两种可能，要么是一个十进制整数，要么是`NaN`。

- 如果字符串以`0x`或`0X`开头，`parseInt`会将其按照十六进制数解析。
- 如果字符串以`0`开头，将其按照 10 进制解析。

```js
parseInt("0x10"); // 16

parseInt("011"); // 11
```

`parseInt`方法还可以接受第二个参数（2 到 36 之间），表示被解析的值的进制，返回该值对应的十进制数。默认情况下，`parseInt`的第二个参数为 10，即默认是十进制转十进制。**我们平时用到的数字就是十进制的**

#### 6.6.2 parseFloat

`parseFloat`方法用于将一个字符串转为浮点数。大部分特性和`parseInt`一样

- 如果字符串符合科学计数法，则会进行相应的转换。
- 尤其值得注意，`parseFloat`会将空字符串转为`NaN`

```js
parseFloat("314e-2"); // 3.14
parseFloat("0.0314E+2"); // 3.14

parseFloat(""); // NaN
parseFloat([]); // NaN
```

注意：parseInt 和 parseFloat，不用于 Number()方法的转换规则

```js
parseFloat(""); // NaN
Number(""); // 0

parseFloat("123.45#"); // 123.45
Number("123.45#"); // NaN
```

#### 6.6.3 isNaN

`isNaN`方法可以用来判断一个值是否为`NaN`。

- 但是，`isNaN`只对数值有效，如果传入其他值，**会被先转成数值**。比如，传入字符串的时候，字**符串会被先转成`NaN`，所以最后返回`true` **，这一点要特别引起注意。也就是说，`isNaN`为`true`的值，有可能不是`NaN`，而是一个字符串。
- 出于同样的原因，对于对象和数组，`isNaN`也返回`true`。
- 但如果是空数组，或者只有一个数值成员的数组，**还是因为`Number`方法的隐式转换**

```js
isNaN("gauhar"); // true
相当于;
isNaN(Number("gauhar"));

isNaN([]); // false
isNaN([123]); // false
isNaN(["123"]); // false
```

#### 6.6.4 isFinite()

`isFinite`方法返回一个布尔值，表示某个值是否为正常的数值。

```js
isFinite(Infinity); // false
isFinite(-Infinity); // false
isFinite(NaN); // false
isFinite(undefined); // false
isFinite(null); // true
isFinite(-1); // true
```

除了`Infinity`、`-Infinity`、`NaN`和`undefined`这几个值会返回`false`，`isFinite`对于其他的数值都会返回`true`。

## 7.字符串

由于 HTML 语言的属性值使用双引号，所以很多项目约定 JavaScript 语言的字符串只使用单引号

字符串默认只能写在一行内，分成多行将会报错。多行的时候可以在每行的行末加上反斜杠`\`，或者使用`+`连接，或者是用 es6 的模板字符串

### 7.1 转义

> 反斜杠（\）在字符串内有特殊含义，用来表示一些特殊字符，所以又称为转义符。

| \0     | null              |
| ------ | ----------------- |
| \b     | 后退键            |
| \f     | 换页符            |
| **\n** | **换行符**        |
| **\r** | **回车键**        |
| **\t** | **制表符 Tab 键** |
| \v     | 垂直制表符        |
| `\'`   | 单引号            |
| `\"`   | 双引号            |
| `\`\   | 反斜杠            |

反斜杠还有三种特殊用法。

（1）`\HHH`

反斜杠后面紧跟三个八进制数（`000`到`377`），代表一个字符。`HHH`对应该字符的 Unicode 码点，比如`\251`表示版权符号。显然，这种方法只能输出 256 种字符。

（2）`\xHH`

`\x`后面紧跟两个十六进制数（`00`到`FF`），代表一个字符。`HH`对应该字符的 Unicode 码点，比如`\xA9`表示版权符号。这种方法也只能输出 256 种字符。

（3）`\uXXXX`

`\u`后面紧跟四个十六进制数（`0000`到`FFFF`），代表一个字符。`XXXX`对应该字符的 Unicode 码点，比如`\u00A9`表示版权符号。

```js
"\251"; // "©"
"\xA9"; // "©"
"\u00A9"; // "©"

"\172" === "z"; // true
"\x7A" === "z"; // true
"\u007A" === "z"; // true
```

[有关字符串的方法](https://www.runoob.com/jsref/jsref-obj-string.html)

### 7.2 Base64 转码

所谓 Base64 就是一种编码方法，可以将任意值转成 0 ～ 9、A ～ Z、a-z、`+`和`/`这 64 个字符组成的可打印字符。使用它的主要目的，不是为了加密，而是为了不出现特殊字符，简化程序的处理。

JavaScript 原生提供两个 Base64 相关的方法。这两个不可以转化**非 ASCII 码**,比如说：中文

- `btoa()`：任意值转为 Base64 编码
- `atob()`：Base64 编码转为原来的值

```js
btoa("gauhar");
("Z2F1aGFy");

btoa("呵"); // 报错
// Uncaught DOMException: Failed to execute 'btoa' on 'Window': The string to be encoded contains characters outside of the Latin1 range.
```

要将非 ASCII 码字符转为 Base64 编码，必须中间插入一个转码环节，再使用这两个方法。

```js
function b64Encode(str) {
  return btoa(encodeURIComponent(str));
}

function b64Decode(str) {
  return decodeURIComponent(atob(str));
}

b64Encode("你好"); // "JUU0JUJEJUEwJUU1JUE1JUJE"
b64Decode("JUU0JUJEJUEwJUU1JUE1JUJE"); // "你好"
```

## 8.对象

### 8.1 键名

对象的所有键名都是字符串（ES6 又引入了 Symbol 值也可以作为键名），所以加不加引号`""`都可以。

```js
let obj = {
  name: "gauhar",
  sex: "男",
};
```

如果键名是数值，会被自动转为字符串。

如果键名不符合标识名的条件（比如第一个字符为数字，或者含有空格或运算符），且也不是数字，则必须加上引号，否则会报错。

```js
// 报错
var obj = {
  1a: 'Hello World'
};
```

对象的每一个键名又称为“属性”（property），它的“键值”可以是任何数据类型。<font color="red">如果一个属性的值为函数，通常把这个属性称为“方法”</font>>，它可以像函数那样调用。

### 8.2 对象的引用

如果不同的变量名指向同一个对象，那么它们都是这个对象的引用，也就是说指向同一个内存地址。修改其中一个变量，会影响到其他所有变量。

```js
let obj = {
  a: 1,
};
let bbb = obj;
bbb.a; // 1
bbb.a = 2;
obj.a; // 2
```

此时，如果取消某一个变量对于原对象的引用，不会影响到另一个变量。因为赋值的时候是把内存地址赋值给变量。但这种情况只限于<font color='red'>复杂类型</font>

如果是<font color='blue'>基本数据类型</font>，这种赋值是对**<font color='orange'>值</font>**的拷贝，并不是拷贝内存地址，所以两个变量不会互相影响

### 8.3 对象属性的获取方式

两种方法，一种是通过对象`.`属性获取；另一种是通过对象`['键名']`获取，注意中括号里面的是**字符串**

```js
let obj = {
  name: "gauhar",
  sex: "男",
};
obj.name; // 'gauhar'
obj["sex"]; // '男'
```

注意中括号里面如果没有使用`''`引号包括的话，将会使用变量

```js
let obj = {};
console.log(obj[a]); // a is not defined
```

这时候的 a 是变量，而 a 并没有定义

方括号运算符内部还可以使用表达式。比如说：在里面拼接字符串

当键名是数字的时候，只能通过对象`['键名']`获取

### 8.4 属性的查看

查看一个对象本身的所有属性，可以使用`Object.keys`方法。

```js
var obj = {
  key1: 1,
  key2: 2,
};

Object.keys(obj);
// ['key1', 'key2']
```

### 8.5 属性的删除：delete 命令

`delete`命令用于删除对象的属性，删除成功后返回`true`。

```js
var obj = { p: 1 };
Object.keys(obj); // ["p"]

delete obj.p; // true
obj.p; // undefined
Object.keys(obj); // []
```

上面代码中，`delete`命令删除对象`obj`的`p`属性。删除后，再读取`p`属性就会返回`undefined`，而且`Object.keys`方法的返回值也不再包括该属性。

注意，删除一个不存在的属性，`delete`不报错，而且返回`true`。

```js
var obj = {};
delete obj.p; // true
```

上面代码中，对象`obj`并没有`p`属性，但是`delete`命令照样返回`true`。因此，不能根据`delete`命令的结果，认定某个属性是存在的。

只有一种情况，`delete`命令会返回`false`，那就是该属性存在，且不得删除。使用`Object.defineProperty`设置不可删除属性

**另外，需要注意的是，`delete`命令只能删除对象本身的属性，无法删除继承的属性**

### 8.6 属性是否存在：in 运算符

`in`运算符用于检查对象是否包含某个属性（注意，检查的是键名，不是键值），如果包含就返回`true`，否则返回`false`。它的左边是一个字符串，表示属性名，右边是一个对象。

```js
var obj = { p: 1 };
"p" in obj; // true
"toString" in obj; // true
```

`in`运算符的一个问题是，它不能识别哪些属性是对象自身的，哪些属性是继承的。就像上面代码中，对象`obj`本身并没有`toString`属性，但是`in`运算符会返回`true`，因为这个属性是继承的。

这时，可以使用对象的`hasOwnProperty`方法判断一下，是否为对象自身的属性。

```js
var obj = {};
if ("toString" in obj) {
  console.log(obj.hasOwnProperty("toString")); // false
}
```

toString 方法是原型链上的方法

### 8.7 for in

`for...in`循环用来遍历一个对象的全部属性。

```js
var obj = { a: 1, b: 2, c: 3 };

for (var i in obj) {
  console.log("键名：", i);
  console.log("键值：", obj[i]);
}
// 键名： a
// 键值： 1
// 键名： b
// 键值： 2
// 键名： c
// 键值： 3
```

`for...in`循环有两个使用注意点。

- 它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。
- 它不仅遍历对象自身的属性，还遍历继承的属性。一般配合`hasOwnProperty`方法一起使用，判断是否是本身的属性

```js
var person = { name: "gauhar" };

for (var key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(key);
  }
}
// name
```

### 8.8with 语句(一般情况下不推荐使用)

它的作用是操作同一个对象的多个属性时，提供一些书写的方便。

```js
var obj = {
  p1: 1,
  p2: 2,
};
with (obj) {
  p1 = 4;
  p2 = 5;
}
// 等同于
obj.p1 = 4;
obj.p2 = 5;
```

注意，如果`with`区块内部有变量的赋值操作，必须是当前对象已经存在的属性，否则会创造一个当前作用域的**全局变量。**

```js
let obj = {};
with (obj) {
  a = 1;
}
obj.a; // undefined
window.a; // 1
```

## 9.函数

### 9.1 斐波那契数列

```js
function fib(num) {
  if (num === 0) return 0;
  if (num === 1) return 1;
  return fib(num - 2) + fib(num - 1);
}
```

### 9.2name 属性

函数的`name`属性返回函数的名字。

```js
function f1() {}
f1.name; // "f1"
```

如果是通过变量赋值定义的函数，那么`name`属性返回变量名。

```js
var f2 = function () {};
f2.name; // "f2"
```

但是，上面这种情况，只有在变量的值是一个匿名函数时才是如此。如果变量的值是一个具名函数，那么`name`属性返回`function`关键字之后的那个函数名。

```js
var f3 = function myName() {};
f3.name; // 'myName'
```

上面代码中，`f3.name`返回函数表达式的名字。**注意，真正的函数名还是`f3`，而`myName`这个名字只在函数体内部可用。**

`name`属性的一个用处，就是获取参数函数的名字。

```js
var myFunc = function () {};

function test(f) {
  console.log(f.name);
}

test(myFunc); // myFunc
```

上面代码中，函数`test`内部通过`name`属性，就可以知道传入的参数是什么函数。

### 9.3 作用域

```js
var a = 1;
var x = function () {
  console.log(a);
};

function f() {
  var a = 2;
  x();
}

f(); // 1
```

总之，函数执行时所在的作用域，**是定义时的作用域**，而不是调用时所在的作用域。

#### 9.4length 属性

length 属性，返回函数预定义的参数(形参)的个数，注意不是实参的个数

```js
function aa(b, c) {
  aa.length; //2
}
```

### 9.5 传值

如果函数参数是复合类型的值**（数组、对象、其他函数）**，传递方式是传址传递（pass by reference）。也就是说，传入函数的原始值的**内存地址**，因此在函数内部修改参数，将会影响到原始值。

```js
var obj = { p: 1 };

function f(o) {
  o.p = 2;
}
f(obj);

obj.p; // 2
```

注意，如果函数内部修改的，不是参数对象的某个属性，而是替换掉整个参数，这时不会影响到原始值。

```js
var obj = [1, 2, 3];

function f(o) {
  o = [2, 3, 4];
}
f(obj);

obj; // [1, 2, 3]
```

因为这时对`o`的赋值了一个新的数组，这个数组是一个新的内存地址。

### 9.6 闭包

闭包的最大用处有两个，一个是可以读取函数内部的变量，另一个就是让这些变量始终保持在内存中

```js
function createIncrementor(start) {
  return function () {
    return start++;
  };
}

var inc = createIncrementor(5);

inc(); // 5
inc(); // 6
inc(); // 7
```

上面代码中，`start`是函数`createIncrementor`的内部变量。通过闭包，`start`的状态被保留了，每一次调用都是在上一次调用的基础上进行计算。从中可以看到，闭包`inc`使得函数`createIncrementor`的内部环境，一直存在。所以，闭包可以看作是函数内部作用域的一个接口。

`inc`始终在内存中，而`inc`的存在依赖于`createIncrementor`，因此也始终在内存中，不会在调用结束后，被垃圾回收机制回收。

**闭包的另一个用处，是封装对象的私有属性和私有方法。**

类的构造函数里定义的 function，即为私有方法；而在构造函数里用 var 声明的变量，也相当于是私有变量。

**下面的代码中，调用 Person 时返回了一个对象，因此才能调用`getAge`和`setAge`，这两个私有方法。**或者把属性和方法挂载到`this`上，即可访问。

```js
function Person(name) {
  var _age; // 私有
  function setAge(n) {
    _age = n;
  }
  function getAge() {
    return _age;
  }

  return {
    name: name,
    getAge: getAge,
    setAge: setAge,
  };
  相当于;
  this.name = name;
  this.getAge = getAge;
  this.setAge = setAge;
}

var p1 = Person("张三");
p1.setAge(25);
p1.getAge(); // 25
```

上面代码中，函数`Person`的内部变量`_age`，通过闭包`getAge`和`setAge`，变成了返回对象`p1`的私有变量。

注意，外层函数每次运行，都会生成一个新的闭包，而这个闭包又会保留外层函数的内部变量，所以内存消耗很大。因此不能滥用闭包，否则会造成网页的性能问题。

### 9.7 自调用函数 IIFE

**不要让`function`出现在行首**，让引擎将其理解成一个表达式。便可以通过`()`调用函数

```js
(function aa(){
    ...
})()
// or
(function aa(){
    ...
}())
// or
!function () { /* code */ }();
~function () { /* code */ }();
-function () { /* code */ }();
+function () { /* code */ }();
```

通常情况下，只对匿名函数使用这种“立即执行的函数表达式”。它的目的有两个：一是不必为函数命名，避免了污染全局变量；二是 IIFE 内部形成了一个单独的作用域，可以封装一些外部无法读取的私有变量。

### 9.8 eval 命令

`eval`命令接受一个字符串作为参数，并将这个**字符串当作语句执行**。

eval 命令没有自己的作用域，所以执行的时候会在当前作用域下，因此会影响到相同的变量，如

```js
var a = 1;
eval("var a = 2");
console.log(a); // 2
```

所以在 js 的严格模式下规定，`eval`内部声明的变量，不会影响到外部作用域。

```js
(function f() {
  "use strict";
  eval("var foo = 123");
  console.log(foo); // ReferenceError: foo is not defined
})();
```

<font color="red">`eval`的本质是在当前作用域之中，注入代码。由于安全风险和不利于 JavaScript 引擎优化执行速度，所以一般不推荐使用。</font>

### 9.9 严格模式

代码：

```js
"use strict";
```

## 10.数组

> 本质是一个特殊的对象，对象的键名一律为字符串，数组的键名其实也是字符串

使用`delete`命令删除一个数组成员，会形成空位，并且不会影响`length`属性。

### 类似数组的对象

如果一个对象的所有键名都是正整数或零，并且有`length`属性，那么这个对象就很像数组，语法上称为“类似数组的对象”（array-like object）。

典型的“类似数组的对象”是函数的`arguments`对象，以及大多数 DOM 元素集，还有字符串。

## 11.算数运算符

### 11.1 对象相加

先会执行对象的`valueOf`，返回对象本身，然后执行 toString 方法，转换为字符串，在相加。在谷歌控制台中执行`{p: 1} + 2`，你会得到`2`

```js
var obj = { p: 1 };
obj + 2; // "[object Object]2"
```

那当然，自己可以重写`valueOf`或者`toString`方法，从而得到想要的东西。

### 11.2 余数运算符

取模，就是取两数相除的余数

```js
5 % 3; // 2
```

余数的正负，由第一个数决定

```js
-5 % 3; // -2
```

## 12. Number()

> 将参数转换位数字

该方法实际的运算规则：

第一步，调用对象自身的`valueOf`方法。如果返回原始类型的值，则直接对该值使用`Number`函数，不再进行后续步骤。

第二步，如果`valueOf`方法返回的还是对象，则改为调用对象自身的`toString`方法。如果`toString`方法返回原始类型的值，则对该值使用`Number`函数，不再进行后续步骤。

第三步，如果`toString`方法返回的是对象，就报错。

```js
Number({
  valueOf: function () {
    return 2;
  },
});
// 2

Number({
  toString: function () {
    return 3;
  },
});
// 3

Number({
  toString: function () {
    return {};
  },
});
// error

Number({
  valueOf: function () {
    return 2;
  },
  toString: function () {
    return 3;
  },
});
// 2
```

## 13. String()

`String`方法背后的转换规则，与`Number`方法基本相同，只是互换了`valueOf`方法和`toString`方法的执行顺序。

1. 先调用对象自身的`toString`方法。如果返回原始类型的值，则对该值使用`String`函数，不再进行以下步骤。
2. 如果`toString`方法返回的是对象，再调用原对象的`valueOf`方法。如果`valueOf`方法返回原始类型的值，则对该值使用`String`函数，不再进行以下步骤。
3. 如果`valueOf`方法返回的是对象，就报错。

## 14. Boolean()

`Boolean()`函数可以将任意类型的值转为布尔值。

它的转换规则相对简单：除了以下五个值的转换结果为`false`，其他的值全部为`true`。

- `undefined`
- `null`
- `0`（包含`-0`和`+0`）
- `NaN`
- `''`（空字符串）

## 15. 报错对象 Error

包含六个子对象：

1. SyntaxError：语法错误
2. ReferenceError：引用不存在变量，赋值错误，如赋值给不可赋值的对象，this = 1
3. RangeError：数值超出有效范围
4. TypeError：类型错误，变量或参数不是预期类型时发生的错误，如：new 1；... is not a function
5. URI：URI 相关函数的参数不正确时抛出的错误
6. Eval：`eval`函数没有被正确执行时，会抛出`EvalError`错误

### 15.1 try...catch

JavaScript 提供了`try...catch`结构，允许对错误进行处理，选择是否往下执行。

```js
try {
  fn();
} catch (err) {
  // throw Error(err)  // 抛出异常后，下面代码console.log(111);不会执行
  console.log(err);
}
console.log(111);
```

`try...catch`结构允许在最后添加一个`finally`代码块，表示不管是否出现错误，都必需在最后运行的语句。

```js
try {
  fn();
} catch (err) {
  throw Error(err); // 抛出异常的代码会在最后执行
} finally {
  console.log(111);
}
```

**如果 catch 中有`return`，`throw`代码，那么 finally 中的代码会先执行**

```js
function f() {
  try {
    throw "出错了！";
  } catch (e) {
    console.log("捕捉到内部错误");
    throw e; // 这句原本会等到finally结束再执行
  } finally {
    return false; // 直接返回
  }
}

try {
  f();
} catch (e) {
  // 此处不会执行   因为在这代码之前执行了finally中的return false

  console.log('caught outer "bogus"');
}
```

## 16. console 对象

`console.log`方法支持以下占位符，不同类型的数据必须使用对应的占位符。

- `%s` 字符串
- `%d` 整数
- `%i` 整数
- `%f` 浮点数
- `%o` 对象的链接
- `%c` CSS 格式字符串

**值的注意的是：css 代码作用的是 %c 后面的代码，如下面代码中，css 的代码不会影响到数字 12！！**

```js
console.log(
  "%d %c %s ",
  12,
  "color:red;background:yellow;font-size:24px",
  "这段文字是红色的，背景是黄色的"
);
```

### 16.1 console.count()

> 可以查看函数、循环多少次，不带参数的时候，默认是 default

```js
for (let index = 0; index < 3; index++) {
  console.count("index");
}
// index: 1
// index: 2
// index: 3
```

### 16.2 console.assert()

> 第一个参数是表达式，第二个参数是字符串。只有当第一个参数为`false`，才会提示有错误，在控制台输出第二个参数，否则不会有任何结果。

下面是一个例子，判断子节点的个数是否大于等于 500。

```js
console.assert(list.childNodes.length < 500, "节点个数大于等于500");
```

## 17. Object

```js
var obj = Object();
// 等同于
var obj = Object(undefined);
var obj = Object(null);

obj instanceof Object; // true
```

上面代码的含义，是将`undefined`和`null`转为对象，结果得到了一个空对象`obj`。

`instanceof`运算符用来验证，一个对象是否为指定的构造函数的实例。`obj instanceof Object`返回`true`，就表示`obj`对象是`Object`的实例。

`Object(value)`与`new Object(value)`两者的语义是不同的，`Object(value)`表示将`value`转成一个对象，`new Object(value)`则表示新生成一个对象，它的值是`value`。

### 17.1 静态方法

#### 17.1.1 `Object.keys()`和`Object.getOwnPropertyNames()`

> 遍历对象**自身**的属性

`Object.keys()`和`Object.getOwnPropertyNames()`只有在涉及不可枚举属性时，才会有不一样的结果。`Object.keys`方法只返回可枚举的属性，`Object.getOwnPropertyNames`方法还返回不可枚举的属性名。

##### 17.1.1.1 枚举类型

如果一个属性没有被标识为可枚举，循环将忽略它在对象内。也就是说在使用`for in`遍历对象的时候，会忽略不可枚举属性

```js
let obj = {
  name: "gauhar",
  age: 21,
};
// 通过Object.defineProperty方法设置不可枚举类型属性
Object.defineProperty(obj, "bbb", {});
let arr = Object.keys(obj);
let arr2 = Object.getOwnPropertyNames(obj);
console.log(arr); // ["name", "age"]
console.log(arr2); //  ["name", "age", "bbb"]
```

### 17.2 hasOwnProperty()

> 会返回一个布尔值，指示对象自身属性中是否具有指定的属性（也就是，是否有指定的键）。

### 17.3 属性描述对象

> 对象的属性

```js
{
  value: 123,
  writable: false,
  enumerable: true,
  configurable: false,
  get: undefined,
  set: undefined
}
```

1. `value`

`value`是该属性的属性值，默认为`undefined`。

2. `writable`

`writable`是一个布尔值，表示属性值（value）是否可改变（即是否可写），默认为`true`。

3. `enumerable`

`enumerable`是一个布尔值，表示该属性是否可遍历，默认为`true`。如果设为`false`，会使得某些操作（比如`for...in`循环、`Object.keys()`、`JSON.stringify()`）跳过该属性。 注意：`for...in`循环包括继承的属性（因此经常配合`hasOwnProperty`判断是否是自身属性），`Object.keys`方法不包括继承的属性。

4. `configurable`

`configurable`是一个布尔值，表示可配置性，默认为`true`。如果设为`false`，将阻止某些操作改写该属性，比如无法<font color='red'>**删除该属性**</font>，也<font color='red'>**不得改变该属性的属性描述对象**</font>。也就是说，`configurable`属性控制了属性描述对象的可写性。

> 注意，`writable`只有在`false`改为`true`会报错，`true`改为`false`是允许的。
>
> 至于`value`，只要`writable`和`configurable`有一个为`true`，就允许改动。
>
> 另外，`writable`为`false`时，直接目标属性赋值，不报错，但不会成功。严格模式下会报错

5. `get`

`get`是一个**函数**，表示该属性的取值函数（getter），默认为`undefined`。**每次读取属性的时候都会先调用，不可以和`value`属性同时存在，也不可以和`writable`属性同时存在**

6. `set`

`set`是一个**函数**，表示该属性的存值函数（setter），默认为`undefined`。**不可以和`value`属性同时存在，也不可以和`writable`属性同时存在**

**如果原型对象的某个属性的`writable`为`false`，那么子对象（继承）将无法自定义这个属性。 除非通过方法重新设置属性描述对象**

### 17.4 存取器

> 存值函数：`setter`
>
> 取值函数：`getter`

```js
let o1 = {
  set p(e) {
    console.log(e);
  },
  get p() {
    return 123;
  },
};
o1.p = "dddd"; // dddd
console.log(o1.p); // 123
```

当然，也可以使用`defineProperty`或者`defineProperties`设置属性描述对象

存取器一般用于根据内部条件的约束，改变属性的值

### 17.5 深拷贝

```js
var deepCopy = function (to, from) {
  for (var key in from) {
    // 如果不是自身属性，跳过此次循环，否则getOwnPropertyDescriptor获取不了描述对象，报错
    if (!from.hasOwnProperty(key)) continue;

    if (Object.prototype.toString.call(from[key]) === "[object Object]") {
      to[key] = {};
      deepCopy(to[key], from[key]);
    } else if (Object.prototype.toString.call(from[key]) === "[object Array]") {
      to[key] = [];
      deepCopy(to[key], from[key]);
    } else {
      Object.defineProperty(
        to,
        key,
        Object.getOwnPropertyDescriptor(from, key)
      );
    }
  }
  return to;
};
```

## 18. Array

## 19. 面向对象

### 19.1 继承

#### 19.1.1 构造函数的缺点

> 通过构造函数生成新的实例对象，每个实例`new`出来时，属性和方法都是一样的，但是实例之间是无法共享属性的，因此有点浪费系统资源

```js
function Person() {
  this.name = "gauhar";
  this.walk = function () {
    console.log(1111);
  };
}
const p = new Person();
const p2 = new Person();
console.log(p.walk === p2.walk); // false
```

#### 19.1.2 prototype 属性的作用

> 函数默认具有`prototype`属性，指向一个对象，构造函数生成实例的时候，该属性会自动成为实例对象的原型

```js
function Person() {
  this.name = "gauhar";
  this.walk = function () {
    console.log(1111);
  };
}
Person.prototype.sayHi = function () {
  console.log("Hi");
};
const p = new Person();
const p2 = new Person();
console.log(p.walk === p2.walk); // false
console.log(p.sayHi === p2.sayHi); // true
```

**只要修改原型对象，变动就立刻会体现在所有实例对象上，这也达到共享属性的目的**

#### 19.1.3 原型链

> 每个对象都会有自己的原型对象，原型对象也有自己的原型。任意的对象可以充当另一个对象的原型对象。所以就形成了**原型链**
>
> 所有对象都继承了`Object.prototype`的属性，而`Object.prototype`的原型是`null`，因此，原型链的尽头就是`null`

```js
Object.getPrototypeOf(Object.prototype); // null
```

#### 19.1.4 constructor 属性

> 指向`prototype`对象所在的构造函数。
>
> 修改原型对象的时候，要同时修改`constructor`指向

#### 19.1.1 原型链继承

> 将父类的实例赋值给子类的原型
>
> `Child.prototype = new Parent()`

```js
function Parent() {
  this.name = "gauhar";
}
Parent.prototype.pro = function () {
  console.log("prototype_pro");
};
function Child() {}
// 将父类的实例赋值给子类的原型
Child.prototype = new Parent();
const c = new Child();
console.log(c.name); // gauhar
c.pro(); // prototype_pro
```
