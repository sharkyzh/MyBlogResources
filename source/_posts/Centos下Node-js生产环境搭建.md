---
title: Centos下Node.js生产环境搭建
date: 2017-12-23 09:33:18
category: 
    - linux
    - node.js
tags: 
    - node.js 
    - linux
---

#### 安装依赖

```
sudo yum install vim openssl build-essential libssl-dev wget curl git
```

#### 安装NVM
NVM是管理node版本的工具，详情[点我查看](https://github.com/creationix/nvm)

```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.5/install.sh | bash
```
安装完成后，建议重新打开一个终端窗口来查看是否安装成功。


```
#查看nvm版本
nvm --version
```
如果出现版本号，那么表示NVM已经安装成功！

#### 安装Node

```
nvm install v6.9.5
```
安装完成后，使用nvm来指定使用的node版本

```
nvm use v6.9.5

nvm alias default v6.9.5
```

#### 指定淘宝镜像源

```
npm --registry=https://registry.npm.taobao.org install -g npm

# 也可以安装cnpm
npm --registry=https://registry.npm.taobao.org install -g cnpm
```
加快npm速度
```
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```
#### 安装nrm来管理使用源
```
npm install -g nrm

nrm ls

nrm use taobao
```


#### 安装工具包
```
npm i pm2 webpack gulp grunt-cli -g
```
---
注意：本文所有操作均在CentOS7下，其它系统不保证安装成功！