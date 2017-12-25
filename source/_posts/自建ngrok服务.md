---
title: 自建ngrok服务
date: 2017-12-25 19:51:24
category: 
    - ngrok
    - linux
tags:
    - ngrok
    - linux
---
### 基于CentOS的自建ngrok服务

> ngrok 是一个反向代理，通过在公共的端点和本地运行的 Web 服务器之间建立一个安全的通道。ngrok 可捕获和分析所有通道上的流量，便于后期分析和重放。

安装ngrok服务之前需要先配置Go和Git环境

#### Install Go 1.5 On CentOS 6
下载编译好的二进制文件压缩包 go1.5.linux-amd64.tar.gz

```
cd /tmp
wget https://storage.googleapis.com/golang/go1.5.linux-amd64.tar.gz

如果提示证书错误或者无法下载，用下面这个命令试一下：
wget -r -np -nd --accept=gz --no-check-certificate https://storage.googleapis.com/golang/go1.5.linux-amd64.tar.gz
```

解压下载的二进制压缩包到  /usr/local

```
tar -C /usr/local -xvzf /tmp/go1.5.linux-amd64.tar.gz
```

修改环境变量 /etc/profile

```
echo "export PATH=\$PATH:/usr/local/go/bin" >> /etc/profile
source /etc/profile
```

验证是否安装成功

```
go version
go version go1.5 linux/amd64      # 显示版本号 说明安装成功
```

Go1.5 安装结束

---

#### Install Git 2.5.0 On CentOS 6

安装依赖包

```
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel -y
```

下载源码包 git-2.5.0

```
cd /tmp
wget https://www.kernel.org/pub/software/scm/git/git-2.5.0.tar.gz

如果提示证书错误或者无法下载，用下面这个命令试一下：
wget -r -np -nd --accept=gz --no-check-certificate wget https://www.kernel.org/pub/software/scm/git/git-2.5.0.tar.gz
```
解压 编译 安装
```
tar xvzf /tmp/git-2.5.0.tar.gz
cd git-2.5.0
./configure --prefix=/usr/local/git
make
make install
```
配置环境变量并验证是否安装成功
```
echo "export PATH=\$PATH:/usr/local/git/bin" >> /etc/profile
source /etc/profile

git --version
git version 2.5.0      # 显示版本号 说明安装成功
```

Git2.5.0 安装结束

---

#### Install ngrok 2.x On CentOS 6

获取ngrok源码
```
cd /usr/ngrok       #这里改为自己的安装目录
git clone https://github.com/inconshreveable/ngrok.git
```

生成自签名ssl证书 "51ngrok.cn" 替换为你自己的域名

```
cd /tmp/ngrok

openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=51ngrok.cn" -days 5000 -out rootCA.pem
openssl genrsa -out device.key 2048
openssl req -new -key device.key -subj "/CN=51ngrok.cn" -out device.csr
openssl x509 -req -in device.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt -days 5000
cp rootCA.pem assets/client/tls/ngrokroot.crt
cp device.crt assets/server/tls/snakeoil.crt
cp device.key assets/server/tls/snakeoil.key
```

设置变量并生成ngrok服务端和客户端
```
GOOS=linux GOARCH=amd64  #如果是32位系统，这里 GOARCH=386
make release-server release-client 
```

> 编译之后，就会在ngrok源码的bin目录下生成两个可执行文件：ngrokd、ngrok。其中ngrokd就是ngrok的服务端程序，ngrok就是ngrok的客户端程序。由于现在生成的客户端ngrok只能在linux下运行，因此如果想要生成windows下的客户端程序，需要继续进行交叉编译。

##### 交叉编译生成windows客户端

```
cd  /tmp/ngrok/

GOOS=windows GOARCH=amd64 make release-server release-client
```

#### ngrokd服务启动

```
cd  /usr/ngrok/bin

./ngrokd -domain="51ngrok.cn" -httpAddr=":8080" #因为服务器上还有其他网站，这里选择端口为8080
```
推荐使用screen一个新连接来执行，或者使用nohup / &后台运行。

#### 设置nginx的端口转发
修改服务器nginx安装路径下，conf文件夹下的nginx.conf文件，添加如下内容

```
server {
    listen 80;
    server_name *.51ngrok.cn;
    location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host:8080;
        proxy_set_header X-Nginx-Proxy true;
        proxy_set_header Connection "";
        proxy_pass http://127.0.0.1:8080;
    }
}
```