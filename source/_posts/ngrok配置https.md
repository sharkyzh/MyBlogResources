---
title: ngrok配置https
date: 2018-01-30 17:19:10
category:
    - ngrok
tags:
    - ngrok
    - https
---

> 最近团队要做微信小程序的开发，需要用到https，以前搭建的ngrok由于使用的是自己签发的证书，所以网址栏会显示大大的红叉，对于强迫症的人实在是难受，搜索了网上的教程及结合自己的摸索，现记录下来，方便后人能方便的配置。

#### 第一部分 申请AlphaSSL 泛域名证书

因为使用的是4级域名，并且域名不可确定，所以需要申请泛域名证书。本次申请的是免费的泛域名证书。

##### 准备域名邮箱

由于证书验证需要域名邮箱作验证，所以首先需要准备好接收邮件的域名邮箱。

域名邮箱可以选择 QQ 域名邮箱，很简单方便，也有操作指南，设置好 MX 解析后用另一个邮箱给域名邮箱发一封邮件测试无误即可。目前 QQ 邮箱并没有屏蔽 AlphaSSL 发来的邮件，因此可以放心使用。必须使用下面的 用户名@域名 邮箱之一才能接收到验证邮件：

```
admin@your-domain.com
administrator@your-domain.com
hostmaster@your-domain.com
postmaster@your-domain.com
webmaster@your-domain.com
```
具体步骤自己摸索下,这里就不细说了。

如果有自己的企业邮箱，也是可以使用的，但是一定要添加上面格式的邮箱账户才可收邮件，谨记！

QQ企业邮箱地址：[https://exmail.qq.com](https://exmail.qq.com)


##### 准备CSR 文件

AlphaSSL 证书的申请，需要先提交域名的 CSR（证书请求文件），然后用域名邮箱作验证，保证你是这个域名的所有者。CSR 很好生成，可以用 openssl 在本地生成，也可以去各大工具网站在线生成。

本次使用[https://www.chinassl.net/ssltools/generator-csr.html](https://www.chinassl.net/ssltools/generator-csr.html)来生成CSR文件。

填写完信息后，最好勾选上发送生成的CSR文件到上面输入的邮箱。

> 注意：域名这里填写*.tunnel.51ngrok.cn，大家根据自己的域名填写。一定保存好生成 CSR 文件时同时生成的 Key 文件，部署证书时要用。

##### 域名的准备工作

进入申请证书页面[https://en.assl.space/](https://en.assl.space/)

申请证书的页面需要你填写一个 Email Address.

这里最好填写域名的联系人邮箱（在域名提供商那里可以查询得到的），这是接收证书的邮箱，填写其它邮箱可能会不能接收到邮件，一定要注意。

另外最好关闭域名保护与清除域名的CNAME记录，防止AlphaSSL无法校验域名信息。


##### 收到邮件后的工作

同意申请链接只能点击一次

在域名邮箱收到验证邮件之后，点击 I Approve，AlphaSSL 便会把证书的 CRT 以邮件内容的形式发送给你，注意，I Approve 这个按钮只能点击一次，点击后不管有没有收到邮件，都会失效。因此接收证书的邮箱一定要填对，确保可以收到 CRT。

##### 合并证书

在收到的邮件最下方就是你申请的域名证书。

新建一个文本文件，把邮件中的域名证书内容粘贴进去，这里要注意的是需要包含 BEGIN 这行。

然后到AlphaSSL网站[https://www.alphassl.com/support/install-root-certificate.html](https://www.alphassl.com/support/install-root-certificate.html)把中级证书也到粘贴到上面内容的后面。

文本文件后缀改为.crt的证书文件。这个crt文件是可以直接部署到服务器上，并且不会因为证书链不完整而被浏览器报错。

---

#### 第二部分 把证书编译进ngrok

##### 准备证书

在[AlphaSSL官网](https://www.alphassl.com/support/install-root-certificate.html)下载Root CA，并重命名为ngrokroot.crt


快速下载地址：[点我下载](https://www.alphassl.com/support/roots/root.pem)

把上面合成的证书重命名为snakeoil.crt

把申请证书时生成的私钥key文件重命名为snakeoil.key


> 把ngrokroot.crt拷贝到assets/client/tls/文件夹下覆盖原来的文件
> 把snakeoil.crt拷贝到assets/server/tls/文件夹下覆盖原来的文件
> 把snakeoil.key拷贝到assets/server/tls/文件夹下覆盖原来的文件
> 拷贝snakeoil.key到bin文件夹内
> 拷贝snakeoil.crt到bin文件夹内


##### 编辑ngrok服务端与客户端

###### 编译生成ngrok服务端
```
#如果是32位系统，这里 GOARCH=386
GOOS=linux GOARCH=amd64  
make release-server release-client
```

> 编译之后，就会在ngrok源码的bin目录下生成两个可执行文件：ngrokd、ngrok。其中ngrokd就是ngrok的服务端程序，ngrok就是ngrok的客户端程序。由于现在生成的客户端ngrok只能在linux下运行，因此如果想要生成windows下的客户端程序，需要继续进行交叉编译。

###### 交叉编译生成windows客户端

```
#如果是32位系统，这里 GOARCH=386
GOOS=windows GOARCH=amd64
make release-server release-client
```

##### 运行ngrok服务端

```
./ngrokd -domain="tunnel.51ngrok.cn" -httpAddr=":8080" -httpsAddr=":8081" -tlsKey="snakeoil.key" -tlsCrt="snakeoil.crt"
```
推荐使用screen管理！


#### 第三部分 配置nginx

修改nginx配置文件，添加如下内容


```
server {
  listen 80;
  listen 443;
  server_name *.tunnel.51ngrok.cn;
  location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host:8080;
    proxy_set_header X-Nginx-Proxy true;
    proxy_set_header Connection "";
    proxy_pass http://127.0.0.1:8080;
  }
  ssl on;
  ssl_certificate /usr/ngrok/bin/51ngrok.crt;
  ssl_certificate_key /usr/ngrok/bin/51ngrok.key;
  ssl_session_timeout  5m;
}
```
