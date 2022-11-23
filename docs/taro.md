<Banner />
## 安装

```shell
# 使用 npm 安装 CLI
$ npm install -g @tarojs/cli
# OR 使用 yarn 安装 CLI
$ yarn global add @tarojs/cli
# OR 安装了 cnpm，使用 cnpm 安装 CLI
$ cnpm install -g @tarojs/cli
```

### 安装指定版本号

> 后面拼接`@版本号`

```shell
$ cnpm install -g @tarojs/cli@1.3.42
```

## 初始化项目

```shell
taro init 项目名称
```

然后按照提示输入项目相关信息

### 快速创建页面

> login 是页面名字

```shell
taro create --name login
```

## 运行项目

执行不同端的运行代码，执行命令打包

### sass 报错

> Node Sass could not find a binding for your current environment: Windows 64-bit with Node.js 10.x

```shell
npm install -g mirror-config-china
npm rebuild node-sass
```

### 微信小程序

> 把 dist/weapp 文件夹导入微信开发者工具

## 开发规范

### 不要在调用 this.setState 时使用 this.state

> 由于 this.setState 异步的缘故，这样的做法会导致一些错误，可以通过给 this.setState 传入函数来避免

```js
this.setState({
  value: this.state.value + 1,
}); // ✗ 错误

this.setState((prevState) => ({ value: prevState.value + 1 })); // ✓ 正确
```

### 尽量避免在 componentDidMount 中调用 this.setState

> 因为在 componentDidMount 中调用 this.setState 会导致触发更新

## 尺寸

> 按照 750px 的尺寸来，代码中直接使用`px`单位，taro 会进行编译成`rpx`或`rem`，设计稿的宽度不是 750 时，在`config/index.js`修改`designWidth`
>
> 如果使用的不是小写的`px`，那么不会被编译
>
> 对于头部包含注释 `/*postcss-pxtransform disable*/` 的文件，插件不予处理

### 编译时忽略样式转换

> 写在 css 代码里面

- `/* autoprefixer: ignore next */` : 忽略下一行

- 注释中间多行

  ```css
  /* autoprefixer: off */
  -webkit-box-orient: vertical;
  /* autoprefixer: on */
  ```

- 写成行内样式(不推荐)

### 行内样式

> 在编译时，Taro 会帮你对样式做尺寸转换操作，但是如果是在 JS 中书写了行内样式，那么编译时就无法做替换了，针对这种情况，Taro 提供了 API `Taro.pxTransform` 来做运行时的尺寸转换。
>
> 用这个 api 不用跟`px`

```jsx
render () {
    return (
        <View className='login'>
            <Text style={{marginLeft:Taro.pxTransform(100)}}>Hello world!</Text>
        </View>
    )
}
```

## 路由

### 获取路由参数

> 在`componentWillMount `中通过`this.$router.params.参数名`获取

```js
Taro.navigateTo({
    url: '/pages/cart/cart?id=1'
})

// cart.tsx
componentWillMount () {
    console.log(this.$router.params.id);
}
```

## 样式

### 外部样式类

如果想传递样式给引用的自定义组件，不能使用 prop 传递类名；需要利用 `externalClasses` 定义段定义若干个外部样式类

> `externalClasses` 需要使用 **短横线命名法 (kebab-case)**

```js
/* CustomComp.js  组件 */
export default class CustomComp extends Component {
  static externalClasses = ["my-class"];

  render() {
    return (
      <View className="my-class">这段文本的颜色由组件外的 class 决定</View>
    );
  }
}
```

```js
/* MyPage.js */
export default class MyPage extends Component {
  render() {
    return <CustomComp my-class="red-text" />;
  }
}
```

```css
/* MyPage.scss */
.red-text {
  color: red;
}
```

### 全局样式类

> 如果想让外部的样式类名能够影响到子组件，那么就在子组件的内部设置`options`的`addGlobalClass`属性为`true`

```ts
/* CustomComp.js */
export default class CustomComp extends Component {
  static options = {
    addGlobalClass: true,
  };

  render() {
    return (
      <View className="red-text">这段文本的颜色由组件外的 class 决定</View>
    );
  }
}
```

```css
/* 组件外的样式定义 */
.red-text {
  color: red;
}
```

## State

> 使用 setState 来修改 state 里面的数据

重新赋值

```js
constructor(props) {
    super(props)
    this.timer = 0
    this.state = {
        date: new Date()
        name: 'chan',
        num: 1
    }
}
this.setState({
    name: 'gauhar'
})
```

如果使用到原来的 State 的值

```js
this.setState((state) => ({
  name: "gauhar" + state.name, //  gauharchan
}));

this.setState((data) => ({
  num: data.num++, // 无效 wrong
}));

this.setState((data) => ({
  num: data.num + 1, // right
}));
```

状态更新一定是异步的，所以不能在 `setState` 马上拿到 `state` 的值。正确的做法是在 `setState` 的第二个参数传入一个 callback

```js
this.setState(
  {
    name: "gauhar",
  },
  () => {}
);
```

## 事件

### 传参

> 传参时，通过`bind`指定上下文并传参，函数可以指定一个`event`对象

```tsx
handlePlus(id, num, e){
    console.log(id);  // 123
    console.log(num); // this.state.num

    console.log(e);   // event
}

render() {
    return (
        <View className='cart'>
            <Text>cart45644545645454</Text>
            <View>现在的时间是{this.state.date.toLocaleTimeString()}</View>
            <View onClick={this.handlePlus.bind(this, 123, this.state.num)}>{this.state.num}</View>
        </View>
    )
}
```

## 条件渲染

### if-else

```tsx
class LoginStatus extends Component {
  render() {
    const isLoggedIn = this.props.isLoggedIn;
    // 这里最好初始化声明为 `null`，初始化又不赋值的话
    // 小程序可能会报警为变量为 undefined
    let status = null;
    if (isLoggedIn) {
      status = <Text>已登录</Text>;
    } else {
      status = <Text>未登录</Text>;
    }

    return <View>{status}</View>;
  }
}
```

### 短路运算`&&` 、`||`

```tsx
class LoginStatus extends Component {
  render() {
    const isLoggedIn = this.props.isLoggedIn;

    return (
      <View>
        {isLoggedIn && <Text>已登录</Text>}
        {isLoggedIn || <Text>未登录</Text>}
      </View>
    );
  }
}

// app.js
import LoginStatus from "./LoginStatus";

// 这样会渲染 `已登录`
class App extends Component {
  render() {
    return (
      <View>
        <LoginStatus isLoggedIn={true} />
      </View>
    );
  }
}
```

### 枚举条件渲染

> 下面代码中通过指定`loadingStatus`是哪一种状态而决定

```tsx
Loading(props) {
    const { loadingText, loadingStatus, onRetry } = props
    return (
        <View className='loading-status'>
            {
                {
                    'loading': loadingText,
                    'fail': <View onClick={onRetry}> 加载失败, 点击重试 </View>,
                    'no-more': '没有更多了'
                }[loadingStatus] /** loadingStatus 是 `loading`、`fail`、`no-more`  其中一种状态 **/
            }
        </View>
    )
}

render() {
    const isLogin = false

    return (
        <View className='cart'>
            <Text>cart45644545645454</Text>
            <View>现在的时间是{this.state.date.toLocaleTimeString()}</View>
            <View onClick={this.handlePlus.bind(this, 123, this.state.num)}>{this.state.num}</View>
            <View>{isLogin && <Text>已登录</Text>}</View>
            <View>{isLogin || <Text>未登录</Text>}</View>
            <View>{this.Loading({
                    loadingText:'加载中',
                    loadingStatus: 'no-more',
                    onRetry: ()=>{console.log();}
                })}</View>
        </View>
    )
}
```

## 列表渲染

> 数组通过 map 循环，返回处理好的节点模板。先处理好要循环的数组，在循环

```tsx
state = {
    name: 'gauhar',
    arr: [
        {
            icon: '🍎',
            id: 0
        },
        {
            icon: '🍌',
            id: 1
        },
        {
            icon: '🍇',
            id: 2
        },
        {
            icon: '🌰',
            id: 3
        },
    ]
}

render () {
    return (
        <View className='index'>
            <View>
                {
                    this.state.arr.map(item => (
                        <View key={item.id}>
                            <Text>{item.icon}</Text>
                            <Text>{item.id}</Text>
                        </View>

                    ))
                }
            </View>
        </View>
    )
}
```

## Children 与组合

### children

> 可以把 `this.props.children` 理解为 `slot` 的语法糖

```tsx
// index.tsx   render()
<Header name="gauhar123">kjkk</Header>
```

```tsx
// Header.tsx
render() {
    return (
        <View>
            <Text>this is header component</Text>
            <View>{this.props.name}</View>
            <View>{this.props.children}</View>
        </View>
    )
}
```

组件标签之间的内容，在组件中可以通过`this.props.children`获取

### 组合

> 注意是写在标签里，相当于属性传值，通过`props`访问。必须使用`render`开头，驼峰式

```tsx
state = {
    name: 'gauhar',
    arr: [
        {
            icon: '🍎',
            id: 0
        },
        {
            icon: '🍌',
            id: 1
        },
        {
            icon: '🍇',
            id: 2
        },
        {
            icon: '🌰',
            id: 3
        },
    ]
}
render(){
    const flag = true  // 这里如果设置为false
    return (
        <Header name='gauhar123'
            renderHeader = {
                <View>header</View>
            }
            renderTest = {
                <Block>
                    {this.state.arr.map((item) => (
                        <View key={item.id}>{item.icon}</View>
                    ))}
                    {flag && <View>test1111</View>}  {/* 如果flag是false，则显示false */}
                </Block>

            }
            renderFooter = {
                <View>Footer</View>
            }
            >
            <View>content</View>
        </Header>
    )
}
```

组件文件

```tsx
render() {
    return (
        <View>
            <Text>this is header component</Text>
            <View>{this.props.name}</View>
            <View>{this.props.renderHeader}</View>
            <View>{this.props.children}</View>
            <View>{this.props.renderFooter}</View>
        </View>
    )
}
```

## 组件通信

### 父传子

> 都一样。
>
> 父传子：通过 props 获取
>
> 下面代码中`<Header name="gauhar" />`使用了子组件`Header`，子组件通过`this.props.name`即可访问父组件传的值

```tsx
getSonData(data){
    console.log(data);
}
return (
    <View className='index'>
        {isShow && <View onClick={this.handleNavigator}>show</View>}
        <Text onClick={this.handleChangeName}>Hello {name}!</Text>
        <Header name='gauhar123'
            getSonData = {this.getSonData}
            renderHeader = {
                <View>header</View>
            }
            renderTest = {
                <Block>
                    {this.state.arr.map((item) => (
                        <View key={item.id}>{item.icon}</View>
                    ))}
                    {flag ? <View>test1111</View> : <View>false</View>}
                    {flag && <View>test1111</View>}
                </Block>

            }
            renderFooter = {
                <View>Footer</View>
            }
            >
            <View>content</View>
        </Header>
        <View>
            {
                this.state.arr.map(item => (
                    <View key={item.id}>
                        <Text>{item.icon}</Text>
                        <Text>{item.id}</Text>
                    </View>

                ))
            }
        </View>
    </View>
)
```

子组件代码

```tsx
getSonData(){
    this.props.getSonData(123)
}

render() {
    return (
        <View>
            <Text onClick={this.getSonData}>this is header component</Text>
            <View>{this.props.name}</View>
            <View>{this.props.renderHeader}</View>
            <View>{this.props.renderTest}</View>
            <View>{this.props.children}</View>
            <View>{this.props.renderFooter}</View>
        </View>
    )
}
```

### 子传父

> 通过自定义函数的参数传递。父组件编写自定义函数，也是通过`props`传递。子组件`this.props.自定义函数名(传递的值)`。子组件执行函数（代码在上面`getSonData`）

## Render Props

> 类似组合，这个使用的是`props函数`，命名和组合的规则一样，以`render`开头。
>
> `render props`就是通过`props`传递一个函数，通过函数的**参数**达到传值的目的

```tsx
// 主页面，使用两个组件的页面，类似index.tsx
import Taro, { Component, Config } from "@tarojs/taro";
import { View, Text } from "@tarojs/components";
import "./login.scss";
import Cat from "../../components/cat/cat";
import Mouse from "../../components/mouse/mouse";

export default class Login extends Component {
  /**
   * 指定config的类型声明为: Taro.Config
   *
   * 由于 typescript 对于 object 类型推导只能推出 Key 的基本类型
   * 对于像 navigationBarTextStyle: 'black' 这样的推导出的类型是 string
   * 提示和声明 navigationBarTextStyle: 'black' | 'white' 类型冲突, 需要显示声明类型
   */

  state = {};

  componentWillMount() {}

  componentDidMount() {}

  componentWillUnmount() {}
  config: Config = {
    navigationBarTitleText: "登录",
  };

  componentDidShow() {}

  componentDidHide() {}

  onShareAppMessage({ from, target }) {
    console.log(from, target);

    // if (res.from === 'button') {
    //   // 来自页面内转发按钮
    //   console.log(res.target)
    // }
    return {
      title: "自定义转发标题",
      path: "/page/user?id=123",
    };
  }

  render() {
    return (
      <View className="login">
        <Text style={{ marginLeft: Taro.pxTransform(100) }}>Hello world!</Text>
        <Mouse renderCat={(mouse) => <Cat mouse={mouse} />} />
      </View>
    );
  }
}
```

Mouse.tsx

```tsx
import { View } from "@tarojs/components";
import "./mouse.scss";

export default class Mouse extends Taro.Component {
  constructor(props) {
    super(props);
    this.state = {
      x: 0,
      y: 0,
    };
  }

  handleClickPosition(e) {
    const { x, y } = e.detail;
    this.setState({
      x,
      y,
    });
  }

  render() {
    return (
      <View onClick={this.handleClickPosition}>
        {this.props.renderCat(this.state)}
      </View>
    );
  }
}
```

Cat.tsx

```tsx
import { View } from "@tarojs/components";
import "./cat.scss";

export default class Cat extends Taro.Component {
  static defaultProps = {
    mouse: {
      x: 0,
      y: 50,
    },
  };

  render() {
    const { mouse } = this.props;
    return (
      <View className="container">
        <Image
          src="https://img.yzcdn.cn/vant/cat.jpeg"
          style={{
            position: "absolute",
            left: Taro.pxTransform(mouse.x),
            top: Taro.pxTransform(mouse.y),
          }}
        />
      </View>
    );
  }
}
```

这个`mouse`组件像是一个工具人，他指定一个`render props`函数，然后执行函数把在`mouse`组件要传递的东西通过参数传递。`render props`返回`cat`组件，并把`mouse`的值赋值给`cat`的`props`。然后`cat`就可以通过`this.props.XXX`使用工具人`mouse`的值啦

## ref

> 不管在任何情况下，Taro 都推荐你使用函数的方式创建 ref。

### 字符串

> 和 vue 一样的定义，但通过`this.refs.字符串名`调用。如果是小程序原生组件，在`didMount`之后才能调用

```js
class MyComponent extends Component {
  componentDidMount() {
    // 如果 ref 的是小程序原生组件，那只有在 didMount 生命周期之后才能通过
    // this.refs.input 访问到小程序原生组件
    if (process.env.TARO_ENV === "weapp") {
      // 这里 this.refs.input 访问的时候通过 `wx.createSeletorQuery` 取到的小程序原生组件
    } else if (process.env.TARO_ENV === "h5") {
      // 这里 this.refs.input 访问到的是 `@tarojs/components` 的 `Input` 组件实例
    }
  }

  render() {
    return <Input ref="input" />;
  }
}
```

### 函数

> 在函数中被引用的组件会作为函数的第一个参数传递。如果是被引用的组件是自定义组件，那可以在任意的生命周期访问引用。

```tsx
getHeader(node){
    this.header = node  // 第一个参数是组件的实例，this.header指向Header组件的实例引用
    node.say() // 直接可以调用header组件的say方法
}
render() {
    return (
    	<Header ref={this.getHeader} />
    )
}
```

## 多端开发

### 内置环境变量

> 通过` process.env.TARO_ENV`的值来判断是哪一个平台。目前有 `weapp` / `swan` / `alipay` / `h5` / `rn` / `tt` / `qq` / `quickapp` 八个取值
>
> `jsx`中也可以使用

```tsx
render () {
  return (
    <View>
      {process.env.TARO_ENV === 'weapp' && <ScrollViewWeapp />}
      {process.env.TARO_ENV === 'h5' && <ScrollViewH5 />}
    </View>
  )
}
```

### 统一接口的多端文件

#### 多端组件

> 一个组件，存在多个 tsx 文件。命名:`文件名.weapp(平台的名字，weapp是微信).tsx`，还有一个不带平台的文件，表示除了已有文件处理的其他平台
>
> 引用的方式依然和之前保持一致，`import` 的是不带端类型的文件名，在编译的时候会自动识别并添加端类型后缀

#### 多端脚本逻辑

> 和多端组件一样，一般用在`util`工具文件夹下

## 使用`async/await`

1. 安装

   ```sh
   yarn add @tarojs/async-await
   ```

2. 随后在项目入口文件 `app.js` 中直接 `import` ，就可以开始使用 `async functions` 功能了

   ```js
   // src/app.js

   import "@tarojs/async-await";
   ```

## 使用第三方组件

> 在页面的`config`中的`usingComponents`中引入使用。**注意跨端**

```jsx
import Taro, { Component } from "@tarojs/taro";
import { View } from "@tarojs/components";

function initChart() {
  // ....
}

export default class Menu extends Component {
  static defaultProps = {
    data: [],
  };

  config = {
    // 定义需要引入的第三方组件
    usingComponents: {
      "ec-canvas": "../../components/ec-canvas/ec-canvas", // 书写第三方组件的相对路径
    },
  };

  constructor(props) {
    super(props);
    this.state = {
      ec: {
        onInit: initChart,
      },
    };
  }

  componentWillMount() {
    console.log(this); // this -> 组件 Menu 的实例
  }

  render() {
    return (
      <View>
        <ec-canvas
          id="mychart-dom-area"
          canvas-id="mychart-area"
          ec={this.state.ec}
        ></ec-canvas>
      </View>
    );
  }
}
```

## 插件

> 使用插件前，使用者要在 `app.js` 的配置中声明需要使用的插件，**例如**

```ts
import Taro, { Component } from "@tarojs/taro";
class App extends Component {
  config = {
    plugins: {
      myPlugin: {
        // 这个myPlugin是自定义名字
        version: "1.0.0", // 插件版本号
        provider: "wxidxxxxxxxxxxxxxxxx", // 插件appid
      },
    },
  };
}
```

在页面中使用插件

```tsx
import Taro, { Component } from "@tarojs/taro";

export default class PageA extends Component {
  config = {
    // 定义需要引入的插件
    usingComponents: {
      "hello-component": "plugin://myPlugin/hello-component",
    },
  };
}
```

## MobX

> 状态管理
>
> <font color='red'>observable 对象里的数据被改变时，触发`componentWillReact`钩子函数</font>

### 安装

```shell
yarn add mobx@4.8.0 @tarojs/mobx @tarojs/mobx-h5 @tarojs/mobx-rn
# 或者使用 npm
npm install --save mobx@4.8.0 @tarojs/mobx @tarojs/mobx-h5 @tarojs/mobx-rn
```

`app.tsx`

```tsx
import Taro, { Component, Config } from "@tarojs/taro";
import { Provider } from "@tarojs/mobx";
import Index from "./pages/index";

import store from "./store";

class App extends Component {
  // 在 App 类中的 render() 函数没有实际作用
  // 请勿修改此函数
  render() {
    return (
      <Provider store={store}>
        <Index />
      </Provider>
    );
  }
}
```

`store/index.ts`

```ts
import counter from "./counter";

const store = {
  counter,
};

export default store;
```

`store/counter.ts`

```ts
import { observable } from "mobx";

const counterStore = observable({
  // 可观察对象
  counter: 0,
  counterStore() {
    this.counter++;
  },
  increment() {
    this.counter++;
  },
  decrement() {
    this.counter--;
  },
  square() {
    this.counter = this.counter * this.counter;
  },
  sqrt() {
    this.counter = Math.sqrt(this.counter);
  },
  incrementAsync() {
    setTimeout(() => {
      this.counter++;
    }, 1000);
  },
});
export default counterStore;
```

> 使用`inject`引用`store`的东西，将 `Provider` 中设置的 `store` 提取到组件的 `props` 中，该 `API` 只适用于`类组件`

`pages/index.tsx`

```js
@inject("counter") // 通过this.props访问
@observer
class Index extends Component {
  sqrt() {
    this.props.counter.sqrt();
  }
}
```

## 生命周期

### 入口文件

#### componentWillMount()

> 对应小程序的`onLaunch`，全局触发一次，通过`this.$router.params`访问路由地址、携带的参数

#### componentDidMount()

> 对应小程序的`onLaunch`，在`componentWillMount`之后执行

#### componentDidShow()

> 对应小程序的`onShow`

#### componentDidHide()

> 对应小程序的`onHide`

#### componentDidCatchError(String error)

> 在微信/百度/字节跳动/支付宝小程序中这一生命周期方法对应 `onError`，H5/RN 中尚未实现

程序发生脚本错误或 API 调用报错时触发，微信小程序中也可以使用 `Taro.onError` 绑定监听

#### componentDidNotFound(Object)

> 在微信/字节跳动小程序中这一生命周期方法对应 `onPageNotFound`，其他端尚未实现
> 微信小程序中，基础库 1.9.90 开始支持

程序要打开的页面不存在时触发，微信小程序中也可以使用 `Taro.onPageNotFound` 绑定监听

### 页面文件

## 坑

### 组件传值不更新

> 通过`props`传值，组件中获取的值还是旧数据

解决方法： 在父组件更新数据之前，先清空，再赋值
