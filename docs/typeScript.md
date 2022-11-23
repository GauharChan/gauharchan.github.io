<Banner />
## 命令

### 生成 tsconfig.json 文件

```shell
tsc --init
```

## 联合类型

> 使用`|`分隔类型

```ts
let nameOrAge: string | number = "gauhar";
nameOrAge = 18;
```

## 合并类型

> 使用`&`符号，将多个类型合并

```ts
const name: { firstName: string } & { lastName: string } = {
  firstName: "gauhar",
  lastName: "chan",
};
```

## interface 接口

> 名称使用大写开头，或者以`I`开头
>
> 使用`?`说明属性是可选的。
>
> `[propName: string]: any`指定该接口有<font color='red'>**任意属性**</font>，这个`any`说明任意属性可以是任意类型。如果换成`string`，则该接口下，必选、可选的属性都必须是`string`类型。
>
> `readonly`定义属性只读。即初始化赋值，后面不可改。

```ts
interface Person {
  name: string;
  age?: number;
  [propName: string]: any;
  readonly noChange: string;
}

let tom: Person = {
  name: "Tom",
};
```

## 数组

> `number[]`: 数字类型数组
>
> `string[]`: 字符串数组
>
> `any[]`: 任意类型数组

## 函数

> 可选参数`c?: number`，非可选参数缺一不可，类型也要一致

<font color='red'>需要注意的是，可选参数必须接在必需参数后面。换句话说，**可选参数后面不允许再出现必需参数了**</font>

**但，如果参数使用默认值，参数就是可选参数，那么限制就不存在了**

```ts
let add = (a: number = 123, b: number, c?: number): number => a + b;
```

上面的写法，add 的类型并没有指定，而是推导出来的。下面是指定的写法

> 左边定义 add 的`=>`并不是`es6`中的箭头函数，而是说明是函数，返回`number`类型

```ts
let add: (a: number, b: number, c?: number) => number = (
  a: number,
  b: number,
  c?: number
): number => a + b;

// 上面的写法不美观，可以使用接口代替
interface Iadd {
  (a: number, b: number, c?: number): number;
}

let add: Iadd = (a: number, b: number, c?: number): number => a + b;
```

### 剩余参数

> `...items`就是除了`array`的所有参数，而他是一个数组，所以使用数组类型声明。他必须放在参数最后一位

```ts
function push(array: any[], ...items: any[]) {
  items.forEach(function (item) {
    array.push(item);
  });
}

let a = [];
push(a, 1, 2, 3);
```

### 重载

> 重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。
>
> 多次定义不同类型的函数，这样就可以保证当参数`a`是`number`类型的时候返回`number`类型

**TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。**

```ts
function f1(a: number): number;
function f1(a: string): string;
function f1(a: number | string): number | string {
  return typeof a === "number" ? 1 : "1";
}
```

## 类型断言

> 我们自己指定一个值的类型。
>
> 需要注意的是，类型断言只能够「欺骗」TypeScript 编译器，无法避免运行时的错误，反而滥用类型断言可能会导致运行时错误

```ts
let duan: string | number;
(duan as string) = 123; // 报错，类型断言为string类型
```

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型中共有的属性或方法**

```ts
interface Icat {
  name: string;
  run(): void;
}
interface Ifish {
  name: string;
  swim(): void;
}

function isCat(animal: Icat | Ifish) {
  console.log(animal.name); // 共同拥有name属性
  console.log(animal.run); // 报错 run只存在于Icat接口，并不是共同拥有的
}
```

使用断言，指定为特定的接口，就可以使用

```ts
function ifFish(animal: Icat | Ifish) {
  (animal as Icat).run();
}
```

## 声明文件

> .d.ts 文件

### 全局声明

全局变量的声明文件主要有以下几种语法：

- `declare var` 声明全局变量
- `declare function` 声明全局方法
- `declare class`声明全局类
- `declare enum`声明全局枚举类型
- `declare namespace` 声明（含有子属性的）全局对象
- `interface` 和 `type`声明全局类型

## 内置对象

> eq：`IArguments`描述`arguments`这个类数组
>
> 常用的类数组都有自己的接口定义，如 `IArguments`, `NodeList`, `HTMLCollection` 等

```ts
let b: Boolean = new Boolean(1);
let e: Error = new Error("Error occurred");
let d: Date = new Date();
let r: RegExp = /[a-z]/;
```

## 类型别名

> 给类型起一个别的名称，下面`$1`代表`number`类型。使用`type` 定义。建议名称首字母大写

```ts
type $1 = number;
type St = string;
const a: $1 = 123;
```

## 字符串字面量类型

> 约束只能取指定的值

```ts
type name = "gauhar" | "chan";

let name2: name = "chan";
```

## 元组

> 初始化赋值时(或者是通过下标赋值)，必须按照顺序的类型

```ts
let tuple: [string, number] = ["string", 123];
```

### 越界的元素

> 使用`push`等方法时，元素的类型会被限制为元组中每个类型的联合类型

```ts
let tuple: [string, number];
tuple.push(123);
tuple.push(true); // 报错。 类型“true”的参数不能赋给类型“string | number”的参数。
```

## 枚举

> 定义枚举的时候就要初始化赋值，如果没有指定值，则默认以 0 开始赋值，`0,1,2...`。不能通过属性名修改值 。

```ts
enum Week {
  SUN,
  MON,
  TUE = ‘星期二’
}

Week.SUN = 'dsf' // 报错，只读属性
console.log(Week.SUN === 0); // true
```

未手动赋值的枚举项会接着上一个枚举项递增，下面的`TUE`就是 2、接着 3、4 以此类推

```ts
enum Week {
  SUN = 5,
  MON = 1,
  TUE, // 2
}
```

## 类

### 静态方法

使用 `static` 修饰符修饰的方法称为静态方法，它们不需要实例化，而是直接通过类来调用：

```js
class Animal {
  static isAnimal(a) {
    return a instanceof Animal;
  }
}

let a = new Animal("Jack");
Animal.isAnimal(a); // true
a.isAnimal(a); // TypeError: a.isAnimal is not a function
```

### ES7

#### 实例属性

> 不需要通过`this.xxx`来定义

```js
class Animal {
  name = "Jack";

  constructor() {
    // ...
  }
}

let a = new Animal();
console.log(a.name); // Jack
```

### 修饰符

- `public` 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 `public` 的
- `private` 修饰的属性或方法是私有的，不能在声明它的类的外部访问
- `protected` 修饰的属性或方法是受保护的，它和 `private` 类似，区别是它在子类中也是允许被访问的

### readonly 属性

> 只读，不可修改

## 装饰器

#### 方法装饰器

```ts
let test = (value?: any) => (target: any, propertyKey: string, descriptor: PropertyDescriptor) => {
    const method = descriptor.value
    descriptor.value = (...args: any[]) => {
        console.log(value);
        if (value) method.apply(this, args)
    }
    return descriptor
}
@test('hello test')
public mounted (): void {
  console.log('mounted');
}
```
