---
title: IOS下使用v2ray之简单教程
category:
  - 翻山越岭
  - v2ray
tags:
  - v2ray
  - IOS
abbrlink: c4024172
date: 2018-03-14 16:20:14
---
> 上次收集了一些v2ray的一键脚本，有些朋友问我在IOS上如何使用，本编内容就介绍下IOS下v2ray常用客户端的使用教程。

#### 服务端配置
新手推荐使用233blog的一键脚本来配置v2ray。
可参考我[上篇博客文章](https://blog.sharkyzh.cn/post/a5cb4d7f.html)，也可直接访问[233Blog](https://233blog.com/post/28/)获取一键脚本。

---
以下内容都是默认使用233blog的一键脚本安装为前提。

#### 客户端
V2Ray 官方没有 iOS 客户端，第三方客户端有：kitsunebi，shadowrocket，Pepi (ShadowRay)。
#### kitsunebi

kitsunebi目前对 V2Ray 支持最完全的应用，支持所有 vmess 链接使用方。

##### 一、扫码配置（推荐）

使用脚本安装完成后会提示生成二维码链接，打开链接就可以得到二维码，直接扫码配置即可，注意，需要手动输入AlterId。
如果安装后忘记生成二维码链接，可以输入命令
```
v2ray qr
```
生成二维码链接后再进行扫码操作。

**再次提醒：需要手动输入AlterId。**

![image](https://ws1.sinaimg.cn/large/7512bce3ly1fpc7qwgeg5j20af0c474d.jpg)

 <!-- more -->
 
##### 二、手动输入配置

1. 点击右上角加号，进入配置填写页面。

依次填入地址，端口，UUID，选择加密方式，填写AlterId

![image](https://ws1.sinaimg.cn/large/7512bce3ly1fpcccru7d5j20af0esaa7.jpg)

2. 加密方式有如下几种，根据自己在服务器上的配置进行选择

![image](https://ws1.sinaimg.cn/large/7512bce3ly1fpcejy8g7mj20af0680sm.jpg)

3. 传输协议：

> 传输协议分为tcp,kcp与ws(websocket)。

###### tcp最简单，直接选择tcp协议就行了，无需其它配置。

###### kcp传输协议

KCP则需要选择伪装头部，当然也可以不进行伪装，以服务器配置为准进行选择。

![image](https://ws1.sinaimg.cn/large/7512bce3ly1fpcenmaajzj20af0ijt8r.jpg)

我的配置如下

![kcp伪装](https://ws1.sinaimg.cn/large/7512bce3ly1fpcetz2wjsj20af0fhglr.jpg)


###### ws传输协议

ws则需要填写ws路径与ws host，如果是HTTPS还需要选择TLS。我的配置（ws+站点伪装+tls）如下，可以进行参考。
![ws+站点伪装+tls](https://ws1.sinaimg.cn/large/7512bce3ly1fpcerd4ow1j20af0ea0sv.jpg)


---


#### Shadowrocket
小火箭大家应该都知道，对于v2ray缺点就是不支持kcp,并且不支持扫码添加v2ray配置（修正，只支持小火箭自己导出的二维码）。

##### 手动输入配置

ws+tls+站点伪装

![ws+tls+站点伪装](https://ws1.sinaimg.cn/large/7512bce3ly1fpcf1cciunj20ku11275d.jpg)

如果不需要站点伪装，直接把最下面的输入框留空就行了。