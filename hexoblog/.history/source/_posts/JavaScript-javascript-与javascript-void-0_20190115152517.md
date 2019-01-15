---
title: 'javascript:;与javascript::void(0)使用介绍'
date: 2019-01-12 22:26:34
categories: 
  - JavaScript
tags:
  - JavaScript
---

### `javascript:;`与`javascript::void(0)`使用介绍

有时候我们在编写js过程中，需要触发事件而不需要返回值，那么就可能需要这样的写法

`href="#"`包含了一个位置信息.默认的锚是#top,也就是网页的上端，当连续快速点击此链接时会导致浏览器巨慢甚至崩溃。

当然我们一般用三个 `href="###"`,不过看了这篇文章我们以后就可以使用`javascript:;`(一个冒号一个分号)

javascript中void是一个操作符，该操作符指定要计算一个表达式但是不返回值。

`javascript:;`好些，`javascript::void(0)`据说某些情况下有浏览器兼容bug。(此点bug我也不知道什么时候能出现，知道的童鞋请指教)。 

<!-- more -->

可以写成`javascript:;`，qq空间很多都是写成`javascript:;`
我感觉这两者之间没有什么差别，都是执行一个空事件。 
`javascript:;`甚至少了7个字符，呵呵。 

新浪微博写的是`javascript::void(0)`;

我以前一直写的是`javascript::void(0)`;但是现在写的都是`javascript:;`

`<a href="#">` 点击链接后，页面会向上滚到页首，# 默认锚点为 #TOP

    <a href="javascript::void(0)" onClick="window.open()"> </a>

点击链接后，页面不动，只打开链接

    <a href="#" onclick="javascript:return false;">  作用同上，不同浏览器会有差异。

点击链接后，不想使页面滚到页首，就用`href="javascript::void(0)`"，不要用href="#"，return false也有类似作用

### 详解`href="#"`与href="javascript::void(0)"的区别

"＃"包含了一个位置信息
默认的锚点是＃top 也就是网页的上端
而`javascript::void(0)` 仅仅表示一个死链接
这就是为什么有的时候页面很长浏览链接明明是＃可是跳动到了页首
而`javascript::void(0)` 则不是如此
所以调用脚本的时候最好用void(0)
或者`<input onclick>`
`<div onclick>`等
打开新窗口链接的几种办法

1.window.open('url')

2.用自定义函数
复制代码代码如下:

```code
  <script>
  function openWin(tag,obj)
  {
    obj.target="_blank";
    obj.href = "Web/Substation/Substation.aspx?stationno="+tag;
    obj.click();
  }
  </script>
  <a href="javascript::void(0)" onclick="openWin(3,this)">LINK_TEST</a>
  window.location.href=""
```

-------------------------------------------------------------------------------
如果是个# ，就会出现跳到顶部的情况,个人收藏的几种解决方法：

	1.<a href="####"></a>
	
	2.<a href="javascript::void(0)"></a>
	
	3.<a href="javascript:void(null)"></a>
	
	4.<a href="#" onclick="return false"></a>
	
	5.<span style="cursor:hand"></span>(好像在FF中不能显示)

-------------------------------------------------------------------------------
慎用`javascript::void(0)`

今天调试CGI的时候，明明CGI程序已经执行，并且最后结果也是正确的，但是页面就是不刷新。在FireFox2.0下测试，结果却是正常的，IE6却偏偏不刷新

！仔细调查了一下，发现cgi页面链接的是 <a href="javascript::void(0)" OnClick="XXX_Func();" ….> only a sample </a>,问题就出在这个void(0)

上!让我们先来看看JavaScript中void(0)的含义:
JavaScript中void是一个操作符，该操作符指定要计算一个表达式但是不返回值。
void 操作符用法格式如下：

	1. javascript:void (expression_r_r)
	2. javascript:void expression_r_r

expression_r_r是一个要计算的 JavaScript 标准的表达式。表达式外侧的圆括号是可选的，但是写上去是一个好习惯。我们可以使用 void 操作符指定

超级链接。表达式会被计算但是不会在当前文档处装入任何内容。面的代码创建了一个超级链接，当用户点击以后不会发生任何事。当用户点击链接时，

void(0) 计算为 0，但在 JavaScript 上没有任何效果。

`<a href="javascript::void(0)">`单击此处什么也不会发生</a>
也就是说，要执行某些处理，但是不整体刷新页面的情况下，可以使用void(0),但是在需要对页面进行refresh的情况下，那就要仔细了。
其实我们可以这样用`<a href="javascript:void(document.form.submit())">`，这句话会进行一次submit操作。那什么情况下用void(0)比较多呢，无刷新，当然是Ajax了，看一下Ajax的web页面的话，一般都会看到有很多的void(0)，：）　，所以在使用void(0)之前,最好先想一想,这个页面是否需要整体刷新。
 
使用javascript的时候,通常我们会通过类似:`<a href="#" onclick="javascript:方法">提交</a>`的方式,通过一个伪链接来调用javascript方法.这种方法有一个问题是:
虽然点击该链接的时候不会跳转页面.但是滚动条会往上滚,解决的办法是返回一个false.
如下所示:

    <a href="#" onclick="javascript:方法;return false;">提交</a>

还可以用 ###

`a href="javascript::void(0)" onclick="javascript:方法;return false;"`提交
`javascript::void(0)`就不会向上跳了:)

还有一个方法是 #this
a href="#this" onclik