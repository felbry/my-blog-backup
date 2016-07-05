title: 数据校验：Struts2
date: 2016-04-13 18:03:36
updated: 2016-04-22 16:14:21
comments: true
categories: TECH NOTE
tags:
- Struts2
- 数据校验
---
* 使用validate()方法校验
* 使用框架校验

<!-- more -->

## 使用validate()方法校验
**1.该方法写在action中，在execute方法前执行**
**2.通过判断语句，执行addFieldError方法**
    
    addFieldError("arg","msg");

## 使用框架校验
**为Action单独建立一个配置文件(.xml)，保存在Action类的相同目录下**
    
    命名规则：Action类名-validation.xml

#### xml文件的DOCTYPE声明
**有些旧的书籍或者文档没有及时更新，其声明如下**
```
<!DOCTYPE validators PUBLIC 
        "-//OpenSymphony Group//XWork Validator 1.0.2//EN"
        "http://www.opensymphony.com/xwork/xwork-validator-1.0.2.dtd">
```

    这样会导致文件找不到的错误。

* 解决方法1：查看最新文档

本地文档：
    
    file:///E:/struts-2.3.24.1/docs/docs/validation.html

添加声明如下：
```
<!DOCTYPE validators PUBLIC 
        "-//Apache Struts//XWork Validator 1.0.2//EN"
        "http://struts.apache.org/dtds/xwork-validator-1.0.2.dtd">
```

**文档里面也有validator的各种详细使用说明**

* 解决方法2：在myeclipse中查看jar包中的源码

**Step 1**
在**项目工程**上点击鼠标右键，选择Properties，在弹出的对话框中选择 Java Build Path -> Libraries -> 选择需要添加源代码的包下边的Source attachment，在弹出的对话框中指定**源码文件位置**

**Step 2**
确认之后即可在项目里面找到相关的Library

**Step 3**
在

> xwork-core-2.3.24.1.jar

包中，找到

> xwork-validator-1.0.2.dtd

即可在注释中看到DCOTYPE的相关说明
