---
title: openssl将私钥和crt证书合成pfx证书
date: 2017-12-25 22:03:25
category: 
    - https
tags:
    - https
    - 证书
---

在IIS配置ssl的时候，安装服务器证书，发现安装后，刷新下证书就消失了，在编辑绑定的时候，可选证书中也没有安装的证书。

原因是我的证书文件的证书申请并不是在我的服务器上申请的，因此只安装crt证书会出现这种情况。

解决此问题的办法是利用openssl将私钥和服务器crt证书合成.pfx证书，然后在IIS中导入该pfx证书即可。
 <!-- more -->
### 步骤
#### 下载安装openssl 
1. 这里我们直接使用安装包进行安装（也可自己编译），选择对应版本进行下载下载。
2. 运行安装程序Win64OpenSSL-1_0_2e.exe，将openssl安装到C:\OpenSSL-Win64。
3. 安装完成后，可以在控制台运行如下指令，来测试是否安装成功。 
```
openssl version
```
4. 如果返回openssl版本号（”OpenSSL 1.0.2e 3 Dec 2015”），则表示安装成功。

#### 合成.pfx证书
1. 将私钥文件（server.key）和服务器crt证书文件（server.crt ），放到openssl安装目录的bin目录下。
2. 控制台也进到此目录下，然后执行下面指令。 
```
openssl pkcs12 -export -out server.pfx -inkey server.key -in server.crt
```
3. 然后再看bin目录下面多了一个server.pfx 文件，即为生成的pfx证书文件。

以上，将pfx导入到IIS后，就可以进行绑定了。