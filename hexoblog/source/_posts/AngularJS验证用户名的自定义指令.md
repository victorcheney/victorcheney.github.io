---
title: AngularJS验证用户名的自定义指令
date: 2017-10-14 14:01:04
tags: angularjs
---

## Angularjs表单验证中，验证用户名是否唯一

```code
angular.module('demo.form')
.directive('ensureUnique', ['$http', ($http) => {
  return {
    require: 'ngModel',
    link: (scope, ele, attrs, c) => {
      scope.$watch(attrs.ngModel, () => {
        $https({
          method: 'POST',
          url: '/api/check/' + attrs.ensureUnique,
          data: {'field': attrs.ensureUnique}
        }).success((data, status, headers, cfg) => {
          c.$setValidity('unique', data.isUnique)
        }).error((data, status, headers, cfg) => {
          c.$setValidity('unique', false);
        })
      })
    }
  }
}]);
```

第三方插件
bindonece 单项绑定
html2js 将html打包成js文件

扩展第三方组件：
$provide.decorator

```code
app.config(function($provide) {
    $provide.decorator('fooDirectve', function($delegate) {
        var directive = $delegate[0];

        directive.scope.fn = '&';
        var link = directive.link;

        directive.compile = function() {
            return funtion(scope, element, attrs) {
                link.apply(this, arguments);
                element.bind('click', function() {
                    scope.$apply(funtion() {
                        scope.fn();
                    })
                })
            }
        }

        return $delegate;
    })
})
```
