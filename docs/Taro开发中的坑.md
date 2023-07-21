# Taro+Nut UI开发中的坑

| 依赖  | 版本  |
| ----- | ----- |
| taro  | 3.6.8 |
| vue   | 3.3.4 |
| nutui | 4.0.4 |

## 音频实例

### 问题1

createInnerAudioContext 报错 Cannot read property 'then' of undefined

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

### 问题2

在**web**中调用音频实例的销毁方法报错

```ts
const audioContext = Taro.createInnerAudioContext()
audioContext.destroy() // 报错
```

![企业微信截图_6a92ba3f-b255-40ef-a5d2-4c25a9be404f](https://raw.githubusercontent.com/GauharChan/Picture-bed/main/img/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_6a92ba3f-b255-40ef-a5d2-4c25a9be404f.png)

我理解的报错原因是

web实际上就是创建一个`audio`元素，但是Taro压根就没有插入body中，destroy却调用了document.body.removeChild，所以就报错了

![企业微信截图_583385e3-2b1b-4109-985c-57698253ab32](https://raw.githubusercontent.com/GauharChan/Picture-bed/main/img/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_583385e3-2b1b-4109-985c-57698253ab32.png)

#### 解决结果

> 更新一下：针对这个问题我提了一个[PR](https://github.com/NervJS/taro/pull/14130)，已被`Merged`

web不调用`destroy`方法，手动把实例设置为`null || undefined`

## 上传文件

### web

> 微信小程序没有问题

### 问题

- 上传文件的接口要求form-data的格式传递参数
- 文件名称服务端无法从我们传递的`file对象`解析到

#### 解决结果

**第一个问题**

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

**第二个问题**

> 文件名称服务端无法从我们传递的`file对象`解析到

**web端获取到的file对象和微信小程序的不一致**，估计是Taro处理过。打印web端的file对象可以发现，它上面还有一个属性名为`originalFileObj`

这个`originalFileObj`原始对象就有文件的名称等信息

所以我的解决方案是在`originalFileObj`获取文件名，并在 `formData`中传多一个`fileName`(和服务端约定好的)字段给接口

## 行内样式

如果涉及到单位，一定要使用`Taro.pxTransform`，不然taro会直接把相关的样式代码直接删除

## definePageConfig不生效？

### 问题

**taro版本高于3.4**，页面用`definePageConfig`修改导航栏文字等，运行后没有生效

### 解决结果

> [参考文档](https://docs.taro.zone/docs/page-config/#%E5%9C%A8%E9%A1%B5%E9%9D%A2%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E4%B8%AD%E4%BD%BF%E7%94%A8)

这个问题产生的原因非常的凑巧，假设我现有一个分包`packageMy`

- 我有一个Index.vue，页面路径自然就是`packageMy/Index`
- 同时我有`packageMy/index.ts`处理了其他的事情

所以这个路径和文件重名了，因为taro优先取`路径名.ts（官网描述的是叫page.config.js）`作为页面的配置，因为`definePageConfig`是v3.4后面才有的

# Nut UI

## textarea

### 问题

`autofocus`属性`web`端不生效

### 解决结果

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



