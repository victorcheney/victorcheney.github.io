---
title: angularjs问题总结
date: 2016-07-12 20:49:28
categories: 
  - AngularJS
tags: 
  - angularjs
---

## 1.动态添加html代码 中的ng指令问题
调用$compile重新编译这部分代码

```code
app.directive("showtip",['$compile',function($compile){
  return {
      restrict : "A",
      scope: false,
      replace: true,
      link : function(scope,element,attrs){
          // element.on("click",function ($event) {
          //     scope.showTip = true;
          //     $event.stopPropagation();
          // });
          setInterval(function(){
              scope.$apply();
          }, 0);
          var el=$compile(element.contents())(scope);
          // elem.contents().remove();
          element.append(el);
      }
  }
}]);
```

...
<!-- more -->

### 2.Angular会在compile阶段收集View模板上的所有Directive。Angular表达式会被解析成一种特殊的指令：addTextInterpolateDirective。到了link阶段，就会利用scope.$watch的API注册我们在上面提到的watchers函数：它的求值函数为$interpolate对绑定表达式进行编译的结果，监听函数则是用新的表达式计算值去修改DOM Node的nodeValue。可见，在View中的Angular表达式，也会成为Angular在$digest循环中watchers的一员。

在上面代码中，还有一部分是为了给调试器用的。它会在Angular表达式所属的DOM节点加上名为‘ng-binding’的调试类。类似的调试类还有‘ng-scope’，‘ng-isolate-scope’等。在Angular 1.3中我们可以使用compileProvider服务来关闭这些调试信息。

```code
app.config(['$compileProvider', function ($compileProvider) {
    // disable debug info
    $compileProvider.debugInfoEnabled(false);// Remove debug info (angularJS >= 1.3)
}]);
```

### 3.避免显示的运用$scope.$watch,如果使用了需要及时销毁

$scope.$watch函数的返回值就是用于释放这个watcher的函数，如下面的单次绑定实现（one-time）：[http://www.cnblogs.com/whitewolf/p/angular-remove-unnecessary-watch-to-improve-performance.html](http://www.cnblogs.com/whitewolf/p/angular-remove-unnecessary-watch-to-improve-performance.html)

```code
angular.module('com.ngnice.app')
.controller('DemoController', ['$scope', function($scope) {
  var vm = this;
  vm.count = 0;
  var textWatch = $scope.$watch('demo.updated', function(newVal, oldVal) {
    if (newVal !== oldVal) {
      vm.count++;
      textWatch();
    }
  });
  return vm;
}]);
```

也可以配合$destroy在页面跳转时，销毁页面controller中的$watch:

```code
$scope.$on("$destroy", function() {
  if(textWatch){
    textWatch();
  }
});
```

controller中使用到$timeout或$interval也要在$destroy中进行及时销毁

### 4.$destroy在自定义指令中，通常要及时销毁自定义指令中的定时器或变量，$destroy事件会在页面跳转时触发

$on、$emit、$broadcast、$watch返回值

### 5.[$rootScope:inprog] http://errors.angularjs.org/1.3.10/$rootScope/inprog?p0=$digest

以上错误是由于显式的调用 $scope.$apply();方法造成的；