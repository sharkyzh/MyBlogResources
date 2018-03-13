---
title: 'MVC中如何在Views目录下存放css,js,html文件'
category:
  - .net
  - mvc
tags:
  - .net
  - mvc
abbrlink: e1f81db1
date: 2018-01-11 12:00:00
---

> 去年做项目的时候，用到换换肤的技术，但是模板放在View目录后，css，js文件不能访问。
> 参考了下博客园的文章，把问题解决了。现在把解决方法总结一下。

#### 如何配置才能访问css，js文件

默认情况下Views目录下是不能访问除了视图文件之外的文件的
在Views目录下的Web.config中添加如下节点信息

```
<system.webServer>  
    <validation validateIntegratedModeConfiguration="false" />  
    <handlers>  
        <remove name="BlockViewHandler"/>  
        <add name="js" verb="GET,HEAD" path="*.css" type="System.Web.StaticFileHandler" />  
        <add name="css" verb="GET,HEAD" path="*.css" type="System.Web.StaticFileHandler" />  
        <add name="jpg" verb="GET,HEAD" path="*.jpg" type="System.Web.StaticFileHandler" />  
        <add name="png" verb="GET,HEAD" path="*.png" type="System.Web.StaticFileHandler" />  
        <add name="js" verb="GET,HEAD" path="*.js" type="System.Web.StaticFileHandler" />  
        <add name="BlockViewHandler" path="*" verb="*" preCondition="integratedMode" type="System.Web.HttpNotFoundHandler" />  
    </handlers>  
</system.webServer>  
```

#### 如何配置才能访问静态页面
在Views目录下的Web.config中添加如下节点信息


```
<httpHandlers>  
  <add verb="GET,HEAD" path="*.html" type="System.Web.StaticFileHandler"/>  
  <add path="*" verb="*" type="System.Web.HttpNotFoundHandler"/>  
</httpHandlers>  
```
