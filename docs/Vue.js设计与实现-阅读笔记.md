## 4.	响应系统的作用与实现

### 4.1	响应式数据与副作用函数

#### 什么是副作用函数

函数的执行会**直接或间接影响其他函数的执行**，这时我们说函数产生了副作用。比如修改了全局变量

```js
// 全局变量
let val = 1
function effect() {
   val = 2 // 修改全局变量，产生副作用
}
```

### 4.2　响应式数据的基本实现

现在我们有个effect函数，我们希望`obj.text`改变的时候，会自动重新运行`effect`函数，达到响应式数据更新的目标

```js
// 原始数据
const data = { text: 'hello world' }
function effect() {
  document.body.innerText = data.text
}
```

**现在的两个重点操作就是**

1. 在**读取** `data.text`的值的时候，把`effect`函数收集起来放在一个桶里面
1. 在**设置** `data.text`的值的时候，**即值被重新赋值了**，执行桶里面的`effect`函数

转换成代码就是

```js
// 存储副作用函数的桶
const bucket = new Set()
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 effect 添加到存储副作用函数的桶中
    bucket.add(effect)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 把副作用函数从桶里取出并执行
    bucket.forEach(fn => fn())
  }
})
```

### 4.3　设计一个完善的响应系统

从上面不难看出，目前有很多缺陷

1. 代理中的`get`，硬编码取了全局的`effect`函数，不够灵活

   改造一下`effect`函数，新增一个参数接收副作用函数，新建一个全局变量`activeEffect`来记录当前活动的副作用函数

   ```js
   // 用一个全局变量存储当前激活的 effect 函数
   let activeEffect
   function effect(fn) {
     // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
     activeEffect = fn
     // 执行副作用函数
     fn()
   }
   ```

2. 副作用函数没有和具体的`key`所关联，只要改变了`obj`的任意一个`key`，都会触发副作用函数的执行

   > 假设原始对象为`target`，`target`对象有多个`key`，然后每一个`key`对应有副作用函数`effect`

   - 先把`bucket`的类型更改为`WeakMap`，以`target`作为`WeakMap`的`key`，而他的`value`是一个`map`结构

     > 使用`WeakMap`，因为利于垃圾回收，`WeakMap`对于`key`的引用是弱引用，当`target`不存在时候，就会进行垃圾回收

   - 上面的`Map`结构我们命名为`depsMap`，他的`key`是原始对象`target`的属性，而他的`value`就是和这个`key`所关联的副作用函数集合**(`Set`结构，目的是去重)**，命名为`deps`

   ![image-20230131170927023](https://raw.githubusercontent.com/GauharChan/Picture-bed/main/img/image-20230131170927023.png)

   3. 为了优化代码，应该封装两个函数`track` 和`trigger`。在`get`中追踪`(track)`依赖；在`set`中触发`(trigger)`依赖

   代码如下

   ```js
   // 原始数据
   const data = { text: 'hello world' }
   // 对原始数据的代理
   const obj = new Proxy(data, {
     // 拦截读取操作
     get(target, key) {
       // 将副作用函数 activeEffect 添加到存储副作用函数的桶中
       track(target, key)
       // 返回属性值
       return target[key]
     },
     // 拦截设置操作
     set(target, key, newVal) {
       // 设置属性值
       target[key] = newVal
       // 把副作用函数从桶里取出并执行
       trigger(target, key)
     }
   })
   
   function track(target, key) {
     let depsMap = bucket.get(target)
     if (!depsMap) {
       bucket.set(target, (depsMap = new Map()))
     }
     let deps = depsMap.get(key)
     if (!deps) {
       depsMap.set(key, (deps = new Set()))
     }
     deps.add(activeEffect)
   }
   
   function trigger(target, key) {
     const depsMap = bucket.get(target)
     if (!depsMap) return
     const effects = depsMap.get(key)
     effects && effects.forEach(fn => fn())
   }
   
   // 用一个全局变量存储当前激活的 effect 函数
   let activeEffect
   function effect(fn) {
     // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
     activeEffect = fn
     // 执行副作用函数
     fn()
   }
   
   effect(() => {
     console.log('effect run')
     document.body.innerText = obj.text
   })
   ```

### 4.4　分支切换与 cleanup

   所谓分支切换，是指有条件影响哪部分代码的执行，比如一个三元表达式

```js
effect(() => {
  console.log('effect run')
  document.body.innerText = obj.ok ? obj.text : 'not'
})
```

 可以看到，副作用函数 分别被字段 data.ok 和字段 data.text 所对应的依赖集合收集。

但当字段 obj.ok 的值修改为 false，并触发副作用函数重新执行后，不应该被字段 obj.text 所对应的依赖集合收集

目前我们没有做到这一点，解决方案也很简单：**每次副作用函数执行时，我们可以先把它从所有与之关联的依赖集合中删除**

#### cleanup

1. 副作用函数要明确知道哪些依赖集合关联到它

   重构一下`effect`函数，新建`effectFn`函数来做之前的事情，在`effectFn`函数上新增一个数组，来储存**当前副作用函数的**依赖集合`deps`

   > 因为函数本质上也是一个对象

   ```js
   function effect(fn) {
     const effectFn = () => {
       // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
       activeEffect = effectFn
       fn()
     }
     // activeEffect.deps 用来存储所有与该副作用函数相关的依赖集合
     effectFn.deps = []
     // 执行副作用函数
     effectFn()
   }
   ```

2. 上面只是初始化了数组，具体要在`track`的时候收集

   ```js{10,13}
   function track(target, key) {
     let depsMap = bucket.get(target)
     if (!depsMap) {
       bucket.set(target, (depsMap = new Map()))
     }
     let deps = depsMap.get(key)
     if (!deps) {
       depsMap.set(key, (deps = new Set()))
     }
     // 当前key所关联的副作用函数
     deps.add(activeEffect)
     // 收集当前副作用函数关联的依赖集合  
     activeEffect.deps.push(deps)
   }
   ```
   
   
   
   
   
   
   
   

