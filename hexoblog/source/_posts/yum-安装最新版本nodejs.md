---
title: yum 安装最新版本nodejs
date: 2019-11-24 20:37:06
categories: Linux
tags: Linux
---

示例是安装 10.x 版

第一步：

`curl --silent --location https://rpm.nodesource.com/setup_10.x | sudo bash -`

第二步：

`sudo yum -y install nodejs`

## 方法二

下载二进制文件

`wget https://nodejs.org/dist/v10.17.0/node-v10.17.0-linux-x64.tar.xz`

解压

```cmd
xz -d node-v10.17.0-linux-x64.tar.xz
tar -xf node-v10.17.0-linux-x64.tar
```

创建软链接(主要 node 解压目录)

```cmd
ln -s ~/node-v10.17.0-linux-x64/bin/node /usr/bin/node
ln -s ~/node-v10.17.0-linux-x64/bin/npm /usr/bin/npm
```

查看 node 版本

```node
[root@VM_0_9_centos /]# npm -v
6.11.3
[root@VM_0_9_centos /]# node -v
v10.17.0
```
