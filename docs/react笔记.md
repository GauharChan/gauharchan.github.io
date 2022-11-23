> 一个只会`vue`的前端菜鸡学`react`

## react18 挂载

```jsx
import { createRoot } from "react-dom/client";
const reactDom = createRoot(document.getElementById("root"));
reactDom.render(<div>HelloWorld</div>);
```

## JSX

### JSX 防止注入攻击

你可以安全地在 JSX 当中插入用户输入内容：

```jsx
const title = response.potentiallyMaliciousInput;
// 直接使用是安全的：
const element = <h1>{title}</h1>;
```

React DOM 在渲染所有输入内容之前，默认会进行[转义](https://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html)。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 [XSS（cross-site-scripting, 跨站脚本）](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击。

特殊字符进行转义，以防止 xss 攻击

```
& becomes &amp;
< becomes &lt;
> becomes &gt;
" becomes &quot;
' becomes &#39;
```

### 表达式

在 JSX 语法中，你可以在大括号内放置任何有效的 [JavaScript 表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions)。例如，`2 + 2`，`user.firstName` 或 `formatName(user)` 都是有效的 JavaScript 表达式。

比如在大括号中使用`js`变量

```jsx
const name = "Gauhar Chan";
const element = <h1>Hello, {name}</h1>;
```

jsx 也是表达式，Babel 会把 JSX 转译成一个名为 `React.createElement()` 函数调用。上面的`element`大概会进行这样的转换

```js
const name = "Gauhar Chan";
const element = React.createElement("h1", {}, "Hello, " + name);
```

而`element`这个`React 元素`大概长这样

```js
// 注意：这是简化过的结构
const element = {
  type: "h1",
  props: {
    children: "Hello, Gauhar Chan",
  },
};
```

## 组件 & props

### class 组件

> `jsx`中使用`this.props`访问组件的`props`

```jsx
import React from "react";

export default class HelloWorld extends React.Component {
  render() {
    return <div>HelloWorld {this.props.name}</div>;
  }
}
```

### function 组件

> 该函数是一个有效的 React 组件，因为它接收唯一带有数据的 “props”（代表属性）对象与并返回一个 React 元素。这类组件被称为“函数组件”，因为它本质上就是 JavaScript 函数。

```jsx
export function HelloFuntion(props) {
  return <div>HelloFuntion, {props.name}</div>;
}
```

### props

当 React 元素为用户自定义组件时，它会将 JSX 所接收的属性（attributes）以及子组件（children）转换为单个对象传递给组件，这个对象被称之为 “props”。

::: warning 警告 ⚠️

**所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。**

即子组件不能直接修改父组件的props，单向数据流

:::

#### 修改props的值

上面有说到不能直接改，那就换个思路，交给父组件自己更新(因为`state` 是私有的，并且完全受控于当前组件。)。通过函数传参的方式实现。

子组件

```jsx
import React from "react";

export default class HelloWorld extends React.Component {
  handle () {
    this.props.onUserChange({
      name: 'gauhar chan'
    })
  }
  render() {
    return (<div onClick={this.handle.bind(this)}>HelloWorld { this.props.user.name }</div>)
  }
}
```

父组件

```jsx
handleChangeUser(user) {
  // 修改值
  this.setState({
    user
  })
}
<HelloWorld user={ this.state.user } onUserChange={ (user) => this.handleChangeUser(user)}/>
```

- 在父组件中定义了一个修改state的函数，并通过`props`传递给子组件
- 子组件监听了点击事件，最终通过传入参数并调用父组件的函数实现修改值。

::: tip 提示

绑定事件的时候要注意，因为是react模板解析机制是上下文会是立即执行内容，所以要加上handle.bind(this)来指定上下文。

:::

### 使用组件

> 上面的两个组件放在了`./components/helloWorld/index.jsx`文件中

```jsx
import HelloWorld, { HelloFuntion } from "./components/helloWorld";
class Game extends React.Component {
  render() {
    return (
      <div>
        <HelloWorld name="chan" />
        <HelloFuntion name="gauhar" />
      </div>
    );
  }
}
const reactDom = createRoot(document.getElementById("root"));
reactDom.render(<Game />);
```

::: warning 警告 ⚠️

**注意：** 组件名称必须以大写字母开头。

React 会将以小写字母开头的组件视为原生 DOM 标签。例如，`<div />` 代表 HTML 的 div 标签，而 `<Welcome />` 则代表一个组件，并且需在作用域内使用 `Welcome`。

:::

## State

State 与 props 类似，但是 state 是私有的，并且完全受控于当前组件。

`class组件`使用`state`，必须创建构造函数并通过`super`将`props`传递到父类的构造函数中

然后在该函数中为 `this.state` 赋初值

```jsx
constructor(props) {
  super(props);
  this.state = {
    name: 'gauhar chan'
  }
}
```

### setState

通过`this.setState`可以修改`state`中的值。

得益于 `setState()` 的调用，React 能够知道 state 已经改变了，然后会重新调用 `render()` 方法来确定页面上该显示什么。

::: warning

构造函数是唯一可以给 `this.state` 赋值的地方。除此之外不要直接修改State。

:::

### State 的更新可能是异步的

出于性能考虑，React 可能会把多个 `setState()` 调用合并成一个调用。

因为 `this.props` 和 `this.state` 可能会异步更新，所以不要依赖他们的值来更新下一个状态。

如需基于之前的 state 来设置当前的 state，可以让 `setState()` **接收一个函数**而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数：

```jsx
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

另外如果需要state修改值之后，执行某些东西，可以使用setState的第二个参数callback回调函数

```jsx
this.setState({
	name: 'gauhar'
}, () => {
	// state.name更新后
})
```

::: tip

在 React 应用中，任何可变数据应当只有一个相对应的唯一“数据源”。通常，state 都是首先添加到需要渲染数据的组件中去。然后，如果其他组件也需要这个 state，那么你可以将它提升至这些组件的最近共同父组件中。你应当依靠[自上而下的数据流](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html#the-data-flows-down)，而不是尝试在不同组件间同步 state。

:::

## 生命周期

![image-20220425170612266](https://raw.githubusercontent.com/GauharChan/Picture-bed/main/img/image-20220425170612266.png)

## 事件处理

React 元素的事件处理和 DOM 元素的很相似，但是有一点语法上的不同：

- React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
- 使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。
- 事件对象`event` 是一个合成事件。React 根据 [W3C 规范](https://www.w3.org/TR/DOM-Level-3-Events/)来定义这些合成事件，所以你不需要担心跨浏览器的兼容性问题。React 事件与原生事件不完全相同。如果想了解更多，请查看 [`SyntheticEvent`](https://zh-hans.reactjs.org/docs/events.html) 参考指南。

### 在class组件中要注意上下文

- 使用bind绑定this，**默认`event`是最后一个参数隐式传递**
   ```jsx
    <div onClick={this.handle.bind(this, '参数')}>HelloWorld { this.props.user.name }</div>
   ```
   
- 使用一个函数返回，通常使用箭头函数简写，**event需要手动显示的传递**
  
   ```jsx
    <div onClick={(event) => this.handle('参数', event)}>HelloWorld { this.props.user.name }</div>
   ```
   
   使用箭头函数有个注意的点，**当这种用法被用于props传递给子组件，这些组件可能会进行额外的重新渲染**。我们通常建议在构造器中绑定或使用 class fields 语法来避免这类**性能问题**。
   
- class fields 
  
   > 目前还是实验性的语法，不过`Create React App`默认启用此语法。
   
   ```jsx
   export default class HelloWorld extends React.Component {
     handle = () => {
       this.props.onUserChange({
         name: 'gauhar chan'
       })
     }
     render() {
       return (<div onClick={this.handle}>HelloWorld { this.props.user.name }</div>)
     }
   }
   ```
   
   上面监听了点击事件的回调，这里有个问题，你并不能直接`onClick={this.handle('参数')}`传参，因为这样函数会立即执行，所以这种情况下似乎又回到了上面的`箭头函数`和`bind`。所以我认为这个`class fields`适合用于`不用传参`和`props传递`的情况。对于props而言，是我们手动触发的。
   
   **用于props的例子：**
   
   ```jsx
   // 子组件
   export default class HelloWorld extends React.Component {
     handle = () => {
       this.props.onUserChange({
         name: 'gauhar chan'
       })
     }
     render() {
       return (<div onClick={this.handle}>HelloWorld { this.props.user.name }</div>)
     }
   }
   
   // 父组件
   export default class Game extends React.Component {
     constructor(props) {
       super(props)
       this.state = {
         user: {
           name: 'gauhar'
         }
       }
     }
     handleChangeUser = (user) => {
       this.setState({
         user
       })
     }
     render() {
       return <HelloWorld user={ this.state.user } onUserChange={this.handleChangeUser}/>
     }
   }
   ```

## 条件渲染

React 中的条件渲染和 JavaScript 中的一样，使用 JavaScript 运算符 [`if`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) 或者[条件运算符](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)去创建元素来表现当前的状态，然后让 React 根据它们来更新 UI。

### && 运算符

这里就涉及到`js`这个`&&`的特性，如果表达式是true，那么会返回运算符右边的东西，否则会返回表达式本身的值。

```jsx
render() {
  const flag = false
  return (
    <div>
    	{ count && <h1>true</h1> }
  	</div>
  )
}
```

上面渲染一个空div，这个是取决于什么数据类型，像数字**0/NaN**这种就会渲染

如果`flag`是0，我们知道0会进行隐式转换条件false，但此时返回的是0本身（NaN同理）

```jsx
render() {
  const flag = 0
  return (
    <div>
    	{ count && <h1>true</h1> }
  	</div>
  )
}
```

最终会渲染`<div>0</div>`

## 阻止组件渲染

在极少数情况下，你可能希望能隐藏组件，即使它已经被其他组件渲染。若要完成此操作，你可以让 `render` 方法直接返回 `null`，而不进行任何渲染。

在组件的 `render` 方法中返回 `null` 并不会影响组件的生命周期。例如`componentDidUpdate` 依然会被调用。

## 表单

### 受控组件

就是通过`state`去管理，将`state`的值传入到组件的`value`，监听`onChange`事件通过`setState`更新值

> 对比之下，`vue`的双向绑定确实很香

### 处理多个输入

当需要处理多个 `input` 元素时，我们可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作。

```jsx
export class IForm extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      phone: '',
      password: ''
    }
  }
  handleInputChange = (event) => {
    const { name, value } = event.target
    this.setState({
      [name]: value
    })
  }
  render() {
    const { phone, password } = this.state
    return (
      <div>
        <form>
          <label>
            手机号码:
            <input
              name="phone"
              type="number"
              value={phone}
              onChange={this.handleInputChange} />
          </label>
          <br />
          <label>
            密码:
            <input
              name="password"
              type="password"
              value={password}
              onChange={this.handleInputChange} />
          </label>
        </form>
        <button onClick={ () => { console.log(this.state) } }>submit</button>
      </div>
    )
  }
}
```

## 组件的包含关系

> 插槽`slot`
>
> 到目前为止我发现了，`react`这是所有的东西都通过`props`传递

在vue中，默认的插槽名称叫`default`，react中则是`props.children`

如果要自定义名称，那么就通过props自定义

```jsx
export function HelloFuntion(props) {
  return (
    <div>
      HelloFuntion, { props.name }
      <div>{ props.children }</div>
      <div>{ props.diy }</div>
    </div>
  )
}
// ...
function DiyCom() {
  return (
    <div>我是自定义插槽</div>
  )
}
<HelloFuntion name="gauhar" diy={ DiyCom() } >
  <div>我是默认插槽</div>
</HelloFuntion>
```

## 代码分割

为了避免搞出大体积的代码包，在前期就思考该问题并对代码包进行分割是个不错的选择。 代码分割是由诸如 [Webpack](https://webpack.docschina.org/guides/code-splitting/)，[Rollup](https://rollupjs.org/guide/en/#code-splitting) 和 Browserify（[factor-bundle](https://github.com/browserify/factor-bundle)）这类打包器支持的一项技术，能够创建多个包并在运行时动态加载。

对你的应用进行代码分割能够帮助你“懒加载”当前用户所需要的内容，能够显著地提高你的应用性能。尽管并没有减少应用整体的代码体积，但你可以避免加载用户永远不需要的代码，并在初始加载的时候减少所需加载的代码量。

### React.lazy

> 这么看来，`vue`确实有很重的`react`影子呀
>
> 这不就是`vue3`还在实验阶段的`suspense`吗😂**(2022/05/05注)**

```js
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```

此代码将会在组件首次渲染时，自动导入包含 `OtherComponent` 组件的包。==说白了就是异步组件==

`React.lazy` 接受一个函数，这个函数需要动态调用 `import()`。它必须返回一个 `Promise`，该 Promise 需要 resolve 一个 `default` export 的 React 组件。

然后应在 `Suspense` 组件中渲染 lazy 组件，如此使得我们可以使用在等待加载 lazy 组件时做优雅降级（如 loading 指示器等）。

```jsx
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </div>
  );
}
```

切换**两个异步组件**显示隐藏时，会出现一个情况就是。新的组件还没准备好，就被渲染出来了，因此用户会看到屏幕闪烁，这个时候可以使用 [`startTransition`](https://zh-hans.reactjs.org/docs/react-api.html#starttransition) API 来进行过渡。新的组件还没准备好时，保留显示旧的组件，新的准备好了，进行过渡切换

```jsx
import React, { Suspense } from 'react';
import Tabs from './Tabs';
import Glimmer from './Glimmer';

const Comments = React.lazy(() => import('./Comments'));
const Photos = React.lazy(() => import('./Photos'));

function MyComponent() {
  const [tab, setTab] = React.useState('photos');

  function handleTabSelect(tab) {
    startTransition(() => {
      setTab(tab);
    });
  }

  return (
    <div>
      <Tabs onTabSelect={handleTabSelect} />
      <Suspense fallback={<Glimmer />}>
        {tab === 'photos' ? <Photos /> : <Comments />}
      </Suspense>
    </div>
  );
}
```

### 基于路由的代码分割

这里是一个例子，展示如何在你的应用中使用 `React.lazy` 和 [React Router](https://reactrouter.com/) 这类的第三方库，来配置基于路由的代码分割。

```jsx
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  </Router>
);
```























