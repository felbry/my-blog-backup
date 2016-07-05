title: nodejs学习手记
date: 2016-05-21 09:48:33
comments: true
categories: TECH NOTE
tags:
- nodejs
---
* 调试工具
* 监听文件变化
* Express.js是如何工作的
* Mocha框架
* Jade语法和特性

<!-- more -->

## 调试工具
* 核心Node.js调试
* Node inspector
* Webstorm和其他集成开发环境

### 核心Node.js调试
**前提：**在程序中手动加入 debugger; 断点
**启动调试：**node debug filename
#### 主要调试命令
* next,n:单步执行
* cont,c:继续执行，直到遇到下一个断点
* step,s:单步执行并进入函数
* out,o:从函数中跳出
* watch(expression):把表达式expression加入监视列表

{% asset_img hello-debug.png %}

### Node inspector调试
**全局安装：**npm install -g node-inspector
**启动：**node-inspector
#### 调试步骤
1.在命令行新窗口使用 node --debug/--debug-brk filename
2.Chrome中访问localhost:8080/debug?port=5858
3.浏览器新窗口访问localhost:1994(文件中指定的端口),然后调试断点
  
{% asset_img ins-1.png %}
  
{% asset_img ins-2.png %}

## 监听文件变化
**Node.js应用是存储在内存中的，修改源码就要重启进程。实现重启进程的自动化，即可用通过Node.js的fs模块的方法进行监听，内容变化就重启服务。**

### 常用工具
* forever
* node-dev
* nodemon
* supervisor

### 安装运行
1.全局安装：npm install -g node-dev
2.执行node脚本：node-dev filename

## Express.js是如何工作的
Express.js是单入口的主文件启动。在这个主文件中，要做以下几件事：
1.引入依赖
2.设置相关配置
3.连接数据库（可选）
4.定义中间件
5.定义路由
6.开启服务/启动应用
7.以模块形式输出应用（可选）

## Mocha框架
### TDD和BDD 测试驱动开发和行为驱动开发
#### TDD主要思想
* 定义一个单元测试
* 执行这个单元测试
* 验证这个测试是否通过

**BDD是TDD的一个专业版本，它制定了从业务需求的角度出发需要哪些单元测试。**

### Mocha的hook（钩子）机制
hook可以理解为是一些逻辑，通常表现为 一个函数 或者 一些声明，当特定事件触发时hook才执行。
有前置hook和后置hook。

### 断言库（assert,Chai,Expect.js,Chai Expect.js）
assert库是Node.js核心的一部分，功能很少，但满足单元测试。

Chai是assert库的子集，相当于是对assert的封装，拥有更多的方法，但两者不一定百分百兼容。

Expect.js是一种BDD语言，链式语法风格。用于BDD。

Chai Expect.js对于Expect.js形同Chai对于assert。

**总结：Chai在一定程度上可以代替assert和Expect.js。**

## Jade语法和特性
### 标签
一行开头任何文本默认解释为HTML标签，标签后文本和空格被解析成内联HTML(元素的文本内容)

### 变量/数据
传给模板的数据称为**locals**，输出一个变量值，用等号=。

### 属性
紧跟标签，用括号括起来，多个属性之间用逗号分隔

    div(id="content", class="main")

有时属性值必须为动态，这时直接使用变量名字(可以理解为不加引号时为变量)

    div(id=itsname)

符号“|”允许在新一行写HTML节点内容，即有“|”那行会变成文本内容

    input
    | innerText

值为false的属性输出HTML代码时自动删除；未传值时自动赋值为true

### 字面量
ID为#，类为.
直接加在标签名后边，链式结构
没写标签名默认为div

### Script和Style块

    script.
        console.log('1');
        console.log('1');

### Javascript代码
* "-" 纯js代码
* "=" (转义) 或 != (不转义)，用于给标签(后)添加文本(本质上也是书写代码)

### 注释
* 显示 //
* 不显示 //-

### 过滤器
当有一个文本块需要用另一种语言写时，就会用到过滤器

### 读取变量
#(variableName)

    p My name is #(personName) ,and Nice to meet you.

### 函数mixin
* 声明：mixin name(param1, param2, ...)
* 用法：+name(data)

### include

    include ./example-include/base

include是一种自顶向下的方法。

a文件里，include了b文件，a文件先被处理，接着再处理b文件。所以可以在a文件里先定义locals数据，b文件就可以使用。

### entend
extend是一种自底向上的方法。
格式：extend filename和block filename

文件a继承文件b。现在文件b中定义block，然后在文件a中对应b中的block。

## 单独使用Jade
* 创建Jade文件
* 创建使用Jade文件的Node.js脚本

脚本使用jade.compile、jade.render、jade.renderFile方法均可。

在浏览器使用jade，用browserify和它的中间件jadeify；
在浏览器和服务器端使用相同jade模板，用Storify的jade-browser(Express.js的中间件)

