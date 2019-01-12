---
title: angularjs中ng-model的坑
date: 2016-08-06 13:33:31
categories: 
  - AngularJS
tags: 
  - angularjs
---

[转]
angular中指令被用的最多的除了ng-repeat之外就非他莫属了吧，由于各种业务的需求，我们经常需要去写一些继承ngModel的指令，来满足各种奇葩需求，最多的莫过于表单的验证啦，另外你在封装一些jquery插件的时候，也是需要继承ngModel的，最典型的莫过于 datepicker、fileUpload等等。...
<!-- more -->

但是ngModel本身其实有很多坑，在这里分享给大家，如果大家又遇到其他问题，欢迎补充：

首先我们要知道一些概念的东西，这里我就不占篇幅了，不了解的可以先去看看这篇文章：

http://www.cnblogs.com/liulangmao/p/4110137.html

在这里，我基本分为两种问题，一种是viewValue和modelValue不同的问题，另一种是管道问题

## 第一种：viewValue和modelValue不同的问题

1.viewValue想变成modelValue
2.modelValue想变成ViewValue
3.moduleValue想和viewModel不一样
4.viewModel和DOM元素的值不一样

要想解决这些问题，我们先来了解一些$apply的一个问题，那就是执行$apply之后数据是以什么为主的

①viewValue -> modelValue
②modelValue -> viewValue,于是我们写了下面这个指令

```code
<input type="text" ng-model="vm.modelTest" model-test>　　
```

```code
app
.directive('modelTest',function(){
  return {
    require : 'ngModel',
    link : function(scope,iElm,iAttrs,ngModelCtrl){
      iElm.on('mouseenter',function(){
        console.info("打印出更改之后，没有执行$apply的值")
        console.log(ngModelCtrl);

        // 更改modelValue的值
        ngModelCtrl.$modelValue = "test change";
        console.info("打印出更改之后，没有执行$apply的值")
        console.log(ngModelCtrl);

        //执行$apply
        scope.$apply();
        console.info("打印出更改之后，执行$apply的值")
        console.log(ngModelCtrl);
      })
    }
  }
})
```

从上面的指令，我们不难得出一个比较武断的结论 $apply会根据viewValue的值而改变modelValue的值，也就是modelValue -> viewValue，那么，我们可以开始解决我们的问题了.

**1.viewValue想变成modelValue**  
这种情况很少， 但是如果你在jq的操作就可能发生这种需求，一般依然还是$apply让model的值重新更新，使用了$apply就会触发$render()函数，因为modelValue的变化，会导致$render触发
**2.modelValue想变成ViewValue**　(看了上面的实验，我想你应该懂了，直接使用$apply)
**3.moduleValue想和viewModel不一样**  
一般情况都会使用管道去解决，但是有个其他思路可以提供给大家,下面这段代码是在国外看到的，觉得挺不错的，效果大家可以去尝试下，将model的值变成数组。

```code
    ngModelCtrl.$viewChangeListeners.push(function(){
          $parse(iAttrs.ngModel).assign(scope, ngModelCtrl.$viewValue.split(',')); 
    });
```

### 第二种：管道问题

管道是什么呢，在ngModel里面其实就是view视图和model视图数据交互的时候需要经过的数组或对象，这个数组或对象由方法组成，每次经过都会执行数组里面的方法，并返回结果。一共有四种管道

**$formatters** ：它是model视图转向view视图的管道（数组），model的值会经过他才转变成view视图的值，另外它的调用顺序是最特别的，从后往前调用，，越在数组后面，越早调用。
**$parsers** : 它正好是跟$formatters相反，是view视图转向model视图的管道（数组），view的值会经过他才转变成model视图的值，在1.2.x（兼容IE8）之前一般我们会在这里实现或扩展验证功能
**$validators**：这是在1.3.x之后出现的管道（json对象），来帮助我们实现和扩展验证功能的，当我们进入$parsers的同时，同时也会进入$validators，在这里我贴段源码（验证required）给大家看看，我想大家应该能理解这个管道的功能

```code
ctrl.$validators.required = function(modelValue, viewValue) {
        return !attr.required || !ctrl.$isEmpty(viewValue);
      };
```

**$asyncValidators** :功能与$validators相似，不同的是他可以实现异步验证，你可以在管道内部执行异步处理，会在结果返回之后，才去设置验证结果，这是个大课题，我看到有人讨论过，我也就不详细讲了http://www.cnblogs.com/liulangmao/p/4118868.html
可以进去这个博客看看