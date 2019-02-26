---
title: 初始化ionic项目
date: 2019-02-22 17:59:14
categories:
  - Ionic
  - Angular
tags:
  - Ionic
  - Angular
---

# ionic 初始化项目步骤

## 文档地址

ionic V3: [https://ionicframework.com/docs/v3/](https://ionicframework.com/docs/v3/)  
ionic V4: [https://ionicframework.com/docs/](https://ionicframework.com/docs/)

## 安装node、npm

```code
npm -v
node -v
```

## 全局安装ionic和cordova

```code
npm install -g ionic  // 最新的4.10.3
npm install -g cordova // 最新8.1.2
```

<https://ionicframework.com/docs/building/android>

## 初始化项目

```code
    ionic start myapp tabs
```

<!-- more -->

## 初始化platform命令

```code
    ionic cordova platform add android // Android

    ionic cordova platform add ios // 初始化ios平台
```

## 命令相关

android:

```code
ionic cordova emulate android -lc 启动安卓模拟器并查看项目
ionic cordova run android -lc  真机调试
```

iOS:

```code
ionic cordova emulate ios -lc
ionic cordova run ios -lc
```

## 插件相关

### Cordova插件可防止应用在后台进入睡眠状态。需要Cordova插件：cordova-plugin-background-mode。有关插件的更多信息，请访问：https：//github.com/katzer/cordova-plugin-background-mode

安装Cordova和Ionic Native插件：

```code
ionic cordova plugin add cordova-plugin-background-mode
npm install --save @ionic-native/background-mode@4   // 3.xx.xx版本
```

```code
import { BackgroundMode } from '@ionic-native/background-mode';

constructor(private backgroundMode: BackgroundMode) { }

...

this.backgroundMode.enable();

```

### 集成极光推送[https://www.jianshu.com/p/0f1c2a1d1dc9](https://www.jianshu.com/p/0f1c2a1d1dc9)

`截止20190222极光推送不支持ionic4`

### 本地消息通知`cordova-plugin-local-notification`

#### 安装插件

```code
ionic cordova plugin add cordova-plugin-local-notification
npm install --save @ionic-native/local-notifications@4
```

#### 支持情况

```code
Android
iOS
Windows
```

已知的问题

对Android Oreo的支持还有限。
v0.9和v0.8彼此不兼容（不修复

用法： v3：[https://ionicframework.com/docs/v3/native/local-notifications/](https://ionicframework.com/docs/v3/native/local-notifications/)

## F&Q

1、ionic 安装失败
https://blog.csdn.net/qq_31482717/article/details/54565253

2、项目提示错误：Current working directory is not a Cordova-based project.

解决方案： ionic cordova platform add android [https://www.jianshu.com/p/35ee988cc1d4](https://www.jianshu.com/p/35ee988cc1d4)