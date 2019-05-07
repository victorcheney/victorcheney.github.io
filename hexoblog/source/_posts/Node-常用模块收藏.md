---
title: Node--常用模块收藏
date: 2018-11-15 16:32:55
categories:
  - Node
tags:
  - Node
---

### Node 常用模块收藏

### inquirer [https://www.npmjs.com/package/inquirer](https://www.npmjs.com/package/inquirer)
常用交互式命令行用户界面的集合:
>提供错误反馈  
>问问题  
>解析输入  
>验证答案  
>管理分层提示  

<!-- more -->

### Commander.js
[https://github.com/tj/commander.js](https://github.com/tj/commander.js) 命令行完美解决方案

### [roa](https://www.npmjs.com/package/ora)

控制台loading `npm install ora`

```code
    const ora = require('ora');

    const spinner = ora('Loading unicorns').start();

    setTimeout(() => {
        spinner.color = 'yellow';
        spinner.text = 'Loading rainbows';
    }, 1000);
```

### [progress](https://www.npmjs.com/package/progress)

控制台进度条 `npm install progress`

```code

    var ProgressBar = require('progress');

    var bar = new ProgressBar(':bar', { total: 10 });
    var timer = setInterval(function () {
    bar.tick();
    if (bar.complete) {
        console.log('\ncomplete\n');
        clearInterval(timer);
    }
    }, 100);
```

### [qs](https://www.npmjs.com/package/qs)

查询字符串解析和字符串化库，增加了一些安全性。