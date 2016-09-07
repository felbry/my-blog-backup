title: Struts2的拦截器小记
date: 2016-04-06 21:40:14
updated: 2016-04-22 16:12:14
comments: true
categories: TECH NOTE
tags:
- Struts2
- 拦截器
---
**针对一道作业题对“拦截器实现”做点笔记**
## 题目如下

> 建立一个拦截器，用户名后缀为“r”的才允许访问“/sec”命名空间下的action。

### 思路大概如下
之前是通过form表单直接将数据提交给action，而拦截器无非就是在中间插了一脚。那么将对后缀“r”的判断丢给拦截器处理就可以了。

<!-- more -->

### 步骤
1. 在struts.xml中定义一个包，包的命名空间设为“/sec”；
2. 包中定义一个name为“test”的拦截器（指定类路径），然后将其与defaultStack拦截器合并成一个拦截器栈；
3. 最后将这个拦截器栈设为默认拦截器。

**struts.xml结构如下**

``` bash
<package name="default" extends="struts-default" namespace="/sec">
	<interceptors>
	    	<interceptor name="test" class="intercept.SimpleInterceptor">
	    	</interceptor>
	    	<interceptor-stack name="mystack">
	    		<interceptor-ref name="defaultStack"></interceptor-ref>
	    		<interceptor-ref name="test"></interceptor-ref>
	    	</interceptor-stack>
    	</interceptors>
    	<default-interceptor-ref name="mystack"></default-interceptor-ref>
</package>
```
**然后将action加入到包中，结构如下**
```
<package name="default" extends="struts-default" namespace="/sec">
    	<interceptors>
	    	<interceptor name="test" class="intercept.SimpleInterceptor">
	    	</interceptor>
	    	<interceptor-stack name="mystack">
	    		<interceptor-ref name="defaultStack"></interceptor-ref>
	    		<interceptor-ref name="test"></interceptor-ref>
	    	</interceptor-stack>
    	</interceptors>
    	<default-interceptor-ref name="mystack"></default-interceptor-ref>
	//加入action
    	<action name="login" class="intercept.Login">
    		<result name="success">success.jsp</result>
    		<result name="notR">notR.jsp</result>
    	</action>
</package>
```
**接下来对拦截器SimpleInterceptor.java定义**
```
package intercept;
import java.util.*;
import org.apache.struts2.ServletActionContext;
import com.opensymphony.xwork2.*;
import com.opensymphony.xwork2.interceptor.*;
public class SimpleInterceptor extends AbstractInterceptor {
	public String intercept(ActionInvocation arg0) throws Exception{
		Login log = (Login) arg0.getAction();
		String user = ServletActionContext.getRequest().getParameter("username");
		if(user.charAt(user.length() - 1) == 'r')
			return arg0.invoke();
		else
			return "notR";
	}
}
```
### 拦截器“接收参数”补充说明：
**这里采用的是**`ServletActionContext.getRequest().getParameter("username");`
**注意在开头要导入包**`import org.apache.struts2.ServletActionContext;`
### 这里有个疑惑不解，书上讲可以通过session的方法来取值，如下
**如果要用session方法，开头声明**`import java.util.*;`
**在action中添加如下语句：**
```
		ActionContext ctx = ActionContext.getContext();
		Map session = ctx.getSession();
		session.put("user", username);
```
**在拦截器中添加如下语句：**
```
		ActionContext ctx = arg0.getInvocationContext();
		Map session = ctx.getSession();
		user = (String) session.get("user");
```
**最后得到参数值之后在拦截器里面做判断**
```
if(user.charAt(user.length() - 1) == 'r')
	return arg0.invoke();
else
	return "notR";
```
**在此出现的问题有：**
1. `arg0.invoke()`**是执行action语句，但是它是在if判断语句里，如果没有执行，session里面是怎么存值的？**
**我尝试运行了几次，每次把user变量打印出来，其结果都是null，自然返回“notR”**
2. **如果将**`arg0.invoke()`**放在之前运行一次，那样的话好像就不会执行if else语句了，所以每次都会返回SUCCESS，跳转到成功界面**

**所以目前还是不理解怎么通过session往拦截器里面传递参数并判断**

**说到判断，还尝试了equals方法和正则表达式matches方法，结果不太理想，不知是语法错误还是怎样**

**嗯，一个伪拦截器大概就是如此吧**

### 补充两点
1. **JSP页面给具有命名空间的action传值：**`<s:form action="login" namespace="/sec">`
2. **通过命名空间action指定的页面，放在“WebRoot/命名空间name”目录下**
