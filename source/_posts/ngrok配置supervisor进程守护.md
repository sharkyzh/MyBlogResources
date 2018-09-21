---
title: ngrok配置supervisor进程守护
category:
  - ngrok
tags:
  - ngrok
abbrlink: 7ded41a5
date: 2018-09-21 17:37:58
---

##### 安装supervisor

可以参考我的博客里的安装教程

[传送门](https://blog.sharkyzh.cn/post/b8744e3e.html)

这里就不细说了

##### 配置文件

```
[program:ngrokd]
command = /usr/ngrok/bin/ngrokd -domain="tunnel.51ngrok.cn" -httpAddr=":8080" -httpsAddr=":8081" -tlsKey="/usr/ngrok/bin/51ngrok.key" -tlsCrt="/usr/ngrok/bin/51ngrok.crt"
directory = /usr/ngrok/bin
process_name = %(program_name)s_%(process_num)s
numprocs = 1 ;注意这里只能为1
autostart = true     ; 在 supervisord 启动的时候也自动启动
startsecs = 5        ; 启动 5 秒后没有异常退出，就当作已经正常启动了
autorestart = true   ; 程序异常退出后自动重启
startretries = 3     ; 启动失败自动重试次数，默认是 3
user = root          ; 用哪个用户启动
stdout_logfile=/logs/ngroklog/ngrok.out.log
stdout_logfile_maxbytes = 10MB
stderr_logfile=/logs/ngroklog/ngrok.err.log
stderr_logfile_maxbytes = 10MB
```
碰到的坑

1、指定证书文件启动时，报错提示 51ngrok.key路径找不到路径

解决办法：证书用完整路径