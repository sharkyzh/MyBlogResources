---
title: CentOS7安装Nginx与DotNetCore环境
date: 2018-02-24 09:22:49
category:
    - .net core
tags:
    - .net core
    - centos
---

#### 安装 dotnet core环境

1. 添加 dotnet 产品源
```
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo sh -c 'echo -e "[packages-microsoft-com-prod]\nname=packages-microsoft-com-prod \nbaseurl= https://packages.microsoft.com/yumrepos/microsoft-rhel7.3-prod\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/dotnetdev.repo'
```

2.安装 .NET SDK

```
sudo yum update
sudo yum install libunwind libicu
sudo yum install dotnet-sdk-2.1.4
```

#### 安装nginx
1.添加 Nginx 仓库

```
sudo yum install epel-release
```

2. 安装 Nginx

```
sudo yum install nginx
```

3. 启动 Nginx

```
sudo systemctl start nginx
```
4. 防火墙允许HTTP与HTTPS传输

```
sudo firewall-cmd --permanent --zone=public --add-service=http 
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload
```

5. Nginx 开机启动

```
sudo systemctl enable nginx
```


---
参考文章：
1. https://www.microsoft.com/net/learn/get-started/linuxcentos
2. https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-centos-7