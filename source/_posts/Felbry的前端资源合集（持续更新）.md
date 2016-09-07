title: Felbry的前端资源合集（持续更新）
date: 2016-08-23 10:40:26
comments: true
categories: TECH NOTE
tags:
- 前端资源
- 前端工具
- Atom编辑器
---
* 写在最前
* Atom编辑器

<!-- more -->

## 写在最前
> 学习前端到现在也有小半年了，从开始的HTML/CSS/JavaScript三大基础以及nodejs，到后来的JQuery/BootStrap/Express，还有什么乱七八糟的模板引擎，Sass预处理语言，Babel编译器，Webpack模块打包，gulp自动化工作流等等等等。
了解了很多，实践过也思考过。感觉前端无外乎三点：基础、工具和规范。
基础即三大语言，最新发布的ES6相比ES5显得更加规范一些；而工具则是为了提高开发效率而生，它包括一些实际的工具（如gulp，webpack，babel），还有一些可能称不上工具的伪工具（包和框架）；规范显得有些抽象，它存在于前面两项中的任何地方，前端之所以发展的这么火热就在于很多方面它都没有一个既定的规范，所以就有了许许多多的规范在建立和推翻再建立。当然这些看法只是一家之言，大家娱乐一下就好。欢迎不吝赐教。
这份总结的主要目的就是收纳我接触过的，使用过的工具和资源，将它们分门别类，渐渐的给自己一个明确的思路。即每当我要开始一个新项目，能够很快搭建出一套适合自己的规范工作流。次要目的一方面是为了梳理知识，毕竟很多不常用的琐碎的东西没必要一直记在脑子里；另一方面也是给一些新手朋友提供一个快速入门的参考，因为自己当初学习一个新知识时就是索引各种资料和摸索解决方案，我希望能将更多的困惑记录在这里，让大家少走弯路。
废话说到这里，**Let's explore the magic world.**

## Atom编辑器
> Atom编辑器的魅力就在于高度的**可定制性**和**可扩展性**，它的风格和Sublime Text基本一致。选择Atom的原因大概有两点，一是它开源，可定制性更强；二是因为它是github主导的项目，感觉应该更了解程序员的痛点。比如内置的git，以及command prompt就很好玩。期待未来它能有更好的发展。

**以下总结参考Atom官网的Flight Manual**
英文版(windows)：[Atom Flight Manual](http://flight-manual.atom.io/)
中文译版(mac)：[Atom飞行手册翻译](https://wizardforcel.gitbooks.io/atom-flight-manual-zh-cn/content/)

### 基础配置
> 基础配置包括Atom的行为（init script）以及编辑器的样式（stylesheet）和表现（config），目前只讨论如何去配置编辑器的表现。

**配置编辑器的表现有两种实现途径：**
一种是在Settings界面设置相关功能，主要是面向全局。如果要想编辑器根据不同语言有不同表现的话就要在Settings的package中检索对应的语言（比如language-javascript），进入特定语言的Settings界面进行设置。

另外一种（推荐）相对比较方便，即在config.cson中进行配置，以一段代码为例：
```
'*': # all languages unless overridden
  'editor':
    'softWrap': false
    'tabLength': 8

'.source.gfm': # markdown overrides
  'editor':
    'softWrap': true

'.source.ruby': # ruby overrides
  'editor':
    'tabLength': 2

'.source.python': # python overrides
  'editor':
    'tabLength': 4
```
**其中的``*``对应全局，``*``下的editor对应编辑器的全局表现，``.source.language``下的editor覆盖全局中的配置，从而实现自定义。**

具体可用的参数详见链接 [Basic Customization](http://flight-manual.atom.io/using-atom/sections/basic-customization/)

### 实用功能
#### 折叠代码（folding）
{% asset_img folding.png %}
#### 代码片段（snippets）
通过配置``snippets.cson``来自定义你的代码片段
```
# 单行代码段
'.source.js': #language-scope
  'script-label': #description
    'prefix': 's' #key
    'body': '<script src="${1:"example.js"}">$2</script>$3' #<script src="example.js"></script> 通过变量来控制tab切换位置

# 多行代码段
# 通过 """ 来控制
'.source.js':
  'if, else if, else':
    'prefix': 'ieie'
    'body': """
      if (${1:true}) {
        $2
      } else if (${3:false}) {
        $4
      } else {
        $5
      }
    """
```
#### 多面板（panes）
{% asset_img panes.png %}
#### 编辑和删除文本/查找和替换
详见链接：[Editing and Deleting Text](http://flight-manual.atom.io/using-atom/sections/editing-and-deleting-text/) / [Find and Replace](http://flight-manual.atom.io/using-atom/sections/find-and-replace/)

**常用快捷键索引**
``Ctrl+J``  将下一行拼接到当前行的末尾
``Ctrl+Up/Down``  上移或者下移当前行

``Ctrl+Click``  添加新的光标
``Ctrl+D``  选择文档中与当前所选的单词相同的下一个单词
``Alt+F3``  选择文档中与当前所选的单词相同的所有单词

``Ctrl+F``  在缓冲区中查找
``Ctrl+Shift+F``  在整个项目中查找

{% asset_img multiple-cursors.gif %}

#### markdown编辑
Atom预装了markdown-preview，提供了markdown文本编辑的实时预览功能，同时也提供了一些常用的代码片段，提升写作体验。再结合自定义snippets，可以扩展更多的代码片段来达到完美的写作体验。

**开启方式：**``Ctrl+Shift+M``或通过``Ctrl+Shift+P``然后输入关键字(markdown preview)
{% asset_img preview.png %}

### 插件（packages）
#### Emmet
