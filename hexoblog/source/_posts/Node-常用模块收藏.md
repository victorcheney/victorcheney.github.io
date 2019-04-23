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

### [lowdb](https://github.com/typicode/lowdb)

 lowdb是一个由Lodash支持的小型本地JSON数据库（支持Node，Electron和浏览器）

 安装

 ```code
 npm install lowdb
 ```

 用法

 ```code
const low = require('lowdb')
const FileSync = require('lowdb/adapters/FileSync')

const adapter = new FileSync('db.json')
const db = low(adapter)

// Set some defaults (required if your JSON file is empty)
db.defaults({ posts: [], user: {}, count: 0 })
  .write()

// Add a post
db.get('posts')
  .push({ id: 1, title: 'lowdb is awesome'})
  .write()

// Set a user using Lodash shorthand syntax
db.set('user.name', 'typicode')
  .write()
  
// Increment count
db.update('count', n => n + 1)
  .write()
 ```

### [simple-git](https://www.npmjs.com/package/simple-git)

用于在任何node.js应用程序中运行git命令的轻量级接口

 安装

 ```code
 npm install simple-git
 ```

### [shortid](https://github.com/dylang/shortid)

>令人惊讶的简短非连续url友好的唯一id生成器。

ShortId创建了非常短的非连续url友好的独特ID。适用于网址缩短程序，MongoDB和Redis ID以及用户可能看到的任何其他ID。

* 默认情况下，7-14 URL友好字符：A-Z，a-z，0-9，_-
* 支持cluster（自动），自定义种子，自定义字母。
* 可以生成任意数量的ID而无需重复，甚至每天数百万。
* 非常适合游戏，特别是如果你担心作弊，所以你不想要一个容易猜到的id。
* 应用程序可以重新启动任意次，而无需重复ID。
* Mongo ID / Mongoose ID的热门替代品。
* 适用于Node，io.js和Web浏览器。
* 包括摩卡测试。

ShortId不会生成加密安全ID，因此不要依赖它来制作无法猜测的ID。

### [material-design-icons-iconfont](https://github.com/jossef/material-design-icons-iconfont)

[Demo page](https://jossef.github.io/material-design-icons-iconfont)

安装

使用bower

```code
bower install material-design-icons-iconfont --save
```

使用npm

```code
npm install material-design-icons-iconfont --save
```

### [gray-matter](https://www.npmjs.com/package/gray-matter)

从字符串或文件中解析前端内容。快速，可靠且易于使用。默认情况下解析YAML前端问题，但也支持YAML，JSON，TOML或Coffee Front-Matter，并提供设置自定义分隔符的选项。

安装

使用npm安装：

```code
npm install --save grey-matter
```

### [markdown-it](https://github.com/markdown-it/markdown-it)

markdown 解析器

安装

node.js & bower:

```code
npm install markdown-it --save
bower install markdown-it --save
```

[examples](https://github.com/markdown-it/markdown-it#usage-examples)

### [markdown-it-katex](http://npm.taobao.org/package/markdown-it-katex)

markdown 中添加数学公式

### [markdown-it-toc-and-anchor](http://npm.taobao.org/package/markdown-it-toc-and-anchor)

markdown-it插件，用于在标题中添加toc和锚点链接