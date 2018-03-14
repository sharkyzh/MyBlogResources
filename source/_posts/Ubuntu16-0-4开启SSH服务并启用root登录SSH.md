---
title: Ubuntu16.0.4开启SSH服务并启用root登录SSH
abbrlink: 3f6688cf
date: 2018-03-14 18:43:58
category:
    - Linux
    - ssh
tags:
    - Linux
    - ssh
---

#### 开启SSH服务

1. 安装openssh-server

在终端中输入: 

```
sudo apt-get install openssh-server
```

2. 查看查看ssh服务是否启动

在终端中输入

```
sudo ps -e | grep ssh
```
如果显示有sshd，则说明ssh服务已经启动，如果没有启动，则输入以下命令来启动ssh

```
sudo service ssh start
```
启动完成后可ssh至服务器。

---

#### 启用root登录SSH

1. 使用ubuntu用户登录，密码为服务器配置时设置的密码，可在重置密码中修改
2. 修改 root 密码
```
sudo passwd root
```
3. 修改配置文件

```
sudo vi /etc/ssh/sshd_config
```

找到Authentication下面的配置：
```
# Authentication:
LoginGraceTime 120
PermitRootLogin prohibit-password
StrictModes yes
```
把PermitRootLogin源行注释，并增加一行为：
```
#PermitRootLogin prohibit-password  
PermitRootLogin yes  
```

4. 重启ssh

```
sudo service ssh restart  
```
接下来就可以用XShell以root身份登录了。