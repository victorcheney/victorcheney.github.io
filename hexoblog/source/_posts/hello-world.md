---
title: Hexo相关操作
tags: "hexo"
---

文档：[https://hexo.io/zh-cn/docs/commands](https://hexo.io/zh-cn/docs/commands)

Hacker主题：[https://github.com/CodeDaraW/Hacker](https://github.com/CodeDaraW/Hacker)

多设备方案：[https://www.zhihu.com/question/21193762](https://www.zhihu.com/question/21193762)

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

### 模版（Scaffold）

在新建文章时，Hexo 会根据 scaffolds 文件夹内相对应的文件来建立文件，例如：

    $ hexo new photo "My Gallery"

在执行这行指令时，Hexo 会尝试在 scaffolds 文件夹中寻找 photo.md，并根据其内容建立文章，以下是您可以在模版中使用的变量：

    变量    描述  
    layout  布局  
    title   标题  
    date    文件建立日期  
