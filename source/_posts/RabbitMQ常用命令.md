---
title: RabbitMQ常用命令
category:
  - RabbitMQ
tags:
  - RabbitMQ
abbrlink: b7d85c7f
date: 2018-06-27 17:20:17
---

##### 启用Web控制台


```
rabbitmq-plugins enable rabbitmq_management
```


##### 开启服务


```
systemctl start rabbitmq-server.service
```


##### 停止服务


```
systemctl stop rabbitmq-server.service
```


##### 查看服务状态


```
systemctl status rabbitmq-server.service
```
 <!-- more -->

##### 查看RabbitMQ状态


```
rabbitmqctl status
```

##### 添加用户赋予管理员权限


```
rabbitmqctl  add_user  mqAdmin  163.com
rabbitmqctl  set_user_tags  mqAdmin  administrator
rabbitmqctl set_permissions -p / mqAdmin “.“ “.“ “.*”
```

##### 查看用户列表


```
rabbitmqctl list_users
```


##### 删除用户


```
rabbitmqctl delete_user username
```


##### 修改用户密码


```
rabbitmqctl oldPassword Username newPassword
```


##### 访问Web控制台

http://xxx:15672/注意配置防火墙，默认用户名密码都是guest，若新建用户一定要记得配置权限。


```
rabbitmqctl  set_permissions   Username  ConfP  WriteP  ReadP
```
