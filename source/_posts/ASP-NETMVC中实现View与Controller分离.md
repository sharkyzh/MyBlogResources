---
title: ASP.NETMVC中实现View与Controller分离
date: 2017-12-25 22:34:42
category:
    - .net
    - mvc
tags:
    - .net
    - mvc
---
1. 新建一个空的MVC项目


命名为:sharkyzh.Mvc01。如何新建在此不作一一介绍，
如果不分离的话，我们就开始在该项目下的Controllers文件夹添加Controller，但是我们现在的作法是将Controller作为一个单独的项目，这个项目仅仅用来做View的操作，那究竟怎么实现呢？

2. 新建一个类库

sharkyzh.Mvc01.Controllers 

3. 添加引用

在此类库中添加**System.Web.dll,System.Web.Abstractions.dll,System.Web.Mvc.dll，System.Web.Routing.dl**的引用

4. 新建GlobalRoutesTable类，用来注册路由

通过该类衔接Controler与View，这个类会在View的Global.asax中Application_Start使用到。
按照国际惯例上代码：
```
public static class GlobalRoutesTable
{
     public static void RegisterRoutes(RouteCollection routes) {
        routes.IgnoreRoute("{resource}.axd/{*pathInfo}"); 
        //default   routes
        routes.MapRoute(
            "Default",
            "{controller}/{action}/{id}",
            new { controller = "Home", action = "Index", id = UrlParameter.Optional },
            new string[] {"sharkyzh.Mvc01.Controllers"}
        ); 
    }
}
```

5. 新建类HomeController类

该类继承System.Web.Mvc.Controller
```
public class HomeController:Controller {
    public ViewResult Index() {
        return View();
    }
}
```

6. 将现有项目 sharkyzh.Mvc01.Controllers.dll添加到 sharkyzh.Mvc01项目中，然后在global.asax中的Application_Start中加入代码:
sharkyzh.Mvc01.Controllers.GlobalRoutesTable.RegisterRoutes(RouteTable.Routes);

7. 在 sharkyzh.Mvc01 项目中的Views下新建Home文件夹,home文件夹下建index.cshtml 视图，然后Ctrl+F5运行项目，我们就可以看到成功运行的结果了。

---
可能大家会想到，如果在Controllers中建文件夹，这样更有利于层次分明，那该怎么实现呢？
博客园创始人@dudu的解决方案中提到了用Area来解决，然后到最后他采纳了评论中的配置路由的方案，经过我的测试，这中方案测试不通过，所以还是采用Area的方式来实现。我们紧接上面的内容
1、在sharkyzh.Mvc01.Controllers项目中新建文件夹Admin
2、在Admin文件夹下新建AdminAreaRegistration类，该类继承AreaRegistration
3、在类中插入如下代码：
```
public class AdminAreaRegistration : AreaRegistration {
    public override string AreaName {
        get {
            return "Admin";
        }
    }
    public override void RegisterArea(AreaRegistrationContext context) {
        context.MapRoute(
            "Admin_default",
            "Admin/{controller}/{action}/{id}",
            new { controller = "Home", action = "Index", id = UrlParameter.Optional },
            new string[] {"NaoGuaZi.MvcExample12.Controllers.Admin"}
        );
     }
}
```
4、新建HomeController类继承Controller
```
public ViewResult Index() {
    return View("~/Views/Admin/Home/Index.cshtml");
}
```
5、在 sharkyzh.Mvc01 项目中的Views下新建Admin/Home文件夹，新建名为Index.cshtml视图。然后Ctrl+F5运行，在浏览器中输入http://localhost:端口号/admin/home/index即可成功访问。