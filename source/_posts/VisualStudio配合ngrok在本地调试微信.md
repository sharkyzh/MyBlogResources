---
title: VisualStudio配合ngrok在本地调试微信
date: 2017-12-25 22:07:56
category:
    - ngrok
tags:
    - ngrok
    - 微信调试
---

1. 打开IIS Express 的配置文件：

```
C:\Users\你的用户名\Documents\IISExpress\config\applicationhost.config
```

2. 对要添加外部访问的网站作修改：

```
<site name="XXY.WX" id="54">
    <application path="/" applicationPool="Clr4IntegratedAppPool">
        <virtualDirectory path="/" physicalPath="D:\NET\XXY.WX\XXY.WX"/>
    </application>
    <bindings>
        <binding protocol="http" bindingInformation="*:43362:localhost"/>
        <binding protocol="http" bindingInformation="*:43362:127.0.0.1" />
    </bindings>
</site>
```

3. 以管理员运行 CMD
执行下面的命令
```
netsh http add urlacl url=http://127.0.0.1:35201/ user=everyone
```
其中35201是你程序运行的端口号