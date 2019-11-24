---
title: Cenos7安装Mongodb
date: 2019-11-24 21:32:07
categories: Linux
tags: Linux
---

## 添加 Mongodb 源

在 CentOS 7 源中默认不包含 MongoDB 的数据源。所以，我们需要自己添加 MongoDB 源，采用 MongoDB4.0 版本

创建源配置文件

```linux
vim /etc/yum.repos.d/mongodb-org-4.0.repo
```

将以下内容写入文件

```mongodb
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
```

## 安装 MongoDB

```linux
sudo yum install mongodb-org
```

安装的包：

```linux
=======================================================================================
 Package                       Arch       Version          Repository          Size
=======================================================================================
Installing:
 mongodb-org                   x86_64     4.0.13-1.el7     mongodb-org-4.0     5.8 k
Installing for dependencies:
 mongodb-org-mongos            x86_64     4.0.13-1.el7     mongodb-org-4.0     12 M
 mongodb-org-server            x86_64     4.0.13-1.el7     mongodb-org-4.0     21 M
 mongodb-org-shell             x86_64     4.0.13-1.el7     mongodb-org-4.0     13 M
 mongodb-org-tools             x86_64     4.0.13-1.el7     mongodb-org-4.0     46 M
```

- mongodb-org-server - 标准的 MongoDB 服务端程序（既守护程序），以及相应的 init 脚本和配置
- mongodb-org-mongos - MongoDB Shard 集群服务端程序（守护进程）
- mongodb-org-shell - MongoDB shell，用于通过命令行与 MongoDB 交互
- mongodb-org-tools - 包含一些用于恢复，导入和导出数据的基本工具，以及其他各种功能。

## 配置 MongoDB

MongoDB 的配置文件位于/etc/mongod.conf，并以 YAML 格式编写。大多数设置在文件中都有非常好（便于理解）的注释。我们概述了以下默认选项:

- systemLog 指定各种日志记录选项，解释如下：
  - destination 告诉 MongoDB 是将日志输出存储为文件或者是系统日志
  - logAppend 指定守护程序重新启动时是否将新日志记录附加到现有日志的末尾（而不是创建备份并在重新启动时启动新日志）
  - path 告诉服务端程序（守护进程）发送日志信息到某个位置（/var/log/mongodb/mongod.log 默认情况下）
- storage 设置 MongoDB 如何存储数据，设置如下：
  - dbPath 指示数据库文件的存储位置（默认：/var/lib/mongo）
  - journal.enabled 启用或禁用日志，以确保数据文件可以恢复
- net 指定各种网络选项，具体如下：
  - port 是 MongoDB 服务端（守护）程序监听的端口
  - bindIP 指定 MongoDB 绑定的 IP 地址，因此它可以监听来自其他应用程序的连接

取消注释该 security 部分并添加以下内容：

文件位置：/etc/mongod.conf

```linux
security:
  authorization: enabled
```

该 authorization 选项为你的数据库启用基于角色的访问控制。如果未指定任何值，则任何用户都可以修改任何数据库。

## 启动和停止 MongoDB

要启动，重新启动或停止 MongoDB 服务，请从以下命令发出相应的命令：

```linux
sudo systemctl start mongod
sudo systemctl restart mongod
sudo systemctl stop mongod
```

你还可以设置开机时候 MongoDB 自动启动：

```linux
sudo systemctl enable mongod
```

参考：https://cloud.tencent.com/developer/article/1329170
