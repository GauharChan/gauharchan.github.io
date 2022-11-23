<Banner />
## 常用语法

`v-once` : 只能一次性的插值， 也就是说获取数据后，更新数据的时候不会响应

`v-html` : 将值以 html 的语法解析，**（一般很少用到） 如果让用户插值，会很危险**

`v-bind` : 绑定 html 属性的值，如：**`v-bind:title="msg"`** `msg 是data中的变量`

`v-bind 的缩写是冒号` **:** 如：`v-bind:title="msg"` === **`:title="msg"` **

`v-on` : 绑定监听事件，如：`v-on:click="函数名"` 里面的值可以是通过函数名去调用函数，或者是简单的运算语法

`v-on` : 缩写 `@` 如：**`@click="函数名"`** `.native`**修饰符监听组件根元素原生事件** 注意是组件的根元素

`v-cloak` : 很多情况下，加载页面时，会出现{{}}表达式闪烁的问题，因为那些变量的值是加载完后才覆盖的。

```html
v-cloak指令保持在元素上直到关联实例结束编译后自动移除，v-cloak和 CSS 规则如
[v-cloak] { display: none } 一起用 时，这个指令可以隐藏未编译的 Mustache
标签直到实例准备完毕。 通常用来防止{{}}表达式闪烁问题 例如：
<style>
  [v-cloak] {
    display: none;
  }
</style>
```

`v-pre`: 不会解析标签中的插值

```html
使用v-pre指令，这个p标签会直接渲染为{{msg}}，就算在data中定义了这个变量也不会解析
<p v-pre>{{msg}}</p>
```

`v-for`: 循环遍历

```html
使用v-for遍历数组，有两个参数：item是数组中的值,index是索引
:key作为遍历到数据的唯一标识
<li v-for="(item,index) of items" :key="index">{{ item }}</li>

使用v-for遍历对象，有两个参数：value是对象中的值,key是对象的键名
:key作为遍历到数据的唯一标识
<li v-for="(value,key) in object" :key="value.id">{{ value }}</li>
```

> 如果在`data`中写函数，则在函数中的`this`指向`window`

## 生命周期钩子

> ！！钩子函数就是符合条件的时候自动调用

```
beforeCreate:初始化一个空的Vue实例对象，只有默认的事件和生命周期
created:vue'实例创建完成'时自动触发，这个时候模板还没有完成解析，所以不能实现数据和模板的关联，但是我们可以在这个钩子中去获取数据了 ===》 created的时候可以拿到data中的数据，这个时候的data中的变量还没有渲染到模板中，

beforeMount:编译好模板，但没有挂载到页面
mounted:当vm实例初始化完成，数据和模板的完成关联

beforeUpdate:Vue的data属性中的数据更新了，但模板的数据还没有更新
updated:模板的数据已经更新完成

beforedestroy: vue实例的销毁函数被调用
destroyed:vue实例已被销毁 或者是 组件被销毁
```

![](https://s1.ax1x.com/2020/05/09/YQTka4.png)

![](https://s1.ax1x.com/2020/05/09/YQTAIJ.png)

## class 和 style 的绑定

类名和内联他们的用法是一样的

通过 v-bind 属性绑定：

```html
<div :class="bgco" :style="nSmall">somethings</div>
```

这个时候应该用计算属性返回`bgco` 的值，这是一个很强大的模式

```js
data:{
  isBgRed : true;
}

computed:{
  bgco(){
    //这里可以返回一个对象，对象的属性就是类名，多个属性相当于多个类名
    return {
      //类名 : 一个布尔类型变量
      "bg-red" : this.isBgRed;
    }
  },
    nSmall(){
      return {
        fontSize : '12px'
      }
    }
}
```

**绑定多个类名和内联样式可以使用数组语法**

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

组件也可以绑定 class，这些 class 将被添加到该组件的根元素上面

```vue
<my-component v-bind:class="{ active: isActive }"></my-component>
```

**style 的绑定**

**对象语法**

直接绑定到一个样式对象通常更好，这会让模板更清晰：

```vue
<div v-bind:style="styleObject"></div>
data: { styleObject: { color: 'red', fontSize: '13px' } }
```

同样的，对象语法常常结合返回对象的计算属性使用。

**数组语法**

`v-bind:style` 的数组语法可以将多个样式对象应用到同一个元素上：

```vue
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

## v-model

### 修饰符

#### .lazy

> 默认情况下，输入框的`v-model`默认的触发事件是`input`。所以每次输入的时候就会更新数据(初了输入中文)。使用`lazy`，`input`被转换为`change`事件

```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" />
```

#### .number

> 输入框类型即使为`number`，`value`的类型还是字符串。
>
> 作用：将输入值通过`parseFloat`转换，如果无法解析则返回原始输入值。

#### .trim

> 如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符

## 组件的 v-model

> 组件可以使用`v-model`，默认`prop`是`value`，事件名称是`input`

```vue
<template>
  <component-a v-model="name" />
</template>
<script>
export default {
  data() {
    return {
      name: "",
    };
  },
};
</script>
```

`<component-a v-model="name" />`等价于

`<component-a :value="name" @input="name = $event" ></component-a>`

**组件代码**

> 必须定义`prop`接收`value`
>
> 输入框绑定`value (attribute)`的值是`prop`的`value(property)`
>
> 输入框的`input`事件，触发自定义事件(`v-model`默认的名字就是`input`)，并传入新输入的值

```vue
<template>
  <input :value="value" @input="$emit('input', $event.target.value)" />
</template>
<script>
export default {
  props: ["value"],
};
</script>
```

更改 v-model 默认的 prop 和 emit 的名称，假设和你组件的 prop 冲突，或者说 checkbox 而不是 input 输入框的时候，prop 改为 checked 更适合，事件改为 change。

> 通过定义`model`选项来改变，**注意`model`中修改后的`prop`名称，在`prop`中要定义**

```vue
<template>
  <!-- 默认的v-model -->
  <!-- <input :value="value" @input="$emit('input', $event.target.value)" /> -->
  <input
    type="checkbox"
    v-bind:checked="checked"
    v-on:change="$emit('change', $event.target.checked)"
  />
</template>
<script>
export default {
  model: {
    prop: "checked",
    event: "change",
  },
  props: {
    checked: Boolean,
  },
};
</script>
```

在父组件那边还是正常的`v-model`写法`<component-a v-model="name" />`

## v-for

> 和`v-if`一样， 可以用`<template>`来循环渲染一段包含**多个元素的内容**
>
> 可以用 `of` 替代 `in` 作为分隔符，因为它更接近 JavaScript 迭代器的语法：
>
> 最好给循环的每一项标识唯一的 key 值
>
> <font color='red'>如果数组的数据会更新，记得记得`key`用使用`id`而不是`index`，不然会出现视图不更新的情况</font>

1. 循环对象时，第一个参数是键值，第二个参数是键名, 第三个参数是索引
2. 循环数组时，第一个参数是值(`value`)，第二个参数是索引(`index`)

```vue
<div v-for="(item, index) of arr">
  <p :key="index">{{item}}</p>
</div>
```

### 数组更新检测

> 点击事件没有这些限制

在生命周期中，`Vue` **不能**检测以下两种情况下数组的变动：

1. 当你利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`
2. 当你修改数组的长度时，例如：`vm.items.length = newLength`

```js
export default {
  name: "Home",
  data() {
    return {
      event: "",
      msg: "点击了0次",
      count: 0,
      arr: ["gauhar", "xiao", "giao"],
    };
  },
  mounted() {
    this.arr[1] = "123"; // 没有效果  本意是想将xiao改成123  但是效果不似预期
  },
};
```

可以使用`Vue.set(vm.items, indexOfItem, newValue)` 实例方法`this.$set`=== 全局`Vue.set`

```js
mounted(){
  this.$set(this.arr, 1, 123)
},
```

或者是使用变异方法`splice()`，修改数组长度也可以用`splice(newLength)`

```js
mounted(){
  this.arr.splice(1, 1, 123)
},
```

**触发视图更新的变异方法包括**：

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

## 计算属性 computed

> 不可以执行异步

### **计算属性是基于它们的响应式依赖进行缓存的**

```
如果返回一个变量，函数方法也可以做到，如果反复使用该变量，每次都要执行这个方法，需要一定的时间；但如果使用计算属性的话，只要这个变量的依赖项没有改变，都会立刻返回这个变量的值,这是他的缓存机制
```

如果想得到的变量是通过一定的处理才得到的，最好使用**`computed`** 函数返回。

当你有一些数据需要随着其它数据变动而变动时，最好避免滥用`watch`，而是用`computed` 返回想要的变量

```html
<p>Computed reversed message: "{{ reversedMessage }}"</p>
```

```javascript
computed: {
    // 计算属性的 getter
    reversedMessage: function () {
        // `this` 指向 vm 实例
        return this.message.split('').reverse().join('')
        //这个message是data中的变量
    }
}
```

## 侦听器 watch

> `watch`可以执行异步

```
监听一个变量的变动，能够返回旧值和新值
```

```javascript
watch: {
  // 如果 `question` 发生改变，这个函数就会运行   首次执行时，question没有改变不会执行
  question: function (newQuestion, oldQuestion) {
    this.answer = 'Waiting for you to stop typing...'
    this.debouncedGetAnswer()
  }
}
```

### immediate

> 即时执行。上面的写法就是`immediate`默认为`false`。上面就是写了一个`handler`

```js
watch: {
  // 第一次不用等待 `question` 发生改变，这个函数就会运行
  question: {
    hanlder(newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    },
    immediate: true
  }
}
```

### deep

> 深度监听，用于监听对象下的属性改变。但这样性能消耗比较大，建议直接监听对象中的属性`'obj.name'`

```js
watch: {
  // 第一次不用等待 `question` 发生改变，这个函数就会运行
  question: {
    hanlder(newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    },
    immediate: true,
    deep: true // new code
  }
}

// 直接监听对象中的属性
watch: {
  // 第一次不用等待 `question` 发生改变，这个函数就会运行
  'obj.name': {
    hanlder(newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    },
    immediate: true
  }
}
```

## 事件处理

在内联绑定事件的时候，可以传入`$event`，访问原始的 DOM 事件

```vue
<button @[bian]="handle('jj', $event)">双数单击，单数双击</button>
<script>
handle(str, event) {
  console.log(event.type);
  this.count++;
  this.msg = `点击了${this.count}次`;
  this.arr[1] = 456
}
</script>
```

### 修饰符

> 使用串联修饰符时，要注意顺序

`.capture`: 捕获

```vue
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>
```

`.self`：触发事件的元素必须是本身

```vue
<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>
```

### 按键修饰符

> 直接使用按键名称更好,键名也可以通过`$event.key`获取
>
> 使用`kebab-case` 的方式写键名：`page-down`

```vue
<input type="text" v-model="inputVal" @keyup.page-down="inputVal = 'pageDown'" @keyup.delete="inputVal = 'delete'">
```

- `.ctrl`
- `.alt`
- `.shift`

```vue
<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

`.exact`：必须是精确的指定修饰符才能执行

```vue
表示必须是单纯的鼠标点击，不能同时按下诸如ctlr的键
<div @click.exact="msg = 123">click</div>
```

### 鼠标修饰符

- `.left`
- `.right`
- `.middle`

## 组件自定义事件

### .sync 修饰符

> 语法糖，通过`.sync`修饰符前面的值决定`prop`的名称和`emit`事件的名称

假设有一个 prop，并且需要更新他的值(类似 v-model)

```vue
<template>
  <component-a :title="title" @update:title="title"></component-a>
</template>
```

`component-a` 那边通过`emit`触发`update:title`事件，传入参数。从而改变父组件的`title`的值

所以上面的代码可以使用.sync 代替

```vue
<template>
  <component-a :title.sync="title"></component-a>
</template>
```

假设 component-a 中执行了`this.$emit('update:title', 123)`时，父组件的`title`的值就是`123`并且更新传给子组件的`prop`

## 动态组件

> 通过`currentTabComponent`的值来决定显示哪个组件
>
> `currentTabComponent`的值可以是组件名字(`name`)，也可以是整个组件(整个选项对象)(引入的组件`.vue`文件)
>
> `<keep-alive>` 将展示的组件缓存起来，而不是每次都销毁

```vue
<keep-alive>
  <component :is="componentId"></component>
</keep-alive>
```

## 全局注册公共基础组件

> 有时候一些类似`input`这样的基础组件会被频繁调用，或者有多个这样的组件，那么在`main.js`自动全局注册，让我们后续开发更加方便
>
> 用到了`lodash`的转换命名格式，首字母大写和驼峰式，所以需要下载，`yarn add lodash`
>
> 公共的基础组件以`Base`开头命名

```js
import upperFirst from "lodash/upperFirst";
import camelCase from "lodash/camelCase";

const requireComponent = require.context(
  // 其组件目录的相对路径
  "./components",
  // 是否查询其子目录
  false,
  // 匹配基础组件文件名的正则表达式
  /Base[A-Z]\w+\.(vue|js)$/
);

requireComponent.keys().forEach((fileName) => {
  // 获取组件配置
  const componentConfig = requireComponent(fileName);

  // 获取组件的 PascalCase 命名
  const componentName = upperFirst(
    camelCase(
      // 获取和目录深度无关的文件名
      fileName
        .split("/")
        .pop()
        .replace(/\.\w+$/, "")
    )
  );

  // 全局注册组件
  Vue.component(
    componentName,
    // 如果这个组件选项是通过 `export default` 导出的，
    // 那么就会优先使用 `.default`，
    // 否则回退到使用模块的根。
    componentConfig.default || componentConfig
  );
});
```

## 动态参数

> 可以动态绑定属性，事件
>
> `attributeName`在 data 中定义，如：`attributeName: href`
>
> `eventName`: `click`
>
> 参数不能有大写，大写会被转换为小写

```vue
<a v-bind:[attributeName]="url"> ... </a>
<div @[eventName]="handle">button</div>
```

如果参数较为复杂，如：`‘foo’ + id`，不能使用空格或引号，应该使用计算属性替换

## vue 自定义指令

```js
// vue 自定义指令
Vue.directive("focus", {
  inserted(el, binding) {
    // el是使用这个指令的dom元素
    el.focus();
  },
});
```

## [过渡的类名](https://cn.vuejs.org/v2/guide/transitions.html#过渡的类名)

> `enter` 开始的状态
>
> `enter-actve` 代表整个进入过渡过程，`leave-active` 代表整个离开过渡过程
>
> `enter-to` 结束的状态

在进入/离开的过渡中，会有 6 个 class 切换。

1. `v-enter`：定义`进入过渡的开始状态`。在元素被插入之前生效，在元素被插入之后的下一帧移除。
2. `v-enter-active`：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。
3. `v-enter-to`: **2.1.8 版及以上** 定义`进入过渡的结束状态`。在元素被插入之后下一帧生效 (与此同时 `v-enter` 被移除)，在过渡/动画完成之后移除。
4. `v-leave`: 定义`离开过渡的开始状态`。在离开过渡被触发时立刻生效，下一帧被移除。
5. `v-leave-active`：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。
6. `v-leave-to`: **2.1.8 版及以上** 定义`离开过渡的结束状态`。在离开过渡被触发之后下一帧生效 (与此同时 `v-leave` 被删除)，在过渡/动画完成之后移除。

### 从无到有，有到无

![](https://s1.ax1x.com/2020/05/09/YQTFZF.png)

路由映射组件

`token`不是浏览器默认行为，需要手动传值

`$emit` 定义自定义事件

`$on` 监听自定义事件

父子组件的关系是使用关系，被使用的是子组件

删除本地存储：`removeItem`

### element-ui

```html
<!-- 这里的prop的值是绑定验证规则的，prop值必须和v-model绑定的属性值相同，否则验证规则会拿不到值 -->
<el-form-item label="商品名称" prop="name">
  <el-input v-model="goodsForm.name"></el-input>
</el-form-item>

错误例子:
<el-form-item label="商品名称" prop="username">
  // 这里和v-model的name不相同，验证规则拿不到值
  <el-input v-model="goodsForm.name"></el-input>
</el-form-item>

rules:{ name:[ { required: true, message: '请输入商品名称', trigger: 'blur' } ]
}
```

级联选择器

```js
<el-cascader
             clearable
             v-model="selectList"
             :options="cateList"
             :props="prop"   绑定下面的对象
             @change="handleChange"
             ></el-cascader>

prop: {
        checkStrictly: true,
        label: 'cat_name',  属性值可以直接绑定到数据源cateList的属性名 // 这是展示给用户看的
        value: 'cat_id',//这是真实的id值
        children: 'children'
      }
```

## 插槽

在子组件的`template`中需要的地方添加`<slot></slot>`插槽标签。

在父组件使用子组件的时候，标签中间的内容就是插槽内容

```vue
<son>插槽内容</son>
```

### 默认插槽

> 即不用写`name`，默认是`default`

父组件

```vue
<HelloWorld msg="Welcome to Your Vue.js App">
    <div>这是默认插槽</div>
</HelloWorld>
```

子组件`HelloWorld.vue`

```vue
<template>
  <div class="hello">
    <slot />
    <!--其他组件-->
    <inject />
  </div>
</template>
```

### 具名插槽

> 带有`name`标识的插槽
>
> 使用`v-slot:name`

父组件中使用`<template v-slot:girl></template>`

```vue
<HelloWorld msg="Welcome to Your Vue.js App">
    <template v-slot:girl>
        <div>这是具名插槽</div>
        <div>这是具名插槽2</div>
    </template>
</HelloWorld>
```

子组件`HelloWorld.vue`

```vue
<template>
  <div class="hello">
    <slot name="girl" />
    <slot />
    <inject />
  </div>
</template>
```

### 作用域插槽

> 作用:子组件中的数据，父组件可以使用

作用域插槽也分**默认插槽**和**具名插槽**

完整写法：`v-slot:girl="slotUser"`。其中`girl`就是插槽的`name`；`slotUser`是随意的一个字符串

`slotUser.girl.sex`：这里的`girl`指的是子组件中定义的数据。

省略写法：`v-slot="slotUser"`等价于`v-slot:default="slotUser"`

`2.6.0`新增了更简洁的写法，用`#`代替`v-slot:`。例：`#girl={sex, age}`

父组件

```vue
<template v-slot:girl="slotUser">
  <div>{{ slotUser.girl.sex }}</div>
  <div>{{ slotUser.girl.age }}</div>
</template>
<template v-slot="slotUser">
  <div>{{ slotUser.user.age }}</div>
  <div>{{ slotUser.user.name }}</div>
</template>
```

子组件

```vue
<template>
  <div class="hello">
    <!-- 作用域具名插槽 -->
    <slot name="girl" :girl="girl" />
    <slot />
    <!-- 作用域匿名插槽 -->
    <slot :user="user"></slot>

    <inject />
  </div>
</template>
<script>
export default {
  data() {
    return {
      user: {
        name: "gauhar",
        age: 18,
      },
      girl: {
        sex: "👧",
        age: "🍎",
      },
    };
  },
};
</script>
```

作用域插槽（匿名或具名）不能和前面的匿名插槽或者具名插槽并存，代码中后者的权重高。

```vue
<template v-slot:girl="slotUser">
  <div>{{ slotUser.girlr.sex }}</div>
  <div>{{ slotUser.girlr.age }}</div>
</template>

<template v-slot:girl>
  <div>这是具名插槽</div>
  <div>这是具名插槽2</div>
</template>

// 页面内容： 这是具名插槽 这是具名插槽2
--------------------------------------------------------------------

<template v-slot:girl>
  <div>这是具名插槽</div>
  <div>这是具名插槽2</div>
</template>

<template v-slot:girl="slotUser">
  <div>{{ slotUser.girlr.sex }}</div>
  <div>{{ slotUser.girlr.age }}</div>
</template>

// 页面内容： 👧 🍎
```

上面这两个都是具名插槽`girl`，页面不会展示两个模板，只会显示后面的模板。

## 插件

通过全局方法 `Vue.use()` 使用插件。它需要在你调用 `new Vue()` 启动应用之前完成：

```js
// 调用 `MyPlugin.install(Vue)`
Vue.use(MyPlugin, { ...options });

new Vue({
  // ...组件选项
});
```

### 开发插件

```js
const MyPlugin = {
  install(Vue, options) {
    Vue.filter('format', () => {...});
  }
};

export default MyPlugin;
```

## 过滤器

> 过滤器函数总接收表达式的值 (之前的操作链的结果) 作为第一个参数，`filterA | filterB | filterC`，`filterB`接收`filterA`的结果，`filterC`接收`filterB`的结果

### 局部注册过滤器

```js
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```

### 全局注册

创建 Vue 实例之前全局定义过滤器：

```js
Vue.filter("capitalize", function (value) {
  if (!value) return "";
  value = value.toString();
  return value.charAt(0).toUpperCase() + value.slice(1);
});

new Vue({
  // ...
});
```

## vuex

流程 ：通过`dispatch`调用`actions`的方法，在`actions`的方法中调用`mutations`的处理数据的方法，mutations 是专门处理`state`的数据的函数。最终通过`getters`的方法获取数据（变量)。

```js
//store.js
import Vue from 'vue'
import Vuex from 'vuex'

export default new Vuex.Store({
    state:{
        // 存放变量，，一定要先定义变量，否则后面有可能出错
        data:''
    },
    mutations:{
        // 处理变量(设置赋值)
        setData(state,value){
            state.data = value
        }
    },
    actions:{
        // 使用commit调用mutations中的方法
        callData({ commit },value){    // 第一个参数返回的是store对象，这里我们只用到commit，所以直接解构出来
            //第一个参数是mutations的函数名
            commit('setData',value)
        }
    },
    getters:{
        // 返回数据的方法
        getData(state){
            return state.data
        }
    }
})

// home.vue  组件
mounted(){
    // 通过dispatch调用actions的方法，第一个参数是函数名
    this.$store.dispatch('callData','传递的值')
}

// 获取变量的组件  about.vue
<div>{{$store.getters.getData}}</div>    // 传递的值
```

## 打包的常见错误

**1.** 文件路径错误

![打包错误2](https://s1.ax1x.com/2020/05/09/YQTVi9.png)

**解决方法**： 增加一个 vue.config.js 的配置文件

```js
// vue.config.js
module.exports = {
  // other config
  productionSourceMap: false,
  publicPath: "./",
};
```

## async

> 解决回调地狱的最终方法

```js
async function test(){
    cosnt data = await 一个promise对象(并且是resolve的状态)
    // data接收的是resolve的参数
}
test();
```

`async` 描述一个函数，函数里面使用`await` 接收一个`promise`对象返回的`resolve`回调函数中的参数

图片路径引入方式

> 通过`require`引入相对路径

```js
require("../../assets/images/avatar.jpg");
```

## router

### 完整的路由导航解析流程(不包括其他生命周期)：

1. 触发进入其他路由。
2. 调用要离开路由的组件守卫`beforeRouteLeave`
3. 调用全局前置守卫：`beforeEach`
4. 在重用的组件里调用 `beforeRouteUpdate`
5. 调用路由独享守卫 `beforeEnter`。
6. 解析异步路由组件。
7. 在将要进入的路由组件中调用`beforeRouteEnter`
8. 调用全局解析守卫 `beforeResolve`
9. 导航被确认。
10. 调用全局后置钩子的 `afterEach` 钩子。
11. 触发 DOM 更新(`mounted`)。
12. 执行`beforeRouteEnter` 守卫中传给 next 的回调函数

## 重写打印

> `vue`的打印输出很不友好，默认不展开，为`...` 我们要一个一个点开
>
> 如果打印的对象中有循环引用，`json`转换不了报错

在`main.js`加入以下代码，使用时：`this.$print('打印的东西')`

```js
Vue.prototype.$print = (obj, type) => {
  type = type || "log";
  try {
    const log = JSON.parse(JSON.stringify(obj));
    console[type](log);
  } catch (error) {
    console[type](log);
  }
};
```

## [处理边界情况](https://cn.vuejs.org/v2/guide/components-edge-cases.html)

### 依赖注入

> 父组件通过`provide`函数返回数据，其所有**后代子组件**都可以通过`Inject`接收数据使用，相当于大范围的`prop`

```js
// 父组件
export default {
  provide(){
    return {
      name: 'gauhar'
    }
  },
 };

// 后代子组件
export default {
    inject: ["name"],
    mounted(){
      console.log(this.name);  // gauhar
    }
  }
```

### 事件侦听器

- 通过 `$on(eventName, eventHandler)` 侦听一个事件
- 通过 `$once(eventName, eventHandler)` 一次性侦听一个事件
- 通过 `$off(eventName, eventHandler)` 停止侦听一个事件

> `$emit`定义自定义事件，`$on`监听(触发)自定义事件，`$off`停止监听

```js
vm.$on("test", function (msg) {
  console.log(msg);
});
vm.$emit("test", "hi");
// => "hi"
```

使用`$once`监听钩子函数

**使用 `hook:`为前缀，为 `vue` 生命周期钩子注册自定义事件。**

```js
mounted: function () {
  var picker = new Pikaday({
    field: this.$refs.input,
    format: 'YYYY-MM-DD'
  })

  this.$once('hook:beforeDestroy', function () {
    picker.destroy()
  })
}
```

### 递归组件

> 使用组件的`name`属性递归自己，`v-if`来判断是否结束
>
> 注意条件判断，不然内存就炸了

```vue
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <HelloWorld v-if="stopFlag" />
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      stopFlag: false
    };
  }
</script>
```

### 内联模板

> 组件标签中添加属性:` inline-template`，则标签中的内容会代替原组件中的`template`内容

不管`my-component`组件文件的`template`中写了什么，只会展示类名为`customer`的内容

```vue
<my-component inline-template>
  <div class="customer">
    <p>These are compiled as the component's own template.</p>
    <p>Not parent's transclusion content.</p>
  </div>
</my-component>
```

### 强制更新

> 迫使 Vue 实例重新渲染。注意它仅仅影响实例本身和插入插槽内容的子组件，而不是所有子组件。
>
> 直接调用即可，如果是因为新增对象或者数组的属性、请使用`this.$set`新增属性

```js
this.$forceUpdate();
```
