---
title: centos7设置supervisor开机启动
category:
  - linux
  - centos
tags:
  - centos
  - linux
abbrlink: ae315e0f
date: 2018-09-21 17:43:36
---

##### 新建文件supervisord.service

```
#supervisord.service

[Unit] 
Description=Supervisor daemon

[Service] 
Type=forking 
ExecStart=/usr/bin/supervisord -c /etc/supervisord.conf 
ExecStop=/usr/bin/supervisorctl shutdown 
ExecReload=/usr/bin/supervisorctl reload 
KillMode=process 
Restart=on-failure 
RestartSec=42s

[Install] 
WantedBy=multi-user.target
```

将文件拷贝到/usr/lib/systemd/system/


```
cp supervisord.service /usr/lib/systemd/system/
```

启动服务


```
systemctl enable supervisord
```

验证一下是否为开机启动


```
systemctl is-enabled supervisord
```