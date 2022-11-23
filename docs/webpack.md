<Banner />

## 项目中安装`webpack`

```sh
npm i webpack webpack-cli -D
```

## 项目中使用`webpack`

```sh
npx webpack
```

## npx 运行原理

**在项目的依赖包`node_modules`中的`bin`目录下执行`webpack.cmd`，然后判断如果不是全局安装的`webpack`在执行依赖包下的`webpack`的`bin`的`webpack.js`**

![image-20191220103028428](https://raw.githubusercontent.com/GauharChan/Picture-bed/main/img/image-20191220103028428.png)

![image-20191220102628023](https://raw.githubusercontent.com/GauharChan/Picture-bed/main/img/image-20191220102628023.png)

### 零配置打包

> 使用默认配置打包，必须遵循指定文件路径，文件名
>
> 默认打包项目中`src`目录下的`index.js`，输出`dist`目录，`dist`中有`main.js`

## 配置

### webpack.config.js

> 或者叫 webpackfile.js

在`node_modules`下的`webpack-cli`下的`bin`，`config`的`config-yargs.js`中的` .options``config `中的`defaultDescription`定义的名字

1. 入口(entry)

2. 输出(output)
3. loader
4. 插件(plugins) // 配置中带 s 的，都是数组

```js
const path = require("path");
const htmlPlugin = requiire("html-webpack-plugin");

module.exports = {
  // mode: 1.development 开发模式 代码不压缩，   2.production(默认) 生产模式(上线) 代码压缩  3.none 去除默认，空
  mode: "production",
  entry: "./src/index.js",
  output: {
    // path.resolve() 解析当前相对路径为绝对路径
    // path: path.resolve(__dirname, 'public'),
    // path.join() 拼接路径
    path: path.join(__dirname, "dist"), // 必须是绝对路径
    filename: "main.js", // 输出文件名字
  },
  // watch: true  // 开启监测模式  相当于--watch
  devServer: {
    // webpack-dev-server配置
    open: true,
    port: 5000,
    compress: true, // 压缩
    // contentBase: './src',
    hot: true,
  },
  plugins: [
    new htmlPlugin({
      filename: "index.html", // 定义生成文件
      template: "./src/index.html", // 模板文件
    }),
  ],
};
```

平时开发中，有可能有多个配置文件(开发，上线)，可以通过`--config + 文件名`调用不同的文件，建议写在`package.json`中。

```json
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "build": "webpack",  // 这里不用加npx npm本身就会去执行node_modules下的文件
  "d-build": "webpack --config webpack.development.js"
},
```

对应的执行不同的命令

```sh
npm run build

npm run d-build
```

### 自动编译

1. webpack's Watch Mode
2. webpack-dev-server
3. webpack-dev-middleware

#### watch

> 自动检测文件，打包，网页中需要手动刷新

以`cli`的方式设置`watch`参数

```json
"watch": "webpack --watch"
```

在`webpack.config.js`中设置`watch`属性为`true`也是同样的效果

```js
watch: true;
```

#### webpack-dev-server

> 需要安装
>
> 开启一个本地`express`服务器，托管在项目的根目录，自动打包，刷新网页

```sh
npm i webpack-dev-server -D
```

运行，同样设置在`package.json` 文件中，`--port` 设置端口 `--hot`热更替，更新修改部分

```json
"dev": "webpack-dev-server --hot --open --port 1234"
```

**注意：**webpack-dev-server 执行后，文件生成在内存中，需要引用服务器的文件地址，所以需要更改`index.html`中引用 js 的路径。如：`/main.js`，注意是**根目录**.

```html
<script src="/main.js"></script>
```

指定访问目录，有时候，我们的 index.html 不在根目录下

```json
"dev": "webpack-dev-server --open --contentBase ./src"
```

#### html 插件

`npm i html-webpack-plugin -D`

1. devServer 时根据模板在 express 项目根目录下生成 html 文件(类似于 devServer 生成内存中的 main.js)
2. devServer 时自动引入 main.js
3. 打包时会自动生成 index.html

### loader

#### css

在输入文件`main.js`中`import`引入`css`文件，报错提示需要`loader`

下载 loader，-D 开发的时候用

```shell
npm i css-loader style-loader -D
```

在配置文件`webpack.config.js`中加入配置代码

```js
module: {
  rules: [
    {
      // 处理css文件
      test: /\.css$/,
      // webpack 读取loader的时候，从右到左
      // loader执行顺序是从右到左管道方式链式调用
      // css-loader：解析css文件
      // style-loader：将解析的结果写入html中，使其生效
      use: ["style-loader", "css-loader"],
    },
  ];
}
```

#### less

下载处理`less`对应的`loader`

```shell
npm i less less-loader -D
```

配置文件，在`rules`中添加

```js
{
  test: /\.less$/,
  // 依赖'style-loader','css-loader'这两个loader
  use: ['style-loader','css-loader','less-loader']
}
```

#### sass

下载处理`sass`对应的`loader`

```shell
npm i node-sass sass-loader -D
```

配置文件，在`rules`中添加

```js
{
  test: /\.s(a|c)ss$/,
    // 依赖'style-loader','css-loader'这两个loader
    use: ['style-loader','css-loader','sass-loader']
},
```

#### 图片

下载对应的`loader`

```shell
npm i file-loader -D
```

配置文件，在`rules`中添加

```js
{
  // 图片
  test: /\.(jpg|png|gif|jpeg|bmp)$/,
    use: 'file-loader'
},
```

#### 字体图标

在`main.js`引入 css

```js
// 引入bootstrap
import "bootstrap/dist/css/bootstrap.css";
```

配置文件，在`rules`中添加

```js
{
  // 字体图标
  test: /\.(ttf|woff2|woff|eot|svg)$/,
  use: 'file-loader'
},
```

html

```html
<div>
  <span class="glyphicon glyphicon-heart"></span>
</div>
```

##### url-loader

> 更高级的 loader，封装了`file-loader`所以需要下载`file-loader`一起使用

```shell
npm i url-loader -D
```

可以设置，图片小于多少的时候转换为 base64

limit 一般会控制在 5kb，因为图片越大，base64 占用越大，超过 5kb，使用 web 链接加载

```js
{
  // 图片
  test: /\.(jpg|png|gif|jpeg|bmp)$/,
    use: {
      loader:'url-loader',
        options:{
          limit: 22 * 1024   // 表示小于22k的图片，转换为base64地址
        }
    }
},
```

还也可以指定图片存放的目录, 名字

```js
{
  // 图片
  test: /\.(jpg|png|gif|jpeg|bmp)$/,
    use: {
      loader:'url-loader',
        options:{
          limit: 22 * 1024,   // 表示小于22k的图片，转换为base64地址
          outputPath: 'images', // 指定输出文件夹
          name: '[name]-[hash:4].[ext]', // 图片名字自定义格式： 保留源文件名字-哈希值的前4位.源文件后缀名
        }
    }
},
```

#### babel

安装`loader`

@babel/core : 核心包

@babel/preset-env : 预设语法包，env:包含大部分 es6

```shell
npm i babel-loader @babel/core @babel/preset-env -D
```

配置

```js
{
  // babel
  test: /\.js$/,
    use:{
      loader: 'babel-loader',
        options:{
          presets:['@babel/env'],
          plugins:['@babel/plugin-proposal-class-properties']  // 使用插件
        }
    },
      // 排除打包文件
      exclude:/node_moudles/
},
```

当书写的代码是比较新的规范的时候，会处于编译不了的错误，一般会有提示，下哪个包，在 babel 官网插件中可以找到更多

如提示：Add @babel/plugin-proposal-class-properties (https://git.io/vb4SL) to the 'plugins' section of your Babel config to enable transformation.

```shell
npm i @babel/plugin-proposal-class-properties -D
```

**使用 Generator 函数的时候，babel 打包后，使用 regeneratorRuntime 来实现，但 babel 并没有内置这个，所以需要下载插件**

```shell
npm install --save-dev @babel/plugin-transform-runtime
```

这个插件同时依赖[`@babel/runtime`](https://babeljs.io/docs/en/babel-runtime)，同时要下载

```shell
npm install --save @babel/runtime
```

官网更推荐使用`.babelrc`配置文件，每个工具应该有他自己独立的配置文件，`json格式`

```json
{
  "presets": ["@babel/env"],
  "plugins": [
    "@babel/plugin-proposal-class-properties"
    // "@babel/plugin-transform-runtime",
  ]
}
```

在使用 es6，es7 提供的对象，数组，字符串新增的方法的时候，因为 js 是动态语言，在代码中可以随时为对象添加方法。因此 babel 默认不会转换，需要下载`@babel/polyfill`

```shell
npm i @babel/polyfill -S
```

哪个文件需要，就在哪里引入

```js
import @babel/polyfill
```

### [souce-map](https://webpack.docschina.org/configuration/devtool/)

通过 webpack 的 devtool 选项，配置哪一种。

定义 souce-map，开发调试更方便，展示源代码，如：打印位置能准确定位

```js
devtool: "cheap-module-eval-source-map";
```
