---
title: 修改hosts文件大法
date: 2018-02-28 22:04:10
categories:
  - Github
tags:
  - Github
---

# 改HOSTS大法

Windows下在C:/Windows/system32/drivers/etc/hosts
Ubuntu等linux系一般在/etc/hosts

## 在hosts中添加如下内容：

```code
 # Github
151.101.44.249 github.global.ssl.fastly.net
192.30.253.113 github.com
103.245.222.133 assets-cdn.github.com
23.235.47.133 assets-cdn.github.com
203.208.39.104 assets-cdn.github.com
204.232.175.78 documentcloud.github.com
204.232.175.94 gist.github.com
107.21.116.220 help.github.com
207.97.227.252 nodeload.github.com
199.27.76.130 raw.github.com
107.22.3.110 status.github.com
204.232.175.78 training.github.com
207.97.227.243 www.github.com
185.31.16.184 github.global.ssl.fastly.net
185.31.18.133 avatars0.githubusercontent.com
185.31.19.133 avatars1.githubusercontent.com
```

## 改完之后立刻刷新

```code
Windows：ipconfig /flushdns
Ubuntu：sudo systemctl restart nscd
```

## 工具推荐

修改hosts文件神器 `SwitchHosts!` [https://github.com/oldj/SwitchHosts/releases](https://github.com/oldj/SwitchHosts/releases)