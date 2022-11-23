<Banner />

## 响应式基础API(reactive.ts)

> [源码文件地址](https://github.com/vuejs/core/blob/main/packages/reactivity/src/reactive.ts)
>
> 源码调试方法
>
> - `node`版本要大于16，装依赖
> - 执行`dev`命令，比如`yarn dev`
> - 页面中引用`dist/vue.global.js`即可
>
> 源码本身也有一些`examples html`，比如`packages/vue/examples/composition/commits.html`

### createReactiveObject

> **创建响应式对象的方法**，非常重要
>
> `baseHandlers`和`collectionHandlers`是调用对应的方法时就确定好的，就是`reactive`、`readonly`...
>
> `proxyMap`也是调用对应的方法时就确定好的，而`proxyMap`对应的是以下4种
>
> ```ts
> export const reactiveMap = new WeakMap<Target, any>()
> export const shallowReactiveMap = new WeakMap<Target, any>()
> export const readonlyMap = new WeakMap<Target, any>()
> export const shallowReadonlyMap = new WeakMap<Target, any>()
> ```
>
> 

```ts
function createReactiveObject(
  target: Target,
  isReadonly: boolean,
  baseHandlers: ProxyHandler<any>,
  collectionHandlers: ProxyHandler<any>,
  proxyMap: WeakMap<Target, any>
) {
  // 如果target的类型不属于Object, Array, Map, Set, WeakMap, WeakSet其中的一个，则直接返回
  if (!isObject(target)) {
    if (__DEV__) {
      console.warn(`value cannot be made reactive: ${String(target)}`)
    }
    return target
  }
  // 如果target已经是一个由本方法创建的proxy了，直接返回
  // 例外: 调用readonly创建reactive对象的只读副本 e.g. readonly(reactive({}))
  if (
    target[ReactiveFlags.RAW] &&
    !(isReadonly && target[ReactiveFlags.IS_REACTIVE])
  ) {
    return target
  }
  // 在对应的缓存列表(WeakMap)中寻找对应的Proxy
  const existingProxy = proxyMap.get(target)
  if (existingProxy) {
    return existingProxy
  }
  // only a whitelist of value types can be observed.
  const targetType = getTargetType(target)
  // 判断target的类型是否符合要求，TargetType.INVALID代表target不能扩展或者被标记了不能转换为响应式对象(markRaw)
  if (targetType === TargetType.INVALID) {
    return target
  }
  const proxy = new Proxy(
    target,
    // Map, Set, WeakMap, WeakSet使用collectionHandlers
    // Object, Array使用baseHandlers
    targetType === TargetType.COLLECTION ? collectionHandlers : baseHandlers
  )
  // 缓存起来
  proxyMap.set(target, proxy)
  return proxy
}
```

**有了`createReactiveObject`，下面4中创建响应式对象的方法，只要传对应的参数即可**

### reactive

> 创建响应式对象并返回副本，响应式转换是“深层”的
>
> 如果任何 `property` 使用了 `ref`，当它通过代理访问时，则被自动解包
>
> 如果传入的`target`是一个`readonly`代理，则直接返回`target`

```ts
export function reactive<T extends object>(target: T): UnwrapNestedRefs<T>
export function reactive(target: object) {
  // if trying to observe a readonly proxy, return the readonly version.
  // 如果尝试观察只读代理，返回只读版本。
  if (isReadonly(target)) {
    return target
  }
  return createReactiveObject(
    target,
    false, // 不是只读
    mutableHandlers, // 对应的baseHandlers
    mutableCollectionHandlers, // 对应的collectionHandlers
    reactiveMap // 对应的proxyMap 
  )
}
```

### readonly

> 接受一个对象 (响应式或纯对象) 或 [ref](https://v3.cn.vuejs.org/api/refs-api.html#ref) 并返回原始对象的只读代理。只读代理是深层的：任何被访问的嵌套 `property` 也是只读的。
>
> 如果任何 property 使用了 `ref`，当它通过代理访问时，则被自动解包

```ts
export function readonly<T extends object>(
  target: T
): DeepReadonly<UnwrapNestedRefs<T>> {
  return createReactiveObject(
    target,
    true,
    readonlyHandlers,
    readonlyCollectionHandlers,
    readonlyMap
  )
}
```

### shallowReactive

> 返回浅层的响应式对象副本，只有根级别属性是响应的，不会自动展开`ref`(即使是根属性的`ref`)

```ts
export function shallowReactive<T extends object>(
  target: T
): ShallowReactive<T> {
  return createReactiveObject(
    target,
    false,
    shallowReactiveHandlers,
    shallowCollectionHandlers,
    shallowReactiveMap
  )
}
```

### shallowReadonly

> 返回浅层的响应式对象**只读副本**，只有根级别属性是响应的，不会自动展开`ref`(即使是根属性的`ref`)

```ts
export function shallowReadonly<T extends object>(target: T): Readonly<T> {
  return createReactiveObject(
    target,
    true,
    shallowReadonlyHandlers,
    shallowReadonlyCollectionHandlers,
    shallowReadonlyMap
  )
}
```

### 小结

- 都是由`createReactiveObject`创建的代理

- `reactive`、`readonly` 会进行**深层**的转换，在`get`的时候将解包所有深层的 [refs](https://v3.cn.vuejs.org/api/refs-api.html#ref)，同时维持 `ref` 的响应性。
- `shallowReactive`、`shallowReadonly` 会进行**浅层**的转换(即根对象)，任何使用 [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref) 的 property 都**不会**被代理自动解包

### ReactiveFlags

> 从名字就可以看出，这些属性用于判断`响应式`对象的一些标识

```ts
export const enum ReactiveFlags {
  // 跳过标识
  SKIP = '__v_skip',
  // 是否为(reactive / shallowReactive)创建的响应式对象
  IS_REACTIVE = '__v_isReactive',
  // 是否为只读响应式代理(readonly / shallowReadonly)
  IS_READONLY = '__v_isReadonly',
  // 是否为浅层代理
  IS_SHALLOW = '__v_isShallow',
  // 原始对象
  RAW = '__v_raw'
}

export interface Target {
  [ReactiveFlags.SKIP]?: boolean
  [ReactiveFlags.IS_REACTIVE]?: boolean
  [ReactiveFlags.IS_READONLY]?: boolean
  [ReactiveFlags.IS_SHALLOW]?: boolean
  [ReactiveFlags.RAW]?: any
}
```

#### IS_REACTIVE

::: tip

其中`ReactiveFlags.IS_REACTIVE`比较特殊，他的值取决于`target`的`isReadonly`。`isReadonly`是在创建`proxy`的时候就确定好的(具体的实现是在`proxy`的`handler`中)

> 拿`baseHandlers.ts`举例

```ts
const get = /*#__PURE__*/ createGetter()
const shallowGet = /*#__PURE__*/ createGetter(false, true)
const readonlyGet = /*#__PURE__*/ createGetter(true)
const shallowReadonlyGet = /*#__PURE__*/ createGetter(true, true)
```

:::

`reactive`、`readonly`、`shallowReactive`、`shallowReadonly`都是创建响应式对象(代理)，因此`IS_REACTIVE`只需判断`只读`的标识即可

```ts
function createGetter(isReadonly = false, shallow = false) {
  return function get(target: Target, key: string | symbol, receiver: object) {
    if (key === ReactiveFlags.IS_REACTIVE) {
      return !isReadonly
    } else if (key === ReactiveFlags.IS_READONLY) {
      return isReadonly
    } else if (key === ReactiveFlags.IS_SHALLOW) {
      return shallow
    } else if (
      key === ReactiveFlags.RAW &&
      receiver ===
      (isReadonly
       ? shallow
       ? shallowReadonlyMap
       : readonlyMap
       : shallow
       ? shallowReactiveMap
       : reactiveMap
      ).get(target)
    ) {
      return target
    }
    // ...
  }
}
```

### isReactive

> 检查对象是否是由 [`reactive`](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 创建的响应式代理。
>
> 从源码可以看出，如果是`readonly`创建的`value`，会进行递归判断，也就是说
>
> 如果该代理是 [`readonly`](https://v3.cn.vuejs.org/api/basic-reactivity.html#readonly) 创建的，但包裹了由 [`reactive`](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 创建的另一个代理，它也会返回 `true`。


```ts
export function isReactive(value: unknown): boolean {
  if (isReadonly(value)) {
    return isReactive((value as Target)[ReactiveFlags.RAW])
  }
  return !!(value && (value as Target)[ReactiveFlags.IS_REACTIVE])
}
```

### isReadonly

> 检查对象是否是由 [`readonly`](https://v3.cn.vuejs.org/api/basic-reactivity.html#readonly) 创建的只读代理。
>
> 他的工作就是检查`ReactiveFlags.IS_READONLY`标识

```ts
export function isReadonly(value: unknown): boolean {
  return !!(value && (value as Target)[ReactiveFlags.IS_READONLY])
}
```

### isShallow

>  `v3.2.28 (2022-01-21)+`，但本文记录时，官网文档没有说明该方法，**算是彩蛋了**
>
> 检查对象是否为浅层的代理
>
> 他的工作就是检查`ReactiveFlags.IS_SHALLOW`标识

```ts
export function isShallow(value: unknown): boolean {
  return !!(value && (value as Target)[ReactiveFlags.IS_SHALLOW])
}
```

### isProxy

> 检查对象是否是由 [`reactive`](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 或 [`readonly`](https://v3.cn.vuejs.org/api/basic-reactivity.html#readonly) 创建的 proxy。
>
> 所以他的判断交给了`isReactive`和`isReadonly`

```ts
export function isProxy(value: unknown): boolean {
  return isReactive(value) || isReadonly(value)
}
```

### toRaw

> 返回 [`reactive`](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 或 [`readonly`](https://v3.cn.vuejs.org/api/basic-reactivity.html#readonly) 代理的原始对象。

- 先拿到observed的(ReactiveFlags.RAW)值

- 如果raw没有值(undefined)，则证明observed是普通对象，直接返回observed

- 如果raw有值，那么会存在两种情况
  - observed的代理只有一层
  - observed 是一个嵌套多层的响应式对象，比如：readonly(reactive({}))、readonly(readonly({}))

- 所以需要递归判断

- 重点结束条件：如果raw的值是undefined就是拿到原始对象了

```ts
export function toRaw<T>(observed: T): T {
  const raw = observed && (observed as Target)[ReactiveFlags.RAW]
  return raw ? toRaw(raw) : observed
}
```

### markRaw

> 标记一个对象不会被代理，加入黑名单，不会被`observed`。目前来看似乎没有提供方法取消标记
>
> 用`Object.defineProperty` 设置`ReactiveFlags.SKIP`属性为`true`
>
> 在用`reactive`创建响应式对象时，会调用`getTargetType(target)`进行判断，如果`target`的`ReactiveFlags.SKIP`属性是`true`，或者是不可扩展的，则认定这个`target`是无效的；**因此并不会创建响应式对象**

```ts
export function markRaw<T extends object>(value: T): T {
  def(value, ReactiveFlags.SKIP, true)
  return value
}

function getTargetType(value: Target) {
  return value[ReactiveFlags.SKIP] || !Object.isExtensible(value)
    ? TargetType.INVALID
    : targetTypeMap(toRawType(value))
}

function createReactiveObject(){
  // ...
  // only a whitelist of value types can be observed.
  const targetType = getTargetType(target)
  if (targetType === TargetType.INVALID) {
    return target  // 返回原始对象
  }
  // ...
}
```

**例子**

```ts
const obj: any = {
  name: 'gauhar',
};
// obj被标记了
markRaw(obj);
// state不是响应的
const state = reactive(obj); // state === obj

function handleClick() {
  // 不会响应
  state.name = '1234';
  state.sex = 'man';
}
```

上面的`state === obj`，因为创建`reactive`的时候被拦截`return target`了。属性有被修改和新增，但已经不是响应式的了。

<font color="red">但值得注意是</font>`markRaw`只劫持了一层(从源码可以看出)，因此对面里的属性没有被标注。比如

```ts
const obj: any = {
  name: 'gauhar',
  info: {
    hair: 'black',
  },
};

// obj被标注了，但info没有
markRaw(obj);
const state = reactive(obj);
// 这种情况下如果使用obj.info去创建reactive
const data = reactive(obj.info);

function handleClick() {
  // 不会响应(按照我们上面的说法，这里应该是不会响应的)
  state.name = '1234';
  state.sex = 'man';
  // 但是由于`同一性风险`，会得到原始对象(obj)被代理后的版本；这个函数里的改变会被响应
  data.hair = 'red' // observed
}
```

关于[同一性风险](https://v3.cn.vuejs.org/api/basic-reactivity.html#markraw)具体可以参考官网👊

### 两个内部方法

> 都是进行对应的转换

```ts
export const toReactive = <T extends unknown>(value: T): T =>
  isObject(value) ? reactive(value) : value

export const toReadonly = <T extends unknown>(value: T): T =>
  isObject(value) ? readonly(value as Record<any, any>) : value
```





























