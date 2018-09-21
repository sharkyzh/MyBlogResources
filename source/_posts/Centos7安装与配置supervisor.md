---
title: Centos7安装与配置supervisor
category:
  - centos
tags:
  - centos
abbrlink: b8744e3e
date: 2018-09-21 14:22:49
---
#### 安装

```
yum install python-setuptools
easy_install supervisor
#或者是
pip install supervisor
```

> supervisor安装完成后会生成三个执行程序：supervisortd、supervisorctl、echo_supervisord_conf，分别是supervisor的守护进程服务（用于接收进程管理命令）、客户端（用于和守护进程通信，发送管理进程的指令）、生成初始配置文件程序。

#### 配置

输出supervisor配置，可以使用echo_supervisord_conf,我们将其重定向到文件中，然后就可以修改其配置项了。 
运行supervisord服务的时候，需要指定supervisor配置文件，如果没有显示指定，默认在以下目录查找：


```
###$CWD表示运行supervisord程序的目录。
$CWD/supervisord.conf 
$CWD/etc/supervisord.conf
/etc/supervisord.conf
/etc/supervisor/supervisord.conf (since Supervisor 3.3.0)
../etc/supervisord.conf (Relative to the executable)
../supervisord.conf (Relative to the executable)
```

我们将其重定向到/etc/目录下面

```
mkdir /etc/supervisor
echo_supervisord_conf > /etc/supervisor/supervisord.conf
```
 <!-- more -->
1、配置文件参数说明
supervisor的配置参数较多，下面介绍一下常用的参数配置，详细的配置及说明，请参考官方文档介绍。 

```
注：分号（;）开头的配置表示注释
[unix_http_server]
file=/tmp/supervisor.sock   ;UNIX socket 文件，supervisorctl 会使用
;chmod=0700                 ;socket文件的mode，默认是0700
;chown=nobody:nogroup       ;socket文件的owner，格式：uid:gid

;[inet_http_server]         ;HTTP服务器，提供web管理界面
;port=127.0.0.1:9001        ;Web管理后台运行的IP和端口，如果开放到公网，需要注意安全性
;username=user              ;登录管理后台的用户名
;password=123               ;登录管理后台的密码

[supervisord]
logfile=/tmp/supervisord.log ;日志文件，默认是 $CWD/supervisord.log
logfile_maxbytes=50MB        ;日志文件大小，超出会rotate，默认 50MB，如果设成0，表示不限制大小
logfile_backups=10           ;日志文件保留备份数量默认10，设为0表示不备份
loglevel=info                ;日志级别，默认info，其它: debug,warn,trace
pidfile=/tmp/supervisord.pid ;pid 文件
nodaemon=false               ;是否在前台启动，默认是false，即以 daemon 的方式启动
minfds=1024                  ;可以打开的文件描述符的最小值，默认 1024
minprocs=200                 ;可以打开的进程数的最小值，默认 200

[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ;通过UNIX socket连接supervisord，路径与unix_http_server部分的file一致
;serverurl=http://127.0.0.1:9001 ; 通过HTTP的方式连接supervisord

; [program:xx]是被管理的进程配置参数，xx是进程的名称
[program:xx]
command=/opt/apache-tomcat-8.0.35/bin/catalina.sh run  ; 程序启动命令
autostart=true       ; 在supervisord启动的时候也自动启动
startsecs=10         ; 启动10秒后没有异常退出，就表示进程正常启动了，默认为1秒
autorestart=true     ; 程序退出后自动重启,可选值：[unexpected,true,false]，默认为unexpected，表示进程意外杀死后才重启
startretries=3       ; 启动失败自动重试次数，默认是3
user=tomcat          ; 用哪个用户启动进程，默认是root
priority=999         ; 进程启动优先级，默认999，值小的优先启动
redirect_stderr=true ; 把stderr重定向到stdout，默认false
stdout_logfile_maxbytes=20MB  ; stdout 日志文件大小，默认50MB
stdout_logfile_backups = 20   ; stdout 日志文件备份数，默认是10
; stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
stdout_logfile=/opt/apache-tomcat-8.0.35/logs/catalina.out
stopasgroup=false     ;默认为false,进程被杀死时，是否向这个进程组发送stop信号，包括子进程
killasgroup=false     ;默认为false，向进程组发送kill信号，包括子进程

;包含其它配置文件,一般情况下，我们会新建一个目录来专门存放服务的配置文件，然后在此文件中将其include包含进来。
[include]
files = relative/directory/*.ini    ;可以指定一个或多个以.ini结束的配置文件
```

2、配置管理进程
进程管理配置参数，不建议全都写在supervisord.conf文件中，应该每个进程写一个配置文件放在include指定的目录下包含进supervisord.conf文件中。 
（1）创建/etc/supervisor/config.d目录，用于存放进程管理的配置文件 
（2）修改/etc/supervisor/supervisord.conf中的include参数，将/etc/supervisor/conf.d目录添加到include中


```
[include]
files = /etc/supervisor/config.d/*.ini
```

3、服务配置模板
###项目名称（program_name是唯一不可重复的，用来唯一标识一个服务）

```
[program:program_name]
###命令要能运行在前台，如果命令加入&符号，例如"ping 127.0.0.1 &"命令就不能被supervisor来进行监控。
command=php /home/wwwroot/site.webshowu.com/artisan queue:work redis --sleep=3 --tries=3 --daemon
autostart=true
autorestart=true
user=root
numprocs=3
redirect_stderr=true
stdout_logfile=/home/wwwlogs/worker1.log
```


这些配置项的含义已经在上面的配置文件说明中详细说明。 
下面是配置dotnet api进程的一个例子：


```
[program:webapi]
command=dotnet zh_Api.dll
directory=/www/wwwroot/api.sharkyzh.fun
environment=ASPNETCORE__ENVIRONMENT=Production #环境变量
user=root
stopsignal=INT
autostart=true
autorestart=true
startsecs=3
stderr_logfile=/wwwlogs/apilogs/api.err.log
stdout_logfile=/wwwlogs/apilogs/api.out.log
```

4、web管理配置说明
编辑配置文件/etc/supervisor/supervisord.conf,在最后面加如下配置：


```
[inet_http_server]         ; inet (TCP) server disabled by default
port=0.0.0.0:9001          ; (ip_address:port specifier, *:port for all iface)
username=user              ; 用户名 (default is no username (open server))
password=123               ; 密码 (default is no password (open server))
```

此时访问本机的9001端口，输入用户名密码即可进入此管理页面

三、启动与关闭supervisor和其管理的项目
1、启动supervisor

```
##-c制定让其读取的配置文件
supervisord -c /etc/supervisor/supervisord.conf
```

2、关闭supervisor

```
supervisorctl shutdown
```


3、重新载入supervisor，在这里相当于重启supervisor服务，里面的服务也会跟着重新启动

```
supervisorctl reload
```


4、添加/删除 要管理服务
（1）添加或删除配置文件 
（2）更新


```
supervisorctl update
```

5、管理supervisor下的服务

```
###启动服务
supervisorctl start all
supervisorctl start service_name
###关闭服务
supervisorctl stop all
supervisorctl stop service_name
###查看状态
supervisorctl status [service_name]
###重新启动所有服务或者是某个服务
supervisorctl restart all
supervisorctl restart service_name
```
