---
title: V2RayN简单使用教程
category:
  - v2ray
  - v2rayN
tags:
  - v2ray
  - v2rayN
abbrlink: 50d46b4d
date: 2018-04-02 14:19:25
---
> 由于 V2Ray 没有自带 GUI 配置界面，所以说客户端使用还是有一点不方便。而V2RayN 则是 windows下 平台一个对新手比较友好的 V2Ray GUI 客户端。

以下内容都是默认使用233blog的一键脚本安装为前提。

下面简单介绍下v2ray的简单使用：

##### 下载v2ray core

下载地址：[https://github.com/v2ray/v2ray-core/releases/latest](https://github.com/v2ray/v2ray-core/releases/latest)

选择 v2ray-windows-64.zip 下载，如果你的系统是 32 位的那就选择 v2ray-windows-32.zip。
下载完成，解压。

##### 下载 v2RayN

下载链接： [https://github.com/2dust/v2rayN/releases/latest](https://github.com/2dust/v2rayN/releases/latest)
然后选择 V2RayN.exe 下载
将下载完成后的 V2RayN.exe 复制到之前打开的 V2Ray 文件夹目录

##### 获取 v2Ray 客户端配置信息

使用233blog的脚本安装完之后会显示v2ray的配置信息，也可以ssh登录到vps上后，使用命令v2ray并按1来查看v2ray配置信息。

本次举例以 ws+tls+站点伪装 为例

配置信息如下：

![配置信息](https://ws1.sinaimg.cn/large/7512bce3ly1fpyedf21l4j20g40asweu.jpg)


##### v2ray导入v2ray配置

右键选择以管理员身份运行 V2RayN.exe 程序。

打开界面如下：

![界面01](https://ws1.sinaimg.cn/large/7512bce3ly1fpyesxsn2gj20b408gq3f.jpg)

注意：打开过程中，可能会提示是否允许联网的界面，全部勾选上并选择允许就行了。

选择 添加[VMess]服务器

![界面02](https://ws1.sinaimg.cn/large/7512bce3ly1fpyesxx2ucj206z0btta6.jpg)

按上面的配置进行填写
![界面03](https://ws1.sinaimg.cn/large/7512bce3ly1fpzfy0vdavj20kc0e9weq.jpg)

最后点击确定即可完成v2ray的配置。

---

##### v2rayN的其它配置

###### 自定义代理网址或IP
点击主界面的参数设置，在弹出的窗口中，选择Core:路由设置选项卡，在下方的文本框内输入需要使用代理访问的网址即可。

![自定义代理网址或IP](https://ws1.sinaimg.cn/large/7512bce3ly1fpzmcxtpqrj20eq0gnjre.jpg)

###### 设置GFWList地址

GFWList地址：

https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt

直接复制粘贴进去。

![GFWList](https://ws1.sinaimg.cn/large/7512bce3ly1fpzmcxx2n3j20ep0gm3yi.jpg)

右击系统托盘，选择更新PAC，注意，这里必须添加完代理才可以操作。

![更新PAC](https://ws1.sinaimg.cn/large/7512bce3ly1fpzmgbvrtgj205p07bmxr.jpg)

###### 快速添加代理

没有配置，只有别人分享的vmess链接，只需要点击主界面的服务器，添加VMess服务器，导入配置文件，并选择从剪贴板导入URL，所有的参数会自动填写完成。

![快速添加](https://ws1.sinaimg.cn/large/7512bce3ly1fpzfy0z25fj20kc0e8jro.jpg)


