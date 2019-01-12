---
title: AngularJS源码之ng-style
date: 2016-10-20 11:58:42
categories: 
  - AngularJS
tags: 
  - angularjs
---

ng-style源码

```code
scope.$watch(attr.ngStyle, function ngStyleWatchAction(newStyles, oldStyles) {
  if (oldStyles && (newStyles !== oldStyles)) {
    forEach(oldStyles, function(val, style) {
      element.css(style, '');
    });
  }
  if (newStyles)
    element.css(newStyles);
}, true);
```
