title: 'JSP基于Servlet的MVC基础结构演示（IDE:JAVA EE）'
date: 2016-09-05 22:29:01
comments: true
categories: TECH NOTE
tags:
- JSP
- mvc
- demo

---
* 安装与配置JSP运行环境
* mvc模式的tree结构与注意事项
* 一个基本注册功能的Demo
* 进阶

<!-- more -->

## 安装与配置JSP运行环境
### 安装JDK和配置环境变量
### 安装Tomcat服务器

## mvc模式的tree结构与注意事项
> 在**JAVA EE**中通过``new > Dynamic Web Project``创建一个项目工程。

### tree结构

```
│
├─build
│  └─classes
│      ├─bean
│      │      Users.class
│      └─servlet
│             Register.class
│
├─src
│  ├─bean
│  │      Users.java
│  │
│  └─servlet
│         Register.java
│
└─WebContent
    │  register.jsp
    │  success.jsp
    │
    ├─META-INF
    │      MANIFEST.MF
    └─WEB-INF
        │  web.xml
        │
        └─lib
```

### 注意事项
**1.将``servlet-api.jar``包放进JRE中。这个包在安装完tomcat后，在其根目录的``lib``文件夹下；
2.注意``web.xml``文件是在``WEB-INF``文件夹下，而不是``lib``文件夹下。否则会导致请求页面时出现the request source is not avaliable错误。
**

## 一个基本注册功能的Demo
### 配置文件
**web.xml**
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app>
  <servlet>
    <servlet-name>registerExample</servlet-name>
    <servlet-class>servlet.Register</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>registerExample</servlet-name>
    <url-pattern>/register</url-pattern>
  </servlet-mapping>
</web-app>
```
### jsp页面
**register.jsp**
```
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8" %>
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Register</title>
</head>
<body>
	<form action="register" method="post">
		username:<input type="text" name="username">
		password:<input type="text" name="password">
		<br><input type="submit" value="提交">
	</form>
</body>
</html>
```
**success.jsp**
```
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8" %>
<jsp:useBean id="userInstance" type="bean.Users" scope="request" />
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Register Successfully</title>
</head>
<body>
	Register Successfully.<br>
	Your username:<jsp:getProperty property="username" name="userInstance"/><br>
	Your password:<jsp:getProperty property="password" name="userInstance"/>
</body>
</html>
```
### servlet和bean
**Register.java**
```
package servlet;
import bean.*;
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;
public class Register extends HttpServlet{

	public void init(ServletConfig config) throws ServletException{
		super.init(config);
	}
	public void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException,IOException{
		String name = request.getParameter("username"),
			   pass = request.getParameter("password");

		Users user = new Users();
		user.setUsername(name);
		user.setPassword(pass);

		request.setAttribute("userInstance", user);	// request周期的Javabean

		RequestDispatcher dispatcher=request.getRequestDispatcher("success.jsp");
		dispatcher.forward(request,response);
	}

}
```
**Users.java**
```
package bean;

public class Users {
	String username, password;

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

}
```

## 进阶
### 通过Ajax实现数据的发送和接收
> 将``form``表格中的``action``取消，改用``url``

#### 发送数据
**register.jsp**
```
<form>
		用户名：<input type="text" name="username"><br>
		密码：<input type="text" name="password"><br>
</form>
<!-- input标签置于form表格外边是因为如果放在里边当点击时就会刷新页面，从而取不到表格中的值 -->
<input type="submit" value="提交">
<script src="https://code.jquery.com/jquery-1.9.0.min.js" integrity="sha256-f6DVw/U4x2+HjgEqw5BZf67Kq/5vudRZuRkljnbF344=" crossorigin="anonymous"></script>
<script>
	$(function(){
		$('input[type="submit"]').click(function(){
			var user = $('input[name="username"]').val(),
			    pass = $('input[name="password"]').val();
			$.post('register', {username: user, password: pass}, function(data){
				console.log(data);  // String person
			})
		})
	})
</script>
```
#### 服务端接收和返回
**Register.java**
> 接收还是按原来的方式接收，返回则是取消``RequestDispatcher``对象，改用``response``对象返回数据

```
String person = username + ', ' + password;
response.getWriter().write(person);
response.getWriter().close();
```
#### 通过JSON数据进行传输
> 上述过程中，浏览器端发送数据时发送一个JSON对象，在服务端通过``request.getParameter("attributeName")``取到对应的值。但是一个一个取值显然比较麻烦；而且返回的是String类型，当需返回多个数据时，就不是很好处理。接下来我们通过``json-lib``包，使用JSON数据传输来优化上述过程。

**一、客户端（浏览器端）发送数据**
> 主要是将数据封装成一个json对象，然后转换为一个字符串传递给服务端

```
var userInfo = {
			username: user,
			password: pass
		}
var userInfoStr = JSON.stringify(userInfo);
$.post('register', {userInfomation: userInfoStr}, function(data){
	console.log(data);
	console.log(typeof data);
})
```

**二、服务端接收并返回数据**
**1.服务端解析JSON数据传值给指定的bean对象在这里可以看到：
  [struts2中利用struts2-json-plugin实现json数据交换](https://felbry.github.io/2016/07/05/struts2%E4%B8%AD%E5%88%A9%E7%94%A8struts2-json-plugin%E5%AE%9E%E7%8E%B0json%E6%95%B0%E6%8D%AE%E4%BA%A4%E6%8D%A2/)**
```
String userInfo = request.getParameter("userInfomation");
JSONObject jo = JSONObject.fromObject(userInfo);
Users user = (Users) JSONObject.toBean(jo, Users.class);
```

**2.服务器返回JSON数据**
> 创建一个JSONObject对象，通过``put()``方法添加键值对，指定返回头中的MIME类型为``application/json``，然后返回给客户端。

```
JSONObject json = new JSONObject();
json.put("username", user.getUsername());
json.put("password", user.getPassword());

response.setContentType("application/json;charset=utf-8");
response.getWriter().write(json.toString());
response.getWriter().close();
```
**注：``write()``方法不接受JSONObject对象参数，故需转换为字符串。由于我们设置了MIME类型，所以字符串会自动转换为JSON格式，也可以通过在浏览器端用``JSON.parse()``方法解析字符串。**
