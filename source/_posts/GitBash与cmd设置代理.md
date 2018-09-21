---
title: GitBash与cmd设置代理
category:
  - others
tags:
  - 其它
abbrlink: e3bb5ff8
date: 2018-06-25 11:51:49
---
##### GitBash设置代理

```
git config --global https.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
git config --global http.proxy 'socks5://127.0.0.1:1080' 
git config --global https.proxy 'socks5://127.0.0.1:1080'
```


##### cmd设置代理

```
set http_proxy=http://127.0.0.1:1080
```