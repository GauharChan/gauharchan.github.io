---
title: utils
date: 2020-03-18 12:13:03
tags: JavaScript
categories: JavaScript
---

<Banner />

![](https://s2.ax1x.com/2019/12/04/QQH51e.png)

## [源文件](http://person.gauhar.top/util.js)

## 根据屏幕宽度设置根标签字体大小

```js
/**
 * 根据屏幕宽度设置根标签字体大小
 */
function remInit() {
  document.addEventListener("DOMContentLoaded", function () {
    let html = document.querySelector("html");
    let fontSize = window.innerWidth / 23; // 屏幕的23分之一
    fontSize = fontSize > 50 ? "50px" : fontSize + "px";
    html.style.fontSize = fontSize;
  });
}
```

## 将 url 携带的数据，转换为对象

```js
/**
 * 将url携带的数据，转换为对象
 * @param {String} url url路径
 */
function parseParams(url) {
  let search = new URL(url).search;
  let arr = search.replace("?", "").split("&"); // 把？去掉，切割为数组
  let obj = {};
  arr.forEach((e) => {
    let list = e.split("=");
    obj[list[0]] = list[1];
  });
  return obj;
}
```

## 字符串去除空格

```js
/**
 * 字符串去除空格
 * @param {String} str 被处理的字符串
 */
function noSpace(str) {
  return str.replace(/\s+/g, "");
}
```

## 判断对象有无指定属性

```js
/**
 * 判断对象有无指定属性
 * @param {Object} obj 判断的对象
 * @param {String} key 判断的属性名(键名)
 */
function hasKey(obj, key) {
  let status = obj.hasOwnProperty(key) && key in obj ? true : false;
  return status;
}
```

## js 时间戳转换格式

> `filterTime`函数的第二个和第三个参数可以省略

```js
/**
 *
 * @param {*} date "转换的日期"
 * @param {string} [spare] "年月日数间隔符"
 * @param {string} [timeSpare] "时分秒间隔符"
 */

function filterTime(date, spare = "-", timeSpare = ":") {
  // 补零
  function fix(i) {
    typeof i !== "number" ? (i = parseInt(i)) : null;
    return i < 10 ? `0${i}` : i;
  }
  // 生成时间
  function time(t, spare = "-", timeSpare = ":") {
    let [y, m, d, h, mi, ms] = [
      new Date(t).getFullYear(),
      new Date(t).getMonth() + 1,
      new Date(t).getDate(),
      new Date(t).getHours(),
      new Date(t).getMinutes(),
      // new Date(t).getMilliseconds().toString().substr(0, 2)  毫秒
      new Date(t).getSeconds(),
    ];
    return `${y}${spare}${fix(m)}${spare}${fix(d)}${fix(h)}${timeSpare}${fix(
      mi
    )}${timeSpare}${fix(ms)}`;
  }
  // 获取长度
  let len = null;
  if (typeof date === "number") {
    date = parseInt(date);
    len = date.toString().length;
  } else {
    len = date.length;
    len == 13 ? (date = parseInt(date)) : null;
  }
  if (len === 10) {
    return time(date * 1000, spare, timeSpare);
  } else if (len === 13) {
    return time(date, spare, timeSpare);
  } else {
    return time(date, spare, timeSpare);
  }
}
```

## 深拷贝

```JS
/**
 * 深拷贝
 * @param {Object} target "要深拷贝的对象"
 * @param {Object} newObj "拷贝后的一个新的对象"
 */
function deepClone(target,newObj){
    for(const key in target){
        let temp = target[key]
        if(Object.prototype.toString.call(temp) === "[object,Array]"){
            newObj[key] = []
            deepClone(temp,newObj)
        }else if(Object.prototype.toString.call(temp) === "[object,Object]"){
            newObj[key] = {}
            deepClone(temp,newObj)
        }else{
            newObj[key] = temp
        }
    }
}
```

## 判断手机系统

```js
/**
 * @return {Boolean} true是ios, false是安卓
 */
function checkPhone() {
  if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
    return true;
  } else if (/(Android)/i.test(navigator.userAgent)) {
    return false;
  }
}
```

## 倒计时

```js
/**
 * 
 * @param {Object} obj 
 * @param {Function} callback 
 * @author gauhar
    c 秒数
    c1 分钟
    c2 毫秒数
    stop 倒计时状态，是否停止
    callback 倒计时完成后的回调,
    sec 渲染秒数的元素节点
    min 渲染分钟的元素节点
    timer 定义一个全局变量名记录定时器
 */
function timedCount(obj, callback) {
  let { c, c1, c2, stop, sec, min, timer } = { ...obj };
  if (c < 10) {
    $(sec).text("0" + c); // 秒 补0
  } else {
    $(sec).text(c);
  }
  if (c1 < 10) {
    $(min).text("0" + c1);
  } else {
    $(min).text(c1); // 分 60
  }
  // document.getElementById('txt2').value = c2; //毫秒 0
  c2 = c2 - 5;
  if (c2 < 0) {
    c = c - 1;
    c2 = c2 + 100;
  }
  if (c < 0) {
    c1 = c1 - 1;
    c = c + 60;
  }
  if (c1 == 0 && c == 0) {
    stop = 1;
    callback();
    clearTimeout(window[timer]);
  }
  if (stop == 0) {
    let obj = { c, c1, c2, stop, sec, min, timer };
    window[timer] = setTimeout(() => {
      timedCount(obj, callback);
    }, 50);
  } else return;
}
```
