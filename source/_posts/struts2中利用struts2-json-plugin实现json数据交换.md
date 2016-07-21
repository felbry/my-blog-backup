title: struts2中利用struts2-json-plugin实现json数据交换
date: 2016-07-05 16:49:18
comments: true
categories: TECH NOTE
tags:
- struts2
- struts2-json-plugin
- json
- ajax
- mysql配置
---
* struts2基本数据交换方式
* 实现json数据交换方式（struts2-json-plugin）
* json-lib，java解析json字符串的包
* 一个完整的Demo
* MySql数据库基础配置
* 用Connector/J驱动连接MySql及常用简单语句
* 待定补充开发所遇问题

<!-- more -->

## struts2基本数据交换方式
### 流程
因为对struts2框架了解不是很多，很多知识都是草草地翻过几页课本得到的，所以在我印象里其数据交换方式无非是
1.前台form表格指定action(name)将值传入后台，
2.然后在struts.xml中指定action的处理类，对前台传过来的数据的处理就在这个指定的类中进行，
3.在指定的类中将前台所需要的数据通过setter方法存入bean中，
4.前台需要访问数据就通过**< s:property value='' />**来进行，value的值对应bean中的变量。
**流程图**
{% asset_img 1.png %}
### 代码示例
```
login.jsp
<form action="check" method="post">
    <input type="text" name="user.username">
    <input type="text" name="user.password">
    <input type="submit" value="提交">
</form>
```

```
struts.xml
<package name="default" extends="struts-default">
    <action name="check" class="login.CheckInfo">
        <result name="success">success.jsp</result>
        <result name="false">failure.jsp</result>
    </action>
</package>
```

```
CheckInfo.java
package login;
import com.opensymphony.xwork2.*;
import login.User;
public class CheckInfo extends ActionSupport {
    User user;
    //setter方法是必须的，不然会抛出无法赋值的错误
    public User getUser() {
        return user;
    }
    public void setUser(User user) {
        this.user = user;
    }
    @Override public String execute(){
        user.setTotal(user.username + user.password + "yes");  //便于结果界面访问值
        return SUCCESS;
    }
```

```
UserBean.java
package login;
public class User {
    String username,password,total;
    //setter,getter方法
}
```

```
success.jsp
<s:property value="user.total" /><br>
<s:property value="user.username" /><br>
<s:property value="user.password" />
```

这种通过form提交数据，**< s:property value='' />**访问数据的方式显然有些笨拙，会导致为了完成某些简单功能在几个页面之间跳来跳去。所以如果采用json数据格式，通过ajax来异步刷新页面会更友好。即**不再返回一个完整页面，而是返回一个json对象，通过回调函数来处理这个json对象数据，再操作dom元素。**

### 补充（setter作用）
struts框架下，action处理类要想获得前台参数值，需要在类中定义和前台name相同的变量名，然后设置setter方法，就会默认取到。
所以如果一个action类中import一个Bean类的话，要想获得前台的值，不仅要声明一个bean对象，还要设置相应的setter方法，以便bean对象能顺利取到值。

## 实现json数据交换方式（struts2-json-plugin）
如前一节所说，返回一个完整页面显然很笨重且不友好。现在很多网站都不会采用这种方式，而是采用Ajax异步刷新技术，而这种技术最常用的数据格式便是json了，因为它比较轻量且易于操作。
struts2有struts2-json-plugin来返回一个json对象。在此不需要深究它的源码，只需要了解它的机制：**它会将所有的变量序列化成一个json对象（键值对）然后返回给前台**。当然这个返回的值是可自定义的，一种方法是在变量的getter方法前声明@JSON(deserialize/serialize=true)来指定某个变量序列化或不序列化，另一种方法是在struts.xml中在对应的action标签中定义param变量，指定某些变量序列化或不序列化，param变量还有其他几个有用的可选参数，在此只作简单介绍，详细移步文档：[JSON plugn Doc](https://struts.apache.org/docs/json-plugin.html)
### 流程图
**基于上一节的流程图，在jsp页面和struts.xml文件之间加上一个js文件，由js发起请求，并在回调函数中接收后端返回的json对象。**

{% asset_img 2.png %}

### 条件与使用
1.在**WebRoot/WEB-INF/lib**文件夹下导入struts2-json-plugin包（自行下载）
2.如需使用**@JSON xxx**声明，在action类开头声明**import org.apache.struts2.json.annotations.JSON;**（有时复制进去的不管用）
3.在struts.xml文件中，package要继承**json-default**(json-default继承自struts-default)， action标签中的result标签，添加属性**type="json"**

### 代码示例
```
login.jsp
<!-- 之所以不用form表，是因为submit在form表被点击就会刷新页面，不方便看xhr -->

用户名：<input type="text" id="username">
密码：<input type="text" id="password">
<input type="submit" value="提交">

<script type="text/javascript" src="js/jquery-2.2.4.min.js"></script>
<script type="text/javascript" src="js/identInfo.js"></script>
```

```
identInfo.js
$(function(){
    $('input[type="submit"]').click(function(){
        var data = {
            username: $('#username').val(),
            password: $('#password').val()
        };
        //请求name为check的action，传入一个Object(json)变量
        $.post('check', data, function(res, textStatus, xhr) {
            //打印出返回的res
            console.log(res);
        });
    })
});
```

```
struts.xml
<package name="default" extends="struts-default,json-default">
    <action name="check" class="login.CheckInfo">
        <result name="success" type="json"></result>
    </action>
</package>
```

```
CheckInfo.java
package login;
import com.opensymphony.xwork2.*;
public class CheckInfo extends ActionSupport {
    String username,password,resStr = "I am a response string";
    //getter和setter方法
    @Override public String execute(){
        System.out.println(username);   //123
        System.out.println(password);   //456
        return SUCCESS;
    }
```

页面中输入123,456，点击提交
{% asset_img 3-1.png %}
在后台会分别输出username，password的值123,456

接着在浏览器按F12查看控制台
{% asset_img 3-2.png %}
发现返回的json对象中有三个属性，分别为username，password和resStr。因为我们没有特意指定哪些不需要返回，所以默认的所有变量都会被序列化进返回的json对象中。

### 补充（为什么用json-lib）
**这种方式太过于简单。一般我们会把变量及对应值都储存在一个Bean当中，然后在action中进行存取操作。想象一下，如果按照所说的那样，我们就要像第一节中的某些代码一样，在action中定义一个Bean对象，设置setter和getter方法；同时为了匹配对象属性，能够赋值成功，就不得不把前台变量名改为形如user.username的格式。不然就会抛Error setting expression 'xxx' with value ['xxx', ]的错误。**
**显然变量名user.username这种格式在js中是存在错误的，所以并不能以这种方式顺利的传值给后台。这时候就用到了json-lib包，它的好处在于提供一些方法，可以直接将一个json字符串解析并赋值给指定Bean对象的各个属性。也就是说，在js中，只需要将所需的数据封装成一个json字符串，以字符串的形式传递给后台，后台通过一条语句就能完成全部赋值工作。**

## json-lib，java解析json字符串的包
使用json-lib包，同时还要导入它的依赖包(否则会在解析时报错)。这里有篇文章提供了json-lib包及其所有依赖包的打包下载(在文章最后)，以及json-lib的常用方法，介绍的很详细。链接：[JSON-LIB jar包下载和使用](http://www.sojson.com/blog/20)。
我在这里只简单介绍下思路，对第二节的代码进行适当修改，让它更具灵活性。
json-lib提供了几个方法
```
JSONObject jsonObject = JSONObject.fromObject(jsonString);  //将一json字符串转为jsonObject
JSONObject.toBean(jsonObject, clazz);   //将jsonObject的值分别赋给对应的bean属性
```

我们将它封装成一个方法，以便调用
```
public static Object getObj(String jsonString, Class clazz) {
    JSONObject jsonObject = null;
    try {
        jsonObject = JSONObject.fromObject(jsonString);
    } catch (Exception e) {
        e.printStackTrace();
    }
    return JSONObject.toBean(jsonObject, clazz);
}
```

这样一来，第二节的action就可以修改为
```
CheckInfo.java
package login;

import login.User;
import java.util.*;
import net.sf.json.JSONObject;

import com.opensymphony.xwork2.*;
public class CheckInfo extends ActionSupport {
    String myjson;
    @JSON(deserialize=true) //不返回myjson变量
    public String getMyjson() {
        return myjson;
    }
    public void setMyjson(String myjson) {
        this.myjson = myjson;
    }
    @Override public String execute(){
        User user = (User) getObj(myjson, User.class);
        //假设前台输入456,789
        System.out.println(myjson); //{"username":"456","password":"789"}
        System.out.println(user.username);  //456
        System.out.println(user.password);  //789
    }
```

如此，我们便完成了解析赋值的工作。现在，要考虑返回json对象的问题。CheckInfo.java中目前只有一个myjson变量，并且设置了不进行序列化。所以此时返回一个空的json对象。
要返回前台所需的数据（即某个bean的多个属性值），有两种方法：
一种是在struts.xml中的action里设置**< param name="root" >...< /param >**指定从哪里开始序列化，详情请参照文档；
另一种比较简单也比较灵活的，是在当前action中声明一个dataMap变量：**private Map< String,Object > dataMap;**，设置getter方法，将所有的实例化Bean对象put进dataMap，从而能够全部返回。如下代码所示
```
CheckInfo.java
public class CheckInfo extends ActionSupport {
    String myjson;
    private Map<String,Object> dataMap;
    //对应的(setter)和getter方法
    @JSON(deserialize=true) //不返回myjson变量
    public String getMyjson() {
        return myjson;
    }
    public void setMyjson(String myjson) {
        this.myjson = myjson;
    }
    @Override public String execute(){
        User user = (User) getObj(myjson, User.class);
        //假设前台输入456,789
        System.out.println(myjson); //{"username":"456","password":"789"}
        System.out.println(user.username);  //456
        System.out.println(user.password);  //789
        // ----------------------------------
        //不完整的代码，只为展示执行完sql语句返回n组数据如何处理
         ResultSet rs = stmt.executeQuery(sql);
         int i = 0;
         while(rs.next()){
             Member memberJson = new Member();
             memberJson.setName(rs.getString(1));
             memberJson.setSex(rs.getString(2));
             //3,4,5...
             dataMap.put(""+ i +"", memberJson);
             i++;
         }
         //此时前台返回对象Object{1: {}, 2: {}, 3: {}, ...}
    }
```

### 补充（前台js处理返回的Object问题）
由于返回的是一个json对象数组，准确的说是形如数组。所以在处理上也碰到一些小问题。下边分享一下我的奇葩解决方式。
以下方式全部基于访问对象
```
Object{1: {attr1: .., attr2: .., ..}, 2: {}, 3: {}, ...}
```

1.计算Object的长度
```
var arr = [];
for(var i in res){
    arr.push(i);
}
console.log(arr.length);
```

2.按顺序遍历res中的每个对象
开始使用的是**for(var i in res)**，
到**排序**的时候发现在页面显示的结果不对。无论是数据库还是后台查看输出结果都是正常的，后来发现原来**for(var i in res)**是随机遍历res的。然后就有了下面的解决办法
```
for(var i = 0; i < arr.length; i++){
    var str = ''+ i +'';
    console.log(res[str].attr1);    //attr1,2,3为变量名
    console.log(res[str].attr2);
    console.log(res[str].attr3);
}
```

补充：一般访问json对象属性用**obj.attr.attr**

## 一个完整的Demo
移步： [社团成员信息管理系统：CRUD和筛选排序](https://github.com/felbry/social-organization-management)
数据库结构信息：
{% asset_img 4.png %}

## MySql数据库基础配置
参照了以下两篇文章汇总结合，其中各有优缺，总结出比较完善的配置。建议看不懂这里步骤的可以先看看这两篇文章，对流程有个大概的认知。
[MySQL环境部署](http://www.cnblogs.com/yyhh/p/5062153.html)
[21分钟MySQL入门教程](http://www.cnblogs.com/mr-wid/archive/2013/05/09/3068229.html)

步骤：
1.下载zip文件(msi的就不用往下看了) [下载地址](http://dev.mysql.com/downloads/mysql/)
2.解压到某个文件夹，然后配置MYSQL_HOME和path变量
3.修改配置文件my-default.ini，以下是几个重要配置（版本号视自己的版本而定，路径建议都写为绝对路径），然后重命名为my.ini
**# 设置mysql的安装目录**
basedir=D:/mysql-5.6.13

**# 设置mysql数据库的数据的存放目录**
datadir=D:/mysql-5.6.13/data

**# 字符编码**
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
character-set-server=utf8
collation-server=utf8_unicode_ci

**字符编码很重要，不然后续会出现乱码情况，如果有乱码状况，也可以暂停并删除服务，配置好my.ini文件，重新安装服务。**

这里有两篇解决乱码的文章写得不错，提供参考
[修改MySQL字符编码为UTF-8解决中文乱码](http://www.osyunwei.com/archives/9178.html)
[Mysql中文乱码以及Incorrect string value的问题](http://blog.sina.com.cn/s/blog_76786df30101aqfp.html)

4.安装服务：以管理员方式进入cmd
执行mysqld --install mysql --defaults-file="my.ini"   **这里的my.ini文件路径建议用绝对路径而不是我这个相对路径**

5.初始化服务（为了生成data目录）
执行mysqld --initialize --user=mysql --console

打印形如：

    D:\Service\mysql57\bin>mysqld --initialize --user=mysql --console
    2015-12-20T08:13:45.264865Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
    2015-12-20T08:13:45.854579Z 0 [Warning] InnoDB: New log files created, LSN=45790
    2015-12-20T08:13:45.998772Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
    2015-12-20T08:13:46.098118Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 9755c3ea-a6f1-11e5-81a3-74d02b122fb3.
    2015-12-20T08:13:46.121617Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
    2015-12-20T08:13:46.135153Z 1 [Note] A temporary password is generated for root@localhost: g!gRw!d%M0Sj

记住最后的**root@localhost: g!gRw!d%M0Sj**，这个是初始的登录密码

6.启动服务
net start mysql

7.使用初始登录密码登录并修改密码
mysql -u root -p
Enter password:(输入初始密码)

登录成功之后
mysql> set password = password('your password');

8.安装workbench图形化界面（可选）
[下载地址](http://dev.mysql.com/downloads/workbench/)

完成配置。

## 用Connector/J驱动连接MySql及常用简单语句
Connector/J驱动是MySql官方的JDBC驱动，用于java中连接数据库。
[Download Connector/J](https://dev.mysql.com/downloads/connector/j/)
### 使用方法
1.导入../WEB-INF/lib中
2.在java文件中添加
```
import java.sql.*;
//databaseName为指定的数据库名
String URL = "jdbc:mysql://localhost/databaseName?autoReconnect=true&useSSL=false";
//加载驱动
Class.forName("com.mysql.jdbc.Driver");
//创建连接对象con
Connection con = DriverManager.getConnection(URL, "用户名", "密码");
```

注意在URL中添加**useSSL=false**，不然会出现warn：

    WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.

创建连接对象之后就可以调用各种语句了，基本和SQL SERVER是一个样子。

### 简单语句
1.查询语句
```
String sql = "select * from table";
Statement stmt = con.createStatement();
ResultSet rs = stmt.executeQuery(sql);
```

2.条件语句
```
String user = "felbry";
int age = 21;
sql+=" user = ? AND age = ?";
PreparedStatement ps = con.prepareStatement(sql);
ps.setString(1, user);
ps.setInt(2, age);
ResultSet rs = ps.executeQuery();
```

3.小结
```
executeQuery(),executeUpdate(),execute()
```

4.关闭连接
```
rs.close();
stmt.close();
ps.close();
con.close();
```

## 待定补充开发所遇问题

Good Night.