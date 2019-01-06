---
title: AngularJS指令进阶–ngModelController详解
date: 2016-07-16 18:15:11
tags: angularjs
---

在自定义Angular指令时，其中有一个叫做require的字段，这个字段的作用是用于指令之间的相互交流。举个简单的例子，假如我们现在需要编写两个指令，在linking函数中有很多重合的方法，为了避免重复自己（著名的DRY原则），我们可以将这个重复的方法写在第三个指令的controller中，然后在另外两个需要的指令中require这个拥有controller字段的指令，最后通过linking函数的第四个参数就可以引用这些重合的方法。代码的结构大致如下：
...
<!-- more -->

```code
    var app = angular.modeule('myapp',[]);

    app.directive('common',function(){
        return {
        ...
        controller: function($scope){
            this.method1 = function(){
            };
            this.method2 = function(){
            };
        },
        ...
        }
    });

    app.directive('d1',function(){
        return {
        ...
        require: '?^common',
        link: function(scope,elem,attrs,common){
            scope.method1 = common.method1;
            ..
            },
        ...
        }
    });

    app.directive('d2',function(){
        return {
        ...
        require: '?^common',
        link: function(scope,elem,attrs,common){
            scope.method1 = common.method1;
            ..
            },
        ...
        }
    });
```

当然，上面例子只是指令中controller用法的一种。虽然一般来说，使用controller字段的机会不是很多，但是想要写好AngularJS的指令，这是必须要掌握的一点。

显然，controller的用法分为两种情形，一种是require自定义的controller，由于自定义controller中的属性方法都由自己编写，使用起来比较简单；另一种方法则是require AngularJS内建的指令，其中大部分时间需要require的都是ngModel这个指令。很多时候，由于我们对ngModel中内建的方法和属性不熟悉，在阅读和编写代码时会有一些困难。今天我们的目的就是详细的介绍ngModel中的内建属性和方法，相信在认真的阅读完本文之后，你一定能够熟练的在指令中require ngModel。

在Angular应用中，ng-model指令时不可缺少的一个部分，它用来将视图绑定到数据，是双向绑定魔法中重要的一环。ngModelController则是ng-model指令中所定义的controller。这个controller包含了一些用于数据绑定，验证，CSS更新，以及数值格式化和解析的服务。它不用来进行DOM渲染或者监听DOM事件。与DOM相关的逻辑都应该包含在其他的指令中，然后让这些指令来试用ngModelController中的数据绑定功能。

下面，我们将用一个例子来说明如何在自定义指令中require ngModel。在这里例子中，我们使用HTML5中的contenteditable属性来制作了一个简单的编辑器指令，同时将在指令定义中require了ngModel来进行数据绑定。

HTML部分

```code
    <form name="myForm">
    <div contenteditable
      name="myWidget" ng-model="userContent"
      strip-br="true"
      required>Change me!</div>
      <span ng-show="myForm.myWidget.$error.required">Required!</span>
    <hr>
    <textarea ng-model="userContent"></textarea>
    </form>
```

指令定义部分

```code
angular.module('customControl', []). directive('contenteditable', function() { return { restrict: 'A', // 作为元素属性 require: '?ngModel', // 获取ngModelController link: function(scope, element, attrs, ngModel) { if(!ngModel) return; // 如果没有ng-model则什么都不做

    // 指定UI的更新方式
    ngModel.$render = function() {
      element.html(ngModel.$viewValue || '');
    };

    // 监听change事件来开启绑定
    element.on('blur keyup change', function() {
      scope.$apply(read);
    });
    read(); // 初始化

    // 将数据写入model
    function read() {
      var html = element.html();
      // 当我们清空div时浏览器会留下一个<br>标签
      // 如果制定了strip-br属性，那么<br>标签会被清空
      if( attrs.stripBr && html == '<br>' ) {
        html = '';
      }
      ngModel.$setViewValue(html);
    }
  }
};
});
```

### ngModelController方法

1.$render()  
当视图需要更新的时候会被调用。使用ng-model的指令应该自行实现这个方法。

2.$isEmpty(value)
该方法用于判断输入值是否为空。
例如，使用ngModelController的指令需要判断其中是否有输入值的时候会使用该方法。该方法可用来判断值是否为undefined,'',null或者NaN。
你可以根据自己的需要重载该方法。

3.$setValidity(validationErrorKey, isValid);
该方法用于改变验证状态，以及在控制变化的验证标准时通知表格。 
这个方法应该由一个验证器来调用。例如，一个解析器或者格式化函数。

4.$setPristine();
该方法用于设置控制到原始状态。 
该方法可以移除'ng-dirty'类并将控制恢复到原始状态('ng-pristine'类)。

5.$cancelUpdate();
该方法用于取消一次更新并重置输入元素的值以防止$viewCalue发生更新，它会由一个pending debounced事件引发或者是因为input输入框要等待一些未来的事件。
如果你有一个使用了ng-model-options指令的输入框，并为它设置了debounced事件或者是类似于blur的事件，那么你可能会碰到在某一段时间内输入框中值和ngModel的$viewValue属性没有保持同步的情况。 
在这种情况下，如果你试着在debounced/future事件发生之前更新ngModel的$modelValue，你很有可能遇到困难，因为AngularJS的dirty cheching机制实际上并不会分辨一个模型究竟有没有发生变化。
6.$cancelUpdate()方法应该在改变一个输入框的model之前被调用。记住，这很重要因为这能够确保输入字段能够被新的model值更新，而pending操作将会被取消。下面是一个例子：
HTML部分

```code
    <form name="myForm" ng-model-options="{ updateOn: 'blur' }">
    <p>With $cancelUpdate()</p>
    <input name="myInput1" ng-model="myValue" ng-keydown="resetWithCancel($event)"><br/>
    myValue: "{{ myValue }}"

    <p>Without $cancelUpdate()</p>
    <input name="myInput2" ng-model="myValue" ng-keydown="resetWithoutCancel($event)"><br/>
    myValue: "{{ myValue }}"
      </form>
    </div>  
```

JS部分

```code
    angular.module('cancel-update-example', [])

    .controller('CancelUpdateCtrl', function($scope) {
      $scope.resetWithCancel = function (e) {
    if (e.keyCode == 27) {
      $scope.myForm.myInput1.$cancelUpdate();
      $scope.myValue = '';
    }
      };
      $scope.resetWithoutCancel = function (e) {
    if (e.keyCode == 27) {
      $scope.myValue = '';
    }
      };
    });  
```

7.$setViewValue(value, trigger)方法
该方法用来更新视图值。这个方法应该在一个视图值发生变化时被调用，一般来说是在一个DOM事件处理函数中。例如，input和select指令就调用了这个函数。 
这个方法将会更新$viewValue属性，然后在$pasers中通将这个值传递给每一个函数，其中包括了验证器。这个值从$parsers输出后，将会被用于$modelValue以及ng-model属性中的表达式。 
最后，所有位于$viewChangeListeners列表中注册的监听器将会被调用。

### ngModelController中的属性

1.$viewValue

视图中的实际值

2.$modelValue

model中的值，它金额控制器绑定在一起

3.$parsers

将要执行的函数的数组，无论什么时候控制器从DOM中读取了一个值，它都将作为一个管道。其中的函数依次被调用，并将结果传递给下一个。最后出来的值将会被传递到model中。其中将包括验证和转换值的过程。对于验证步骤，这个解析器将会使用$setValidity方法，对于不合格的值将返回undefined。

4.$formatters

一个包含即将执行函数的数组，无论什么时候model的值发生了变化，它都会作为一个管道。其中的每一个函数都被依次调用，并将结果传递给下一个函数。该函数用于将模型传递给视图的值进行格式化。
$viewChangeListeners
只要视图的值发生变化，其中的函数就会被执行。其中的函数执行并不带参数，它的返回值也会被忽略。它可以被用在额外的#watches中。

5.$error
一个包含所有error的对象

6.$pristine
如果用户还没有进行过交互,值是true。

7.$dirty
如果用户已经进行过交互，值是true。

8.$valid
如果没有错误，值是true。

9.$invalid
如果有错误，值是true。

博客地址：http://blog.csdn.net/yy374864125/article/category/2557175