# Taro+Nut UI开发中的坑

| 依赖  | 版本  |
| ----- | ----- |
| taro  | 3.6.8 |
| vue   | 3.3.4 |
| nutui | 4.0.4 |

## 音频实例

### createInnerAudioContext 报错 Cannot read property 'then' of undefined

```ts
const state = reactive<any>({
  //...
  InnerAudioCtx: null,
})
//创建音频实例
function createAudioText() {
  state.InnerAudioCtx = Taro.createInnerAudioContext();
  state.InnerAudioCtx.onPlay(() => {
    console.log('开始了');
  });
  state.InnerAudioCtx.onError((res) => {
    console.log(res);
  });
}
// 其他的代码..
function play() {
  state.InnerAudioCtx.src = 'https://...'
  state.InnerAudioCtx.play()
}
```

这是一个很普通的创建音频和播放的代码。

然而神奇的是`src`赋值之后在微信小程序(web没问题)中你会得到这么一个报错，并且无法查看`WAServiceMainContext.js`这个文件报什么错，`source`那边提示开发者工具隐藏了

![image-20230706140610537](https://raw.githubusercontent.com/GauharChan/Picture-bed/main/img/image-20230706140610537.png)

我在网上查了很久，有看到遇到相同问题的，但是没有解决的结果，因为不能看到具体什么代码报错，因此只能不断地尝试；

- 本地src路径
- 基础库
- ..

#### 解决结果

> `web`和`微信小程序`都可以

最后我把目光放到了proxy上面，这里的实例我把它放到了reactive中，因此它经过proxy，会不会src属性因此受到什么影响；于是我把它改为了一个普通的变量，就此解决了

```ts
/** 音频播放器实例，不可以使用reactive，否则微信小程序会发生错误 */
let InnerAudioCtx: Taro.InnerAudioContext | null = null;
//创建音频实例
function createAudioText() {
  InnerAudioCtx = Taro.createInnerAudioContext();
  InnerAudioCtx.onPlay(() => {
    console.log('开始了');
  });
  InnerAudioCtx.onError((res) => {
    console.log(res);
  });
}
// 其他的代码..
function play() {
  InnerAudioCtx!.src = 'https://...'
  InnerAudioCtx!.play()
}
```

### 在**web**中调用音频实例的销毁方法报错

```ts
const audioContext = Taro.createInnerAudioContext()
audioContext.destroy() // 报错
```

![企业微信截图_6a92ba3f-b255-40ef-a5d2-4c25a9be404f](https://raw.githubusercontent.com/GauharChan/Picture-bed/main/img/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_6a92ba3f-b255-40ef-a5d2-4c25a9be404f.png)

我理解的报错原因是

web实际上就是创建一个`audio`元素，但是Taro压根就没有插入body中，destroy却调用了document.body.removeChild，所以就报错了

![企业微信截图_583385e3-2b1b-4109-985c-57698253ab32](https://raw.githubusercontent.com/GauharChan/Picture-bed/main/img/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_583385e3-2b1b-4109-985c-57698253ab32.png)

#### 解决结果

> 更新一下：针对这个问题我提了一个[PR](https://github.com/NervJS/taro/pull/14130)，已被`Merged`，发布于[v3.6.9](https://github.com/NervJS/taro/releases/tag/v3.6.9)

web不调用`destroy`方法，手动把实例设置为`null || undefined`

## 上传文件

> web端存在问题
>
> 微信小程序没有问题

### 上传文件的接口要求form-data的格式传递参数

> 上传文件的接口要求form-data的格式传递参数

```ts
Taro.uploadFile({
  url: uploadCfg.value.upUrl,
  filePath: item.src,
  timeout: 60000,
  name: 'file',
  header: {
    'Content-Type': 'multipart/form-data',
  },
  formData: {
    token: uploadCfg.value.token,
  },
  success: (res) => {}
})
```

这里我们手动指定了`Content-Type`，浏览器解析的结果不对

于是，把header去掉，使用默认的即可

### 文件名称服务端无法从我们传递的`file对象`解析到

> 文件名称服务端无法从我们传递的`file对象`解析到

**web端获取到的file对象和微信小程序的不一致**，估计是Taro处理过。打印web端的file对象可以发现，它上面还有一个属性名为`originalFileObj`

这个`originalFileObj`原始对象就有文件的名称等信息

所以我的解决方案是在`originalFileObj`获取文件名，并在 `formData`中传多一个`fileName`(和服务端约定好的)字段给接口

## 行内样式

如果涉及到单位，一定要使用`Taro.pxTransform`，不然taro会直接把相关的样式代码直接删除

## definePageConfig不生效？

**taro版本高于3.4**，页面用`definePageConfig`修改导航栏文字等，运行后没有生效

### 解决结果

> [参考文档](https://docs.taro.zone/docs/page-config/#%E5%9C%A8%E9%A1%B5%E9%9D%A2%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E4%B8%AD%E4%BD%BF%E7%94%A8)

这个问题产生的原因非常的凑巧，假设我现有一个分包`packageMy`

- 我有一个Index.vue，页面路径自然就是`packageMy/Index`
- 同时我有`packageMy/index.ts`处理了其他的事情

所以这个路径和文件重名了，因为taro优先取`路径名.ts（官网描述的是叫page.config.js）`作为页面的配置，因为`definePageConfig`是v3.4后面才有的

# Nut UI

## textarea

### `autofocus`属性`web`端不生效

#### 解决结果

> IOS有限制，无法解决

通过ref直接执行dom的focus方法

```html
<NutTextarea
  ref="editTextAreaRef"
  max-length="50"
  autofocus
></NutTextarea>
```

```ts
// 省略isWeb editTextAreaRef声明
if (isWeb) {
  // h5 autofocus不生效
  nextTick(() => {
    editTextAreaRef.value.textareaRef.focus();
  });
}
```

# Taro - 支付宝小程序

## 使用[Antv/F2](https://f2-v4.antv.vision/zh/docs/tutorial/miniprogram)

### 前言

> 一开始我就有一个困惑，我用`taro` `vue3`去写支付宝小程序，那我究竟是看[如何在Vue中使用](https://f2-v4.antv.vision/zh/docs/tutorial/vue)还是[如何在小程序中使用](https://f2-v4.antv.vision/zh/docs/tutorial/miniprogram)的文档呢❓

随即我开始了踩坑之旅，通过实践发现`F2`的vue版本是针对web端的，因此当我在小程序跑起来的时候直接报了一个`node.parentNode`获取不到的错误

上面说的是`4.x`版本。但从一开始我并没有直接用`4.x`，因为`4.x`开始，`F2`都是以`jsx`的方式去使用组件；我在vue3使用`script setup`去编写代码，所以暂时无法编写`jsx`。还有一个很重要的是旧项目(微信小程序)那边的`F2`用的是[`3.x`](https://f2-v3.antv.vision/zh/docs/tutorial/getting-started)

这两个版本在使用方式上改变还是挺大的，3.x用起来没有那么的挑；也正如所愿，我成功的跑起了示例，不曾想用安卓机一看，人都麻了，出现了以下的情况

- 图表有时候满屏(我设置的宽度100%)，有时候又变成了4分之一小
- 使用`v-if`控制，有概率渲染不了，canvas节点在，就是没有渲染，使用`v-show`直接就不渲染了

上诉情况没有任何的报错，无从解决；因此我只能转向`4.x`继续踩坑

> p.s. `IOS`啥毛病都没有，动画还贼丝滑

### 支付宝小程序接入Antv/F2 4.x版本

> 正式开始

#### 1. 安装 F2 依赖

```bash
# 安装 F2 依赖
npm i @antv/f2 --save

# 安装f2-context
npm i @antv/f2-context --save
```

#### 2. 配置 jsx transform

> 如果项目已有 jsx 编译，可忽略此步骤

```bash
npm i @babel/plugin-transform-react-jsx -D
```

在`config/index.js`增加配置

```js
mini: {
  webpackChain(chain) {
    chain
      .merge({
      module: {
        rule: {
          F2: {
            test: /\.jsx$/,
            use: {
              babelLoader: {
                loader: require.resolve('babel-loader'),
                options: {
                  plugins: [
                    [
                      '@babel/plugin-transform-react-jsx',
                      {
                        runtime: 'automatic',
                        importSource: '@antv/f2',
                      },
                    ],
                  ],
                },
              },
            },
          },
        },
      },
    })
  }
}
```

#### 3.下载[f2-my的源码](https://github.com/antvis/F2/tree/master/packages/my/src)

点击上面的地址把src文件夹的内容下载到本地，假设放到了**项目的**`/src/components/F2Chart`，**下面将以这个路径举例**

这也是为什么我在第一步的时候没有说安装`@antv/f2-my`，这里有两个坑，我们需要改一下源码

位置: `/src/components/F2Chart/index.js`

- 从[文档](https://f2-v4.antv.vision/zh/docs/tutorial/miniprogram)得知，我们等下在使用组件的时候需要传递`onRender`这个props，但是在实践过程中，通过这个props名称我无法把我们自己的函数传递过去，我尝试新加一个props，从而解决了问题

  ```js
  Component({
    props: {
      onRender: function onRender(_props) {},
      // 上面的onrender无法执行到，因此写了一个新的自定义prop
      render: function onRender(_props) {},
      // width height 会作为元素兜底的宽高使用
      width: null,
      height: null,
      type: '2d', // canvas 2d, 基础库 2.7 以上支持
    },
  })
  ```

  首先在props选项中增加了`render`，接着把原来调用`props.onRender`的地方改为`props.render`;

  分别是在`didUpdate`和`canvasRender`这两个地方

- 还有就是他的`click`函数，从源码可以看到是点击`canvas`时触发的，但是一点击控制台就收到一个报错，说事件的`Event`的`detail`是`undefined`，我打印了整个`Event`对象，确实如此，连同源码中的`target.offsetLeft`也是没有的，我怀疑这部分是复制了`web`的过来然后没改；因此我的`click`函数改成了如下，我暂时用不到

  ```js
  click: function click(e) {
    // 支付宝的点击event对象没有detail，这里只能改造一下不执行了
    var canvasEl = this.canvasEl;
    if (!canvasEl) {
      return;
    }
    var event = wrapEvent(e);
    canvasEl.dispatchEvent('click', event);
  }
  ```

  > 补充一下：在真机上有源码需要的`e.detail.x`，但是`e.target.offsetLeft`还是没有

#### 4.编写我们的业务代码

> 下面举例写一个官方的上手例子

和官方一样，先写一个`Chart.jsx`

```jsx
import { Chart, Interval, Axis } from '@antv/f2';

export default (props) => {
  const { data } = props;
  return (
    <Chart data={data}>
      <Axis field="genre" />
      <Axis field="sold" />
      <Interval x="genre" y="sold" color="genre" />
    </Chart>
  );
};

```

接着我们写一个taro的vue页面，`F2.vue`

```vue
<template>
  <view>
    <button @click="show = !show">change</button>
    <view class="container" v-show="show">
      <gh-canvas :render="onRenderChart"></gh-canvas>
    </view>
  </view>
</template>

<script lang="tsx">
  import { reactive, toRefs } from 'vue';
  import Chart from './Chart';
  import { createElement } from '@antv/f2';

  definePageConfig({
    usingComponents: {
      'gh-canvas': '../../components/F2Chart',
    },
  });

  export default {
    setup() {
      const state = reactive({
        show: true,
      });
      const data = [
        { genre: 'Sports', sold: 275 },
        { genre: 'Strategy', sold: 115 },
        { genre: 'Action', sold: 120 },
        { genre: 'Shooter', sold: 350 },
        { genre: 'Other', sold: 150 },
      ];
      function onRenderChart() {
        return createElement(Chart, {
          data: data,
        });
      }
      return {
        ...toRefs(state),
        onRenderChart,
      };
    },
  };
</script>

<style lang="scss">
  .container {
    width: 100%;
    height: 600px;
  }
</style>

```

  这里有个注意点，在页面先注册一下我们下载到本地的`antv/F2`组件；这个时候如果你直接跑起来你会收到一个警告，虽然是个警告，但是你的代码是不行的。

` [Vue warn]: Failed to resolve component: gh-canvas
If this is a native custom element, make sure to exclude it from component resolution via compilerOptions.isCustomElement. `

 这个提示很明显了，我们自己取的这个组件名，gh-canvas，在构建的时候把他当做vue组件去解析了；解决办法就是配置一下`vue-loader`让他跳过我们的组件

但是吧，又不能在`webpackchain`中直接配置覆盖，于是我一顿找，终于找到了一个老哥的贡献

https://github.com/NervJS/taro/pull/11694

接下来就简单了，让我们再次来到**config/index.js**增加以下配置

```js
plugins: [
  // 你的其他插件
  // https://github.com/NervJS/taro/pull/11694
  [
    '@tarojs/plugin-framework-vue3',
    {
      vueLoaderOption: {
        compilerOptions: {
          isCustomElement: (tag) => tag.startsWith('gh-'),
        },
      },
    },
  ],
],
```

这个组件名前缀你喜欢改什么就改什么，记得在vue的template的引用同步更改就行

重新运行`pnpm dev:alipay`，大功告成~🎉 4.x版本在安卓上已经没有上面提到的两个问题了

### 总结

这里踩坑主要还是配置问题，因为我不熟悉`webpack`、`webpackchain`因此废了很大劲。还有找资料还是优先到`github`查找一下有没有类似的`issue`或者贡献，什么chatGPT没啥用，不停的说谎给出错误答案











































