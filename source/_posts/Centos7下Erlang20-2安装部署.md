---
title: Centos7下Erlang20.2安装部署
category:
  - centos
tags:
  - centos
abbrlink: 3909ad56
date: 2018-09-21 14:22:49
---
##### 前言
本篇文章适用范围？

```
Erlang 17.0+
CentOS 7
```

环境信息?

```
CentOS 7.X
Erlang 20.2
```

##### 编译安装
安装Erlang编译安装必要依赖

```
#安装必要依赖
sudo yum install -y gcc gcc-c++ glibc-devel make ncurses-devel openssl-devel autoconf java-1.8.0-openjdk-devel git
```

下载Erlang源码
下载地址：https://www.erlang.org/downloads


```
#进入下载目录
cd /home/download
#下载
wget http://erlang.org/download/otp_src_20.2.tar.gz
```

解压

```
tar -zvxf otp_src_20.2.tar.gz
```
 <!-- more -->
 
编译&安装

```
#进入根目录
cd otp_src_20.2.tar.gz
#编译&安装
./otp_build autoconf
./configure && make && sudo make install
```
验证

```
#进入erlang命令行表示成功
erl
```

##### YUM安装
创建Yum源

```
#创建yum源
sudo vi /etc/yum.repos.d/rabbitmq-erlang.repo
```

```
#文件内容
[rabbitmq-erlang]
name=rabbitmq-erlang
baseurl=https://dl.bintray.com/rabbitmq/rpm/erlang/20/el/7
gpgcheck=1
gpgkey=https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc
repo_gpgcheck=0
enabled=1
安装
sudo yum install -y erlang
验证
#进入erlang命令行表示成功
erl
```

##### 备注
> 
> https://github.com/erlang/otp/blob/maint/HOWTO/INSTALL.md
> 
> https://github.com/rabbitmq/erlang-rpm
> 
> https://zfanw.com/blog/install-erlang-on-centos-7.html