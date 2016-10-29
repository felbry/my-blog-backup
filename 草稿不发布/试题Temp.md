title: 试题Temp
date: 2016-10-22 19:43:50
tags:
---
# Lonlife前端开发试题
**姓名：柴方博             联系方式：13290967808**

<!-- more -->

### 1.如何实现多标签页之间的通讯

> Demo：[felbry.leanapp.cn/pages-msg.html](http://felbry.leanapp.cn/pages-msg.html)

#### opener属性
```
//父窗口中获得子窗口引用
var win = window.open(url, ...);

//子窗口中获得父窗口的引用
window.opener
```

#### postMessage() （可跨域）
```
//向指定窗口发送信息
theWindow.postMessage(msg, targetOrigin)

//窗口监听消息事件接收信息
window.addEventListener('message', function (e) {
  console.log(e.data);
})
```

#### 还可将消息存储在cookie或localStorage中，通过相关API操作

### 2.简述什么是按需加载，然后实现一个图片按需加载的js

> Demo: [felbry.leanapp.cn/lazy-load.html](http://felbry.leanapp.cn/lazy-load.html)

#### 按需加载
顾名思义即为根据需要加载。常用在大型网页中（如淘宝首页），减少某个时段大量的http请求。将一些资源请求暂时搁置，等必要时再发起请求，从而优化了首次加载速度。

#### 图片懒加载
* 指定一张默认的图片，首次加载只请求这一张
* 将每张图片的src属性值以data-src属性存储
* 对window进行滚动事件绑定，即到达图片的Y轴位置时请求图片资源

```
var imgArr = Array.prototype.slice.apply(document.getElementsByTagName('img'));
window.addEventListener('scroll', function () {
  if(imgArr.length != 0) {
    for(var i = 0; i < imgArr.length; i++) {
      if(imgArr[i].offsetTop <= (document.documentElement.scrollTop || document.body.scrollTop) + imgArr[i].getAttribute('height')/1) {
        imgArr[i].setAttribute('src', imgArr[i].getAttribute('data-src'));
        imgArr.splice(i, 1);
        i -= 1;
      }
    }
  }
})
```

### 简述下闭包，有什么好处和坏处
一句话形容，闭包就是能够读取其他函数内部变量的函数。主要是为了解决ES5之前JavaScript没有局部作用域概念的问题，利用闭包可以实现伪模块化。

#### 好处
如上所述，可以实现模块化思想，避免全局作用变量污染

#### 坏处
1.由于将函数内部变量暴漏出去，在全局作用域中会存在对函数内部变量的引用，导致变量内存不能被垃圾回收，从而造成内存泄漏
2.暴漏内部变量，存在数据被随意写入的风险

### 什么是模块开发，怎么实现一个模块开发
软件设计有一个思想叫“高内聚，低耦合”，感觉形容模块开发再合适不过了。用拼图来举个不恰当的例子：模块就相当于是那些拼图碎片，我们只需准备好所有的碎片，接下来开发app只是拼装的问题。当然。好的模块应该也具备良好的复用性。前端开发中对模块有AMD，CommonJs等规范，新的ES6标准也有模块的相关定义。

```
//es6模块规范

//circle.js
function area (radius) {
  return Math.PI * radius * radius;
}

function circumference (radius) {
  return 2 * Math.PI * radius;
}

function others (args) {

}

export {area, circumference};

//main.js
import {area, circumference} from './circle';

console.log('圆面积：' + area(4));
console.log('圆周长：' + circumference(14));
```

### 实现一个多列的等高布局

> Demo：[felbry.leanapp.cn/columns.html](http://felbry.leanapp.cn/columns.html)

```
<style>
  .father {
    height: 100vh;
    display: flex;
  }

  .child {
    outline: 1px solid green;
    flex-grow: 1;
  }
</style>

<body>
  <div class="father">
    <div class="child">1</div>
    <div class="child">2</div>
    <div class="child">3</div>
    <div class="child">4</div>
  </div>
</body>
```

### zoom是什么，有什么用？
zoom是一个非标准的css属性，用来调整内容的大小，属性可以为百分比，数字。原本只有ie支持，不过发展到现在多数浏览器也支持了这一属性，考虑到兼容性问题，可以用css3的transform来替代。以下是zoom现今的支持情况。
{% asset_img zoom.png %}

### 怎么优化一个首屏的加载速度，可以从js和css等多方面考虑
个人目前水平对优化加载速度的理解是可以从两大方面着手：资源请求的数量优化和资源的大小优化。
因为每一个静态文件的加载都要发起一个http请求，而http请求越多，相对耗时就会越长。减少资源请求一是可以对资源进行打包，比如雪碧图，webpack打包工具将多个js文件打包等；二是可以按需加载，比如先加载首屏第一页内容，其他内容可以根据用户的相应操作来触发事件。
资源大小优化上面，首先可以对代码文件进行相应的压缩，再者是对图片类型进行压缩（通过在线工具，或者是将图片转格式，如webp，也可以是font类型）等。
最后还得考虑下网页的加载原理，比如dom的加载，css和js外链文件加载。可以采取一些优化HTML DOM结构，js文件放置在最后等手段适当调整。

### 简述下new一个对象后，this的变化
```
new Date();

//1.创建一个空的obj对象，即Object的一个实例
//2.把这个空对象obj绑定到函数（Date）的上下文环境中，相当于把this指向了obj
//3.执行函数，这个过程就把函数中的属性、方法拷贝到了obj中
```

### 怎么实现一个对象的继承
```
var o = {
  say: function (name) {
    console.log('My name is ' + name);
  }
}

function Stu () {}
Stu.prototype = o;

var s = new Stu();
s.say('Felbry');
```

### 简述下平时开发用到的技术栈
前端方面：一般是用sass写css，jquery写js，gulp结合一些插件实现sass与css的转换、监测文件变动浏览器自动刷新。bootstrap用得较少，感觉它更满足基于设计稿定制并快速开发的需求。
后端方面：比较熟悉原生JSP以及struts2框架，简单了解过node.js和express框架，实现过一些小demo。
前端框架：在react和vue中选择了vue来学习，目前在学习vue2，相关实践较少。
