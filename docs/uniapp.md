<Banner />
## 目录结构

- components：组件(插件)，easycom 自动根据这个文件夹自动引入组件，在 vue 文件的`template`直接使用，无需引入，注册。(可自行修改文件，做出自己想要的组件，都是 vue 文件)

- common：公共文件，css，less，scss，font 字体图标等文件可放在此文件夹再对应的引入使用。

- pages： 页面文件

- static：静态资源并且只能存放在此文件夹，视频，图片等

- wxcomponents：微信小程序组件(插件)

- main.js ：Vue 初始化入口文件

- App.vue：应用配置，用来配置 App 全局样式以及监听 [应用生命周期](https://uniapp.dcloud.io/frame?id=应用生命周期)

- manifest.json：配置应用名称、appid、logo、版本等打包信息，[详见](https://uniapp.dcloud.io/collocation/manifest)

- pages.json ：配置页面路由、导航条、选项卡等页面类信息，[详见](https://uniapp.dcloud.io/collocation/pages)使用微信小程序的组件时例如：`vant-weapp`，可以在`globalStyle`配置`usingComponents`全局引用

  ```json
  "usingComponents": {
      "van-button": "/wxcomponents/vant-weapp/button/index"
  }
  ```

## tips

- css 文件不能引用网络地址，字体文件可以

- 支付宝小程序组件内 image 标签不可使用相对路径

- `@/`指向根目录，在 cli 项目中@指向 src 目录

- js 文件不支持使用`/`开头的方式引入

- 小程序不支持在 css 中使用本地文件，包括本地的背景图和字体文件

- 非 H5 端不支持`classObject` 和 `styleObject` 语法。

  ```js
  classObject: {
      active: true,
      'text-danger': false
  }
  ```

- 非 H5 端**组件**不可以用`class`和`style`绑定

- 使用字体图标，引入本地文件时，`~@/`表示根路径

  ```css
  @font-face {
    font-family: "iconfont";
    src: url("~@/static/iconfont.eot?t=1587009270769"); /* IE9 */
    src: url("~@/static/iconfont.eot?t=1587009270769#iefix") format("embedded-opentype"); /* IE6-IE8 */
  }
  ```

## 生命周期

### 应用生命周期

> 先触发 onLaunch 再触发 onShow
>
> **应用生命周期仅可在`App.vue`中监听，在其它页面监听无效。**

| 函数名   | 说明                                           |
| :------- | :--------------------------------------------- |
| onLaunch | 当`uni-app` 初始化完成时触发（全局只触发一次） |
| onShow   | 当 `uni-app` 启动，或从后台进入前台显示        |
| onHide   | 当 `uni-app` 从前台进入后台                    |
| onError  | 当 `uni-app` 报错时触发                        |

### 页面生命周期

| 函数名                              | 说明                                                         | 平台差异说明                                         |
| :---------------------------------- | :----------------------------------------------------------- | :--------------------------------------------------- |
| onLoad                              | 监听页面加载，其参数为上个页面传递的数据，参数类型为 Object（用于页面传参），参考[示例](https://uniapp.dcloud.io/api/router?id=navigateto) |                                                      |
| onShow                              | 监听页面显示。页面每次出现在屏幕上都触发，包括从下级页面点返回露出当前页面 |                                                      |
| onReady                             | 监听页面初次渲染完成。注意如果渲染速度快，会在页面进入动画完成前触发 |                                                      |
| onHide                              | 监听页面隐藏                                                 |                                                      |
| onUnload                            | 监听页面卸载                                                 |                                                      |
| onResize                            | 监听窗口尺寸变化                                             | App、微信小程序                                      |
| onPullDownRefresh                   | 监听用户下拉动作，一般用于下拉刷新，参考[示例](https://uniapp.dcloud.io/api/ui/pulldown) |                                                      |
| onReachBottom                       | 页面上拉触底事件的处理函数                                   |                                                      |
| onTabItemTap                        | 点击 tab 时触发，参数为 Object，具体见下方注意事项           | 微信小程序、百度小程序、H5、App（自定义组件模式）    |
| onShareAppMessage                   | 用户点击右上角分享                                           | 微信小程序、百度小程序、字节跳动小程序、支付宝小程序 |
| onPageScroll                        | 监听页面滚动，参数为 Object                                  |                                                      |
| onNavigationBarButtonTap            | 监听原生标题栏按钮点击事件，参数为 Object                    | 5+ App、H5                                           |
| onBackPress                         | 监听页面返回，返回 event = {from:backbutton、 navigateBack} ，backbutton 表示来源是左上角返回按钮或 android 返回键；navigateBack 表示来源是 uni.navigateBack ；详细说明及使用：[onBackPress 详解](http://ask.dcloud.net.cn/article/35120) | App、H5                                              |
| onNavigationBarSearchInputChanged   | 监听原生标题栏搜索输入框输入内容变化事件                     | App、H5                                              |
| onNavigationBarSearchInputConfirmed | 监听原生标题栏搜索输入框搜索事件，用户点击软键盘上的“搜索”按钮时触发。 | App、H5                                              |
| onNavigationBarSearchInputClicked   | 监听原生标题栏搜索输入框点击事件                             | App、H5                                              |

## 使用 vuex

> **这里可以使用`uni`对象**，像提示登录，可以写在`store`，然后页面随意调用

根目录下新建`store`文件夹，新建一个 js 文件

```js
import Vue from "vue";
import Vuex from "vuex";

Vue.use(Vuex);

export default new Vuex.Store({
  state: {
    name: "gauhar",
  },
  mutations: {
    setName(state, value) {
      state.name = value;
    },
  },
  actions: {
    callName({ commit }, value) {
      commit("setName", value);
    },
    // 提示登录的弹窗
    login({ commit }, msg) {
      console.log(msg);
      console.log(uni.showModal);
      uni.showModal({
        title: "提示",
        content: msg,
        success: function (res) {
          if (res.confirm) {
            uni.switchTab({
              url: "/pages/my/my",
            });
          } else if (res.cancel) {
            uni.navigateBack({
              delta: 1,
            });
          }
        },
      });
    },
  },
});
```

在`main.js`中引入 store，并挂载到实例上，方便使用

```js
import Vue from "vue";
import App from "./App";
import store from "./store/index";
Vue.config.productionTip = false;

App.mpType = "app";

const app = new Vue({
  store,
  ...App,
});
app.$mount();
```

通过`this.$store`调用

```vue
<script>
export default {
  data() {
    return {
      href: "https://uniapp.dcloud.io/component/README?id=uniui",
    };
  },
  computed: {
    msg() {
      return this.$store.state.name;
    },
  },
  methods: {
    open() {
      this.$refs.popup.open();
    },
    handleTimeUp() {
      this.$store.dispatch("callName", "时间到");
      this.$refs.popup.open();
    },
  },
  onShow() {},
};
</script>
```

## globalData

> 全局变量，如果不用 vuex 的话，这个还是挺不错的

在 App.vue 中定义

```vue
<script>
export default {
  globalData: {
    text: "text",
  },
  onLaunch: function () {
    console.log("App Launch");
  },
  onShow: function () {
    console.log("App Show");
  },
  onHide: function () {
    console.log("App Hide");
  },
};
</script>

<style>
/*每个页面公共css */
</style>
```

### 取值和赋值

赋值：`getApp().globalData.text = 'test'`

取值：`console.log(getApp().globalData.text) // 'test'`

## 分包

在 page.json 添加规则

```json
"subPackages": [{
  "root": "pagesA",  // 分包根目录
  "pages": [  // 配置页面，path,style
    {
      "path": "list/list"
    }
  ]
}],
"preloadRule": {   // 分包下载规则
  "pagesA/list/list": {  // 包路径
    "network": "all",  // wifi 还是全部
    "packages": ["__APP__"]  // 表示主包
  },
  "pages/index/index": {
    "network": "all",
    "packages": ["pagesA"]
  }
}
```

## 自定义事件

### uni.$emit(eventName, object)

> 触发
>
> 注意第二个参数是对象

```js
uni.$emit("update", { msg: "页面更新" });
```

### uni.$on(eventName, callback)

> 监听

```js
uni.$on("update", function (data) {
  console.log("监听到事件来自 update ，携带参数 msg 为：" + data.msg);
});
```

## api

### 获取用户定位

1. 在`manifest.json`中设置，desc 是获取权限时的提示语

   ```json
   "permission" : {
     "scope.userLocation" : {
       "desc" : "你的位置信息将用于小程序位置接口的效果展示"
     }
   }
   ```

2. `uni.authorize`获取权限，success 接收成功回调

3. `uni.getLocation`获取用户定位`type: "gcj02"`获取的经纬度可以用在`map`组件中

4. `uni.openLocation`使用应用内置地图查看位置。传入获取到的经纬度

   ```js
   let that = this;
   uni.authorize({
     scope: "scope.userLocation", // 对应permission的键名
     success() {
       uni.getLocation({
         type: "gcj02",
         // altitude: true,
         success: function (res) {
           that.longitude = res.longitude;
           that.latitude = res.latitude;
           console.log("当前位置的经度：" + res.longitude);
           console.log("当前位置的纬度：" + res.latitude);
           uni.openLocation({
             latitude: that.latitude,
             longitude: that.longitude,
           });
         },
       });
     },
   });
   ```

### 录音

#### 流程

1. 创建录音对象

   ```js
   const recorderManager = uni.getRecorderManager();
   ```

2. 页面加载完成，绑定**监听**录音结束事件，保存返回的临时文件路径

   ```js
   onLoad() {
     let that = this;
     recorderManager.onStop(function(res) {
       console.log(res);
       that.voicePath = res.tempFilePath;  // 保存返回的临时文件路径
     });
   },
   ```

3. 开始录音事件

   ```js
   startRecord() {
     console.log("开始录音");
     recorderManager.start({
       format: 'aac'  // 指定为aac格式，更加高效
     });
   }
   ```

4. **触发**结束录音事件

   ```js
   recorderManager.stop();
   ```

### 播放音频

> 音频的路径可以是微信临时文件路径，可以使网络地址

1. 创建播放**音频**对象

   ```js
   const innerAudioContext = uni.createInnerAudioContext();
   ```

2. 路径的赋值，一些设置

   ```js
   innerAudioContext.src =
     "http://gauhar.top/music/static/media/%E6%9E%97%E4%BF%8A%E6%9D%B0-%E9%9B%AA%E8%90%BD%E4%B8%8B%E7%9A%84%E5%A3%B0%E9%9F%B3.ff6502e.mp3"; // 设置路径
   innerAudioContext.volume = 0.7; // 设置音量
   ```

3. 调用播放方法

   ```js
   innerAudioContext.play();
   ```

## 组件

### swiper

> 轮播图组件

修改指示点

```css
.swiper /deep/ .wx-swiper-dot {
}
.swiper /deep/ .wx-swiper-dot-active {
} /* 修改当前的指示点 */

/* #ifdef MP-WEIXIN */
.swiper /deep/ .wx-swiper-dot-active {
  width: 30rpx;
  height: 10rpx;
  border-radius: 5rpx;
}
/* #endif */

/* #ifndef APP-VUE */
/deep/uni-swiper .uni-swiper-dot-active {
  width: 30rpx;
  height: 10rpx;
  border-radius: 5rpx;
}
/* #endif */
```

### rich-text

> 富文本渲染组件
>
> :node="node 数组或者字符串"。 app 端不支持字符串，并且字符串被使用的时候会被转换为数组，性能低

使用[htmlParser.js](https://github.com/dcloudio/hello-uniapp/blob/master/common/html-parser.js)将字符串转换为数组再使用。

```vue
<template>
  <view>
    <rich-text :nodes="nodeArr"></rich-text>
  </view>
</template>
<script>
import parseHtml from '../../common/htmlParser'
export default {
  data() {
    return {
      htmlString: '<div style="text-align:center;"><img src="https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/uni@2x.png"/></div>',
      parseHtml
    };
  },
  computed: {
    nodeArr(){
      return this.parseHtml(this.htmlString)
    }
  }
</script>
// 这是转换之后的数组
nodes="[{"name":"div","attrs":{"style":"text-align:center;"},"children":[{"name":"img","attrs":{"src":"https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/uni@2x.png"}}]}]"
```

## 坑

### 出现报错`Cannot read property 'call' of undefined`

通常出现在移动文件夹，修改 pages.json 的某些配置出现，一度怀疑自己代码写错，对，就要这样坑你！

> 解决方法：停止运行项目，把打开了这个项目文件夹的编辑器，微信开发者工具关闭。打开项目文件夹，删除 unpackage dist 目录，再重新启动服务。重新回到了美好的世界

### 使用 uni-ui 的同时使用自定义组件的时候

配置 easycom 的时候注意，从官网复制的例子，中间少了一层文件夹的名字

```json
"easycom": {
		"autoscan": true,
		"custom": {
			"uni-(.*)": "@/components/uni-$1/uni-$1.vue",
			"gh-(.*)": "@/components/gh-$1/gh-$1.vue"
		}
	}
```

### open-data

> 展示用户头像，很多情况下要求圆形

需要设置样式`display: block;` + `overflow: hidden;`

```css
.avatar {
  display: block;
  width: 100rpx;
  height: 100rpx;
  border-radius: 50%;
  overflow: hidden;
}
```

### css

#### border-radius

> 在小程序如果要设置其中的某个圆角，而不是四个角。比如只要 top 部分的左右两个圆角，底下的还是直角

**不能像 h5 那样直接写**

```css
div {
  border-radius: 20px 20px 0 0;
}
```

**必须单独设置**

```css
div {
  border-top-left-radius: 20rpx;
  border-top-right-radius: 20rpx;
}
```

### ref

内置的组件中不可以使用`this.$refs`获取，只能用于自定义组件
