title: 读《Javascript权威指南（6）》
date: 2016-07-07 22:32:19
comments: true
categories: TECH NOTE
tags:
- javascript
- 犀牛书
---
* 第3章：类型，值和变量
* 第4章：表达式和运算符
* 第5章：语句
* 第6章：对象
* 第7章：数组
* 第8章：函数
* 第9章：类和模块
* 第13章：web浏览器中的javascript
* 第14章：Window对象
* 第18章：脚本化http

<!-- more -->

## 第3章：类型，值和变量
### 文本
#### 字符串
相关常用方法：s.replace() s.slice() s.split() s.indexOf() s.substring()/substr() s.match() s.concat() s.endsWith()
#### 转义字符的使用
#### 正则表达式
创建：构造函数RegExp()或直接量 /../
使用：字符串具有接收正则参数的方法：match(),search(),replace(),split()
[正则表达式30分钟入门教程](http://www.jb51.net/tools/zhengze.html#howtouse)

### null和undefined
1.null是关键字，undefined是全局变量
2.可以认为undefined表示系统级的、出乎意料的或类似错误的值的空缺
  null是表示程序级的、正常的或在意料之中的值的空缺
3.null == undefined (true)
  null === undefined (false)
4.想将它们赋值给变量或属性，或将作为参数传入函数，最佳选择null

### 全局对象
1.全局属性
2.全局函数
3.构造函数
4.全局对象

### 包装对象
机制：当引用一个字符串s的属性时，会调用new String()转换为对象；一旦引用结束，这个临时对象就会被销毁。
```
var s = "i am felbry";
s.len = 7;
var t = s.len;  // undefined

var S = new String(s);
S.len = 8;
t = S.len;  // 8
```

### 不可变的原始值和可变的对象引用
#### 对象引用
对象是引用类型，区别于基本类型。对象值都是引用。所以对象的比较都是引用的比较。当且仅当都引用同一个基对象时，才相等。

    举一个不恰当的例子：
    一个基对象就相当于是一个祖源
    然后有一代二代三代的子孙（对象值：即引用）都在这条祖线上。一条祖线上的同属一个家族（即同一基对象的对象值都相等）
    这个祖源的发展构成一本唯一族谱。家族中的任一成员做了关乎祖上的荣辱之事都会影响族谱。（即族谱是可变的，任一成员都可更改）
    但是张姓和李姓有两个人长得一模一样，就像复制出来的，也不是同一祖源（即不同一基对象的对象值不相等）
    而这个祖源对于我们这些后代来说可能只是个传说，这个传说有些抽象。但我们总能从一个已知族人推知与其相关联的族人之间是相等

1.同一个基对象
```
var a = []; // a引用了一个空数组
var b = a;  // b引用同一个数组
b[0] = 1;
console.log(a); // [1]
console.log(b); // [1]
```

2.对象/数组 不可能相等（即使相同属性和值/索引元素完全相等）
```
var a = [], b = [];
console.log(a == b);    // false
console.log(a === b);   // false
var a1 = {}, b1 = {};
console.log(a1 == b1);  // false
console.log(a1 === b1); // false
```

3.在1中只是将引用赋给了b，如果想要得到一个对象或数组的副本(即复制一次)，则需要显式复制对象的每一个属性或数组的每个元素。

4.如若想要形如 a = [] 和 b = []相等，自定义一个比较函数，若两者元素都相等，返回true

### 类型转换
1.“==”运算符从不试图将其操作数转换为布尔值
```
"0" == false    // true
```

### 显式类型转换
1.Object(null/undefined)会返回一个新创建的空对象，若直接把null/undefined转换为对象，会抛错误
2.Number()类的toString()方法，可接收(可选的)转换基数
```
var n = 15;
var a,b,c;
a = n.toString(2);
b = n.toString(10);
c = n.toString(16);
console.log(a + ", " + b + ", " + c);   //1111, 15, f
```
3.处理财务或科学数据，控制小数点位置和有效数字位数
**Number类的toFixed(),toExponential(),toPrecision()**
4.全局函数 parseInt()解析整数 parseFloat()解析整数和浮点数
```
parseInt(.1)    //NaN
parseFloat(.1)  //0.1
parseInt()可接收第二个可选参数，指定转换基数
```

### 对象转换为原始值(boolean,string,number)
boolean:true
string,number: toString(),valueOf()
具体机制根据场景也有不同，待用时考究
#### Date类的toString和valueOf()方法
```
var d = new Date(2010, 0, 1);
console.log(d.toString());  //Fri Jan 01 2010 00:00:00 GMT+0800 (中国标准时间)
console.log(d.valueOf());   //1262275200000
```

### 函数作用域和声明提前
```
var scope = "global";
function(){
    console.log(scope); //undefined而不是global(局部变量已经覆盖了全局变量)
    var scope = "local";
    console.log(scope);
}
```

### 作为属性的变量
**var 声明的变量，无法通过delete运算符删除**

## 第4章：表达式和运算符
### 前增量和后增量
```
var i = 1;
j = i++;    //j=1,i=2
j = ++i;    //j=2,i=2
```

### 相等与不相等运算符
相等/严格相等  ==/===
不相等/不严格相等  !=/!==

### in 和 instanceof
字符串(或可转换为字符串) in 对象
对象 instanceof 对象的类

### 赋值操作符
赋值操作符的结合性是从右至左
```
i=j=k=0;    //把三个变量初始化为0
```

### eval
eval本是个函数，但常被当做运算符来对待
它可执行任意符合js语法的字符串（常丢进去一个可运行函数）
#### ES5 的直接调用和间接调用eval()
直接eval：在调用它的上下文作用域内执行
间接eval(起别名)：使用全局对象作为其上下文作用域

### 条件运算符
简单的if else语句可根据情况改写成条件运算更为简洁

### typeof

### delete
1.删除数组元素，不改变数组长度（相当于是打了个洞）
2.不能删var声明的变量，不能删function定义的函数和函数参数
3.关于对象：只能删除自有属性，不能删除继承属性

## 第5章：语句
### 空语句
```
//初始化一个数组a
for(var i =0; i < a.length; a[i++] = 0) /* empty */ ;
```

### switch
```
switch(expression){
    case expression:
    //代码块
    break;
}
```
1.用break或return(函数中)终止
2.如若不终止，会从匹配到的case向下逐个执行
3.表达式匹配是按 ===（严格相等） 来匹配

### while和do while
```
while(){
    
}

do{
    
}while();
```

### for/in
```
for(variable in object){
    
}
```
其中variable可以是变量也可以是表达式
```
var o = {name: '倩倩', sex: '女'};
var arr = [], i = 0;
for(arr[i++] in o) ;    //将对象属性存储在数组arr中
console.log(arr);   //["name", "sex"]
for(var i = 0; i < arr.length; i++){
    console.log(o[arr[i]]); //倩倩 女
}

//遍历json对象数组
var o = {stu1: {name: '倩倩', sex: '女'}, stu2: {name: '倩宝', sex: '女'}, stu3: {name: '小甜甜', sex: '女'}};
var arr = [], i = 0;
for(arr[i++] in o) ;    //将对象属性存储在数组arr中
console.log(arr);   //["stu1", "stu2", "stu3"]
for(var i = 0; i < arr.length; i++){
    console.log(o[arr[i]]); //Object {name: "倩倩", sex: "女"} Object {name: "倩宝", sex: "女"} Object {name: "小甜甜", sex: "女"}
}
```

### 标签语句
```
label: statement
```
1.只有break和continue可以使用标签语句
2.statement可以是普通语句也可以是代码块
3.一个语句标签不能和内部语句标签同名，但两个不嵌套的相互独立的可以同名
4.无法越过函数边界。即对一条带标签的函数定义语句，不能从函数内部通过标签访问函数外部。
```
test: for(var i = 0; i < 3; i++){
    console.log('visible');
    continue test;  //continue跳出本次循环进行下次循环，break跳出整个循环
    console.log('invisible');
}
```

### try/catch/finally
```
try{
    
}catch(e){
    
}finally{
    
}
```
1.通常执行完try语句块，执行finally
2.如若有异常，从与try语句就近的catch语句开始向上匹配，直到遇到处理这个异常的。
3.try中有return，continue，break跳转语句，在跳出之前，先执行finally
4.如若有异常，而且与try语句相关的catch不是处理这个异常的，先执行finally，再向上匹配
5.finally从句运行了return语句，不管有没有未处理的异常，方法依然会正常返回。
```
var func = function(){
    try{
    //抛出一个异常
    }
    finally{
        return 1;
    }
};
func(); //1
```

## 第6章：对象
### 相关名词
可写：能否设置属性的值
可枚举：能否for/in循环属性返回
可配置：能否修改和删除

对象的原型
对象的类
对象的扩展标记：是否可向该对象添加新属性

内置对象
宿主对象
自定义对象

自有属性
继承属性

### 创建对象
1.对象直接量
2.new
3.Object.create(args1, [args2]) //args1为对象原型，args2为可选参数（第九章了解）

#### 原型
每个js对象都和另一个对象相关联，
“另一个对象”就是原型，每个对象都从原型继承属性

1.直接量创建的对象 的原型：Object.prototype
2.new创建的对象 的原型：构造函数.prototype。如：new Array()，原型为Array.prototype。但同时也继承自Object.prototype。（原型链）
3.Object.create(args1, [args2]) 的原型：args1。（第九章详细了解）
```
var o = {};
var o1 = new Object();
Object.prototype.say = function(){
    console.log('i am 倩倩');
};
o.say();    //i am 倩倩
o1.say();   //i am 倩倩

o.say = function(){
    console.log('i am 小甜甜');
}
o.say();    //i am 小甜甜
o1.say();   //i am 倩倩
```

### 属性访问错误

### 检测属性
1.in运算符
2.hasOwnProperty()  //是否是对象的自有属性
3.propertyIsEnumerable()    //是自有属性且可枚举
4.!== undefined  //局限性，假使属性被显式赋值为undefined时会导致判断出错

### 枚举属性
在ES5标准之前，一些工具库给Object.prototype添加属性和方法，然而会被每个对象继承，并且会在for/in循环中枚举出来
```
var o = {name: '倩倩', sex: '女'};
Object.prototype.say = function(){
    
}
for(var i in o){
    //通过这两条语句可以控制：if(!o.hasOwnProperty(i)) continue; if(typeof o[i] === "function") continue;
    console.log(i); //name sex say
}
```

#### ES5 两个枚举属性的方法
1.Object.keys() //返回数组，由对象中可枚举的自有属性名称组成
2.Object.getOwnPropertyNames()  //返回数组，对象的所有自有属性名称，不仅仅是可枚举的属性
```
var o = {name: '倩倩', sex: '女', name1: '倩宝', say: function(){}};
console.log(Object.keys(o));    //["name", "sex", "name1", "say"]
Object.defineProperty(o, 'name1', {enumerable: false}); //属性的特性中讲到如何设置
console.log(Object.keys(o));    //["name", "sex", "say"]
console.log(Object.getOwnPropertyNames(o)); //["name", "sex", "name1", "say"]
```

### 属性getter和setter
明确概念：数据属性和存取器属性
和数据属性一样，存取器属性是可继承的

#### 给对象直接量添加存取器属性
```
var o = {
    //数据属性
    name: '倩倩',
    sex: '女',
    //存取器属性
    set operationSex(_sex){
        this.sex = _sex;
    },
    get operationSex(){
        return this.sex;
    }
}
console.log(o.sex);
console.log(o.operationSex);
o.operationSex = '女神';
console.log(o.sex);

//返回序列号
var serialnum = {
    $n: 0,
    get next(){
        return this.$n++;
    }
}
for(var i = 0; i < 10; i++){
    console.log(serialnum.next);    //0-9
}
```

#### getter和setter的老式API
_lookupGetter_()
_lookupSetter_()
_defineGetter_()
_defineSetter_()

### 属性的特性
一个属性包含一个值和四个特性
数据属性：value, writable, enumerable, configurable
存取器属性：get, set, enumerable, configurale

#### 查看自有属性描述符（即四个特性）
Object.getOwnPropertyDescriptor(obj, attr);
```
var o = {name: '倩倩', sex: '女'};
Object.getOwnPropertyDescriptor(o, 'sex');  //Object {value: "女", writable: true, enumerable: true, configurable: true}
```
要想查看继承属性的描述符，需遍历原型链Object.getPrototypeOf()

#### 设置属性特性
修改一个属性
Object.defineProperty(obj, attr, {})
同时修改多个属性
Object.defineProperties(obj, {
    attr1: {},
    attr2: {}
});
```
//属性虽然为只读，但是依然可配置
var o = {name: '倩倩', sex: '女'};
Object.defineProperty(o, 'name', {writable: false});
o.name = '倩宝';  //严格模式下报错，非严格模式下操作失败但不报错
console.log(o.name);
Object.defineProperty(o, 'name', {value: '小甜甜'});
console.log(o.name);
```

#### 完整规则
对象在不可扩展和对象属性不可配置的情况下，几种操作规则。（六条）

### 对象的三个属性
#### 原型属性
查看：Object.getPrototypeOf(obj)
判断：.isPrototypeOf(obj)

Mozilla专门命名一个_proto_，与上述功能类似，有部分浏览器实现了，但不建议使用

#### 类属性
默认的toString()方法，返回如下格式字符串
[object class]
因为很多对象toString方法重写，需要间接调用Function.call()方法（第8章详解），如下实现
```
function classof(o){
    if(o === null) return 'Null';
    if(o === undefined) return 'Undefined';
    return Object.prototype.toString.call(o).slice(8,-1);
}
var a = [];
console.log(classof(a));    //Array
```

#### 可扩展性
Object.esExtensible(obj)    //判断该对象是否可扩展
Object.preventExtensions(obj)   //转换为不可扩展
一旦转换为不可扩展，就无法转换为可扩展了。但只影响本身，如果给某个不可扩展的对象的原型添加属性，这个对象同样可以继承。
Object.seal(obj)    //除了设置不可扩展，自有属性也设置为不可配置
同样封闭起来不可解封，查看是否封闭用Object.isSealed()
Object.freeze(obj)  //除了设置不可扩展，自有属性也设置为不可配置，自有数据属性设置为只读，存储器属性不受影响
查看用Object.isFrozen()

### 序列化对象
JSON.stringify()和JSON.parse()只能序列化可枚举属性，可以接收第二个可选参数，定制序列化。

## 第7章：数组
### 创建数组/数组元素的读和写
```
var arr = new Array(5); //一般用直接量
arr['6'] = 'Qian';
arr[7] = 'Bao';
console.log(arr + ', ' + arr.length);   //,,,,,,Qian,Bao, 8
```

### 稀疏数组
包含从0开始的不连续索引的数组
```
var a1 = [,,,];
var a2 = [,];
console.log(a1 + ', ' + a1.length); //,,, 3
console.log(a2 + ', ' + a2.length); //, 1
console.log(0 in a1);   //书上讲为true（代表在索引0处有一个元素），实际为false
console.log(0 in a2);   //false
```

可以通过数组的方法来压缩稀疏数组

### 数组长度
```
var a = [1,2,3,4,5];
a.length = 3;
console.log(a); //[1, 2, 3]
```

1.可通过Object.defineProperty()设置length及属性
2.当一个属性设置为不可配置时（即不能删除），length不能设置为小于这个不可配置元素的索引值

### 数组遍历
for
for/in  //遍历可枚举属性名，可过滤稀疏数组
forEach()方法

### 多维数组
javascript不支持多维数组，但可以通过数组中的数组元素来模拟

### 数组方法
join() reverse() sort() concat() slice() splice() push()和pop() unshift()和shift() toString()和toLocalString()

#### sort()
```
var arr = [5,2,3,1,4];
arr.sort(function(a,b){
    return a - b;
});
console.log(arr);   //[1, 2, 3, 4, 5]
```

### ES5的数组方法
1.大多数方法的第一个参数是一个函数，并对数组的每一个元素（或一些元素）都调用一次该函数
2.这个函数使用三个参数：数组元素，元素索引和数组本身，通常只需第一个参数
3.若方法中有第二个参数，则函数（第一个参数）会作为这个参数的方法，即this指向
4.不同方法处理返回值的方式不一样。

#### forEach()
```
var arr = [1,2,3,4,5],sum = 0;
//仅访问
arr.forEach(function(v){
    sum += v;
});
console.log(sum);   //15

//操作
arr.forEach(function(value, index, obj){
    obj[index] = value + 1;
});
console.log(arr);   //[2, 3, 4, 5, 6]
```

#### map()
有一个返回值（不修改原数组）
```
var arr = [1,2,3,4,5];
var arr1 = arr.map(function(v){
    return v * v;
})
console.log(arr + ', ' + arr1); //1,2,3,4,5, 1,4,9,16,25
```

#### filter()
返回符合条件判断的一个子集

可以过滤压缩稀疏数组
```
function(){ return true; }
```

#### every()和some()
定义过滤条件，返回true或false

#### reduce()和reduceRight()
```
array.reduce(function(total,currentValue,currentIndex,arr),[initialValue])
```

#### indexOf()和lastIndexOf()

### 数组类型
**Array.isArray()**

### 类数组对象

### 作为数组的字符串
字符串是不可变类型，所以数组只有不是更改原值的方法应该都适应于字符串。

## 第8章：函数
### 函数定义
```
//定义后直接调用
var sum = function(m){
    return m + m;
}(10);
console.log(sum);

//作为参数传递给其他函数
var arr = [5,2,4,1,3];
console.log(arr.sort(function(a,b){
    return a - b;
}));
```

### 函数调用
1.作为函数
非严格模式：this为全局对象
严格模式：this为undefined
可以用作来判断严格/非严格模式

2.作为方法
任何函数只要作为方法调用都会传入一个隐式的实参，这个实参就是一个对象。
```
var o = {
    name: '倩倩',
    sex: '女',
    say: function(){
        that = this;
        console.log(this.name);
        mySex();

        //定义函数mySex()
        function mySex(){
            console.log(that.sex);
        }
    }
}
console.log(o.say());   //倩倩 女
```

**方法链**
当方法的返回值是一个对象，这个对象还可以调用它的方法。
这种方法调用序列中（通常称为“链”）每次调用结果都是另外一个表达式的组成部分。

当方法并不需要返回值时，最好直接返回this。（进行链式调用风格的编程）

注：别把方法的链式调用和构造函数的链式调用混为一谈

3.作为构造函数
构造函数调用和普通函数调用和方法调用的不同：
**实参处理：构造函数先处理括号里的实参表达式，计算完毕传入函数中；若没有实参，可以省略括号**
```
var o = new Object();
var o = new Object;
```
**调用上下文：构造函数将新创建的对象用作其调用上下文，用this关键字来引用这个对象。**
```
var Constructor1 = function(){
    this.name = '倩';
    that = this;
    this.say = function(){
        that.sex = '女';
    }
}
var t1 = new Constructor1();
console.log(t1.sex);    //undefined
t1.say();
console.log(t1.sex);    //女

var o = {
    name: '倩宝',
    Constructor2: function(){
        this.honey = '小甜甜';
    }
}
var t2 = new o.Constructor2();
console.log(t2.honey);  //小甜甜
```
**返回值：通常不使用return；如果显式返回一个对象，调用表达式的值就是这个对象，如果没有指定值或是返回一个原始值，则调用表达式的值就是新构造出来的对象。**
```
var o = {
    name: '倩倩'
};
function Construtor(){
    this.name = '倩宝'
    return o;
}
var func = new Construtor();
console.log(func.name); //倩倩
```

4.通过它们的call()和apply()方法间接调用

### 函数的实参和形参
#### 传入形参比实际形参少：可选形参
```
function getProperties(o, arr){
    arr = arr || [];
    for(var i in o){
        arr.push(i);
    }
    return arr;
}
var obj = {
    name: '',
    sex: '',
    say: function(){

    }
}
console.log(getProperties(obj));    //["name", "sex", "say"]
```

#### 传入形参比实际形参多：可变长的实参列表，即实参对象
标识符arguments指向实参对象的引用

非严格模式下，arguments[0]等同于x，操作任一个都会对另一个造成影响
```
function test(x){
    console.log(x); //5
    arguments[0] = 'Qian';
    console.log(x); //Qian 若声明'use strict' x = 5
}
test(5,3,6,4,7,9);
```

**ES5 移除了上一特性，非严格下，arguments仅作标识符，严格下，函数中无法用arguments作形参名或局部变量名，也不能给arguments赋值。**

**callee和caller属性**
注：严格模式下对这两个属性进行读写操作都会抛错
arguments.callee指代当前正在操作的函数（递归使用）
arguments.caller(非标准)指代当前正在操作的函数的函数（访问调用栈）

#### 将对象属性用作实参
好处：形参太多时不需要去记顺序
```
function base(a,b,c,d,e,f){
    
}
function collect(obj){
    base(obj.a, obj,b, ...)
}
```

#### 实参类型
虽然有自动转换类型，但必要时需显式检验函数中的参数类型

#### 作为值的函数
赋给变量，对象属性，数组元素，或是某函数的一个参数

#### 自定义函数属性
不污染全局属性，将num作为函数对象的一个属性来保存
```
var num = 5;
counter.num = 0;
function counter(){
    return counter.num++;
}
var n = 0;
while(n<8){
    console.log(counter() + 'times');   //0 - 7 times
    n++;
}
console.log(counter.num);   //8
console.log(num);   //5
```

另一种方式：使用自身属性（将函数自身当做一个数组）来缓存
```
function f(num){
    f[1] = 1;   //初始化属性是必须的
    if(!(num in f))
        f[num] = num * f(num - 1);
    return f[num];
}
console.log(f(3));  //6
```

### 闭包
#### 理解作用域链
1.函数定义时的作用域链在函数执行时依然有效
2.每次调用函数时会创建一个新的对象，这个对象用来保存局部变量，将此对象添加至作用域链中，当函数返回时，就从作用域链中将这个（绑定变量）的对象删除掉。【如不存在嵌套函数，也没有其他引用指向这个对象，会被垃圾回收】
3.如果定义嵌套函数，每个函数都各自对应一个作用域链，并且这个作用域链指向一个（变量绑定）对象。【原理和2中的相同】【当这些嵌套函数指向的（变量绑定）对象在外部函数中保存下来，那也会随这个外部函数指向的（变量绑定）对象被垃圾回收】
4.如果嵌套函数作为返回值或存储在某处的属性中，这时就有外部引用指向嵌套函数，自然不会被回收，而且所指向的（变量绑定）对象也不会被回收
```
//查看作用域链
function father(){
    var name = '倩';
    var sex = '女';
    (function bro1(){ console.log('bro1\'s father: ' + bro1.caller) })();
    (function bro2(){ console.log('bro2\'s father: ' + bro2.caller) })();
}
father();   //father x 2

//简单的一个闭包例子
var scope = 'global scope';
function checkScope(){
    var scope = 'local scope';
    function f(){ return scope; }
    return f;
}
console.log(checkScope()());    //local scope
```

#### 变量共享
```
function correct(i){
    return function(){ return i; };
}
var arr = [];
for(var i = 0; i < 3; i++){
    arr[i] = correct(i);    //每次调用函数新创建一个绑定变量对象
}
for(var i in arr){
    console.log(arr[i]());  //0 1 2
}

//闭包共享一个变量i
function err(){
    var arr = [];
    for(var i = 0; i < 3; i++){
        arr[i] = function(){ return i; };
    }
    return arr;
}
var a = err();
for(var i in a){
    console.log(a[i]());    //3 3 3
}
```

### 函数属性、方法和构造函数
#### length
arguments.length    实际传参个数
arguments.callee.length 期望传参个数

#### prototype属性

#### call()和apply()
将函数作为指定对象的方法来调用
```
function add(y,z){
    return this.x + y + z;
}
var o = {
    x: 1
};
console.log(add.call(o,2,3));   //6
```

#### bind()
将函数绑定至指定对象，返回一个新函数

返回的新函数对象length属性是绑定函数的形参个数减去绑定实参的个数（length值不能小于零）
```
function add(y){
    return this.x + y;
}
var o = {
    x: 1
}
var addNew = add.bind(o /*, arg1, arg2, ...*/);
console.log(addNew(2));
```

#### toString()
一般函数返回源码
内置函数返回一个类似'[native code]'的字符串作为函数体

#### Function()构造函数
1.允许javascript在运行时动态创建并编译函数
2.因为1的原因，所以这种方式在循环中效率很低
3.创建的函数始终是全局函数

#### 可调用的对象
检验一个对象是否是真正的函数对象
```
function isFunc(o){
    return Object.prototype.toString.call(o) === '[object Function]';
}
```

### 函数式编程
#### 使用函数处理数组

#### 高阶函数
```
function compose(f, g){
    return function(){
        return f.call(this, g.apply(this, arguments));
    }
}
var square = function(x){ return x * x; };
var sum = function(x, y){ return x + y; };

var squareofsum = compose(square, sum);
console.log(squareofsum(2, 3)); //25
```

#### 不完全函数
将f(1,2,3,4,5,6)
改为等价的f(1,2)(3,4)(5,6)

#### 记忆

## 第9章：类和模块
```
function Range(){}
Range.prototype = {
    //constructor: Range,
    name: '倩'
};
var r = new Range();
console.log(r.constructor); //Object() { [native code] }

var F = function(){};
var f = new F();
console.log(f.constructor);
```

## 第13章：web浏览器中的javascript
### 安全性
#### 同源策略
通俗讲：脚本只能读取和**所属文档来源相同**的窗口和文档的属性

文档的来源包括协议，主机，以及载入文档的url端口：
1.不同的web服务器
2.同一主机不同端口
3.http:协议和https:协议(即使是相同服务器)
以上三种都具有不同的来源

脚本本身的来源和同源策略不相关，相关的是脚本所嵌入的文档的来源，举个例子

    A源：a.js     a.html
    B源：b1.html 包含了a.js  还有个b2.html
    C源：c.html
这种情况下a.js能够访问b1.html和b2.html，但不能访问a.html或c.html

#### 不严格的同源策略
1.documet.domain
使用多个子域的大站点，比如
home.example.com 和 catalog.example.com
需要在窗口包含的脚本把domain设置成相同的值

2.标准化：跨域资源共享CORS（cross-origin resource sharing）
通过
新的Origin请求头
和新的Access-Control-Allow-Origin响应头
来扩展http

它允许服务器用头信息显示地列出源，或使用通配符来匹配所有的源并允许由任何地址请求文件

3.跨文档消息（cross-document messaging）
允许一个文档的脚本传递文本信息给另一个文档的脚本，不管来源是否相同

使用：Window对象的postMessage()方法
可以用onmessage事件句处理程序函数来处理

#### 跨站脚本
cross-site scripting，又叫XSS

**XSS攻击：**
攻击者通过向目标Web站点注入html标签或脚本。

**情景：**
web页面动态产生文档内容，并且这些内容是基于用户提交的数据的（并没有从中移除任何html标签来消毒）

**例子：**
```
<script>
    var name = decodeURIComponent(window.location.search.substring(1)) || '';
    document.write('Hello ' + name);
</script>
```
请求：``http://www.example.com/greeting.html?felbry``
页面显示：Hello felbry

请求：``http://www.example.com/greeting.html?%3Cscript%3Ealert('you are fighted')%3C/script%3E``
页面弹出：you are fighted
同样可以通过script标签引入一个外部的js文件

**防止：**
1.通过简单的移除（转义）尖括号（基于上例）
``name = name.replace(/</g, '&lt;').replace(/>/g, '&gt;');``

2.IE8定义了一个toStaticHTML()，移除script标签和其他潜在的可执行内容，不修改不可执行的html
注：这个方法不是标准，可以仿照着来实现

3.H5的iframe
H5的iframe的sandbox属性，允许显示不可信的内容，并自动禁用脚本

## 第14章：Window对象
### 多窗口和窗体
**不管是窗口还是窗体，其根本是获得所需窗口或窗体的window对象引用**

#### 多窗口通信
``window.open(url, name, argsList, replaceOrCreate)``
四个参数，不赘述，详见文档

窗口名字：window.name（可写 writable）
当调用window.open()方法时，可以通过传入name值指定新建窗口的名字，当然也可以指定已存在的name跳转至对应的窗口。一个window窗口的opener属性对应打开它的窗口，通过这种方式，即可实现窗口间的相互通信

一个窗口关闭，代表它的window对象依然存在，会有个值为true的closed属性，它的document是null，方法通常也不会工作
```
win1.html
<button id="btn1">点击打开新窗口</button>
    
<script src="jquery-2.2.4.min.js"></script>
<script src="win1.js"></script>

win2.html
<h2 id='p2'>这是初始值</h2>

<script src="jquery-2.2.4.min.js"></script>
<script src="win2.js"></script>

win1.js
$(function(){
    $('#btn1').click(function() {
        w = window.open('win2.html');
        w.onload = function(){
            //访问h2元素值
            console.log(w.document.getElementById('p2').innerHTML); //这是初始值
            
            //新创建p元素
            var p = w.document.createElement('p');
            p.innerHTML = 'new create';
            w.document.body.appendChild(p);

            //更改h2元素值
            w.document.getElementById('p2').innerHTML = 'changed value';
        }
    });
})

win2.js
$(function(){
    opener.document.body.innerHTML = '<h1>哈哈，反向控制！</h1>';
})
```

#### 多窗体(iframe)通信
**只要确定一个iframe的window对象，其他的iframe窗体都可以通过parent或top表示。**

**表示一个iframe的window对象：**
窗口中iframe元素的contentWindow属性，反向操作（即通过window对象得到iframe元素）用frameElement
```
var ele = document.getElementBy..('');
var frameWin = ele.contentWindow;
ele === frameWin.frameElement;  //true
```

**多种方法引用窗口或窗体的子孙窗体：**
1.frames：每个window对象都有个frames属性，类数组对象，引用自身包含的窗口或窗体的子窗体。可以通过数字或窗体名索引
比如：引用某个窗口的子窗体，用``frames[0]``；引用某个窗体的兄弟窗体，用``parent.frames[1]``；如果``<iframe>``元素有name属性，可以通过``frames['itsname']``或``frames.itsname``
2.window[num]
还可以通过window.length或length查询窗体的编号

**iframe的name属性：**
1.``<iframe>``元素本身有name属性，会作为这个window.name的值
2.name可用作一个链接的target属性
3.name可用作window.open()方法的第二个参数

#### 总结
**窗口(浏览器窗口或标签页)之间通信：**
1.window.open()
2.postMessage() //暂不讨论

**窗体(iframe)之间通信：**
1.通过parent（直接父级）或top（顶级祖先）定位到所需的window对象，从而获得其引用
2.frames属性

## 第18章：脚本化http
### 跨域http请求
如果浏览器支持XMLHttpRequest的CORS且实现跨域请求的网站决定使用CORS允许跨域请求，那么同源策略将不放宽而跨域请求就会正常工作

实现CORS支持的跨域请求工作不需要做任何事情，但有些安全细节：
1.若给XHR对象的open()方法传入用户名和密码，不会通过跨域请求发送
2.跨域请求也不会包含其他任何的用户证书：cookie和HTTP身份验证令牌（token）
3.如跨域请求需这几种凭证才能成功，必须在send()发送请求前设置XHR对象的withCredentials属性为true（不常用，但withCredentials的存在可测试浏览器是否支持CORS）

### 借助``<script>``发送http请求：JSONP
1.P代表“填充”或“前缀”
2.响应内容必须用js函数名和圆括号包裹起来

    若单纯返回一个json对象，客户端会报 Uncaught SyntaxError: Unexpected token : 错误
3.包裹后的响应会成为script元素的内容
4.一般通过指定一个查询参数的值来告诉服务端这是一个``<script>``元素调用，应返回jsonp响应，而不是普通的json响应。（一般将这个参数放置在多个参数的最后，命名为jsonp或callback）
5.服务端不会强制指定客户端必须实现的回调函数名称，而是使用查询参数的值（即jsonp或callback的值），这样达到了和客户端函数名的对应。

```
A源客户端：
var script = document.createElement('script');
script.src = 'http://felbry.leanapp.cn/jsonp?arg1=girl&jsonpCB=jsonpCallback'
document.body.appendChild(script);

function jsonpCallback(res){
    alert(res.name);    //倩倩
}

B源服务端(node.js的express框架)：
app.get('/jsonp', function(req, res){
    console.log(req.query.jsonpCB); //jsonpCallback
    console.log(req.query.arg1);    //girl
    res.send(req.query.jsonpCB + "({name: '倩倩'})"); //jsonpCallback({name: '倩倩'})
})
```