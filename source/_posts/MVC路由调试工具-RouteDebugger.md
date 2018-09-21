---
title: MVC路由调试工具-RouteDebugger
category:
  - .net
  - mvc
tags:
  - .net
  - mvc
abbrlink: 2e35e381
date: 2018-06-28 11:55:05
---

#### 1.获取方式
#### 第一种方法：
在程序包控制台中执行命令:

    Install-Package routedebugger
安装成功后Web.config文件中会自动加入行

    <add key="RouteDebugger:Enabled" value="true" />

##### 第二种方法：
下载之后，解压之后得到RouteDebugger.dll，引用到web项目中。
手动在web.config中加入

    <add key="RouteDebugger:Enabled" value="true" />

#### 2.使用
##### 1.对于.NET3.5和MVC3之前的项目，如果要使用RouteDebugger，还需要在Application_Start中注册。

     protected void Application_Start(object sender, EventArgs e) 
     {
        RegisterRoutes(RouteTable.Routes);
        RouteDebug.RouteDebugger.RewriteRoutesForTesting(RouteTable.Routes);
     }

如果是MVC3、MVC4、MVC5，则无需在Application_Start中注册。

##### 2.做完上述工作后，访问页面即可以跟踪到路由。

##### 3.禁用/启用

在web.config中操作按照下面的配置节进行修改

    <!--禁用-->
    <add key="RouteDebugger:Enabled" value="false" />
    <!--启用-->
    <add key="RouteDebugger:Enabled" value="true" />
##### 4.使用效果图：
![routeDebugger](https://ws1.sinaimg.cn/large/7512bce3ly1fqk3i4ts5hj20zl0rd3zt.jpg)