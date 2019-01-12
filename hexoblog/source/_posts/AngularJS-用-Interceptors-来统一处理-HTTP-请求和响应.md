---
title: AngularJS 用 Interceptors 来统一处理 HTTP 请求和响应
date: 2017-01-06 12:29:27
categories: 
  - AngularJS
tags: 
  - angularjs
---

## 主要功能

Interceptors 有两个处理时机，分别是：

其它程序代码执行 HTTP 请求之后，在实际从浏览器发出请求之前，即处理请求
得到请求的响应之后，在交给其它程序代码处理之前，即处理请求的响应
所以，不难理解它可以用于如下几个方面：

全局处理错误
统一进行身份验证一类的处理
对所有发出去的请求进行预处理
对所有收到的响应进行预处理
做一些增强用户体验的操作，例如显示一个进度条...

<!-- more -->

### 先来看看最基本的使用：

```more
    var app = angular.module('app', []);

    // 定义一个 Service ，稍等将会把它作为 Interceptors 的处理函数
    app.factory('HttpInterceptor', ['$q', HttpInterceptor]);

    function HttpInterceptor($q) {
      return {
        request: function(config){
          return config;
        },
        requestError: function(err){
          return $q.reject(err);
        },
        response: function(res){
          return res;
        },
        responseError: function(err){
          if(-1 === err.status) {
            // 远程服务器无响应
          } else if(500 === err.status) {
            // 处理各类自定义错误
          } else if(501 === err.status) {
            // ...
          }
          return $q.reject(err);
        }
      };
    }

    // 添加对应的 Interceptors
    app.config(['$httpProvider', function($httpProvider){
      $httpProvider.interceptors.push(HttpInterceptor);
    }]);
  ```
进一步了解

实际的 Interceptor 处理函数中， return 了一个包含四个成员的对象，这四个成员都 不是必须 的，可以按实际情况指定一二，分别如下：

`request` ：接收一个参数，它是 $http 中的标准 config 对象，同时也需要返回一个标准 config ，此时可以添加各类身份验证信息，同时也可在此启动进度条  
`requestError` ：当有多个 Interceptor 的时候， requestError 会在前一个 Interceptor 抛出错误或者执行 $q.reject() 时执行，接收的参数就对应的错误  
`response` ：接受一个请求对象参数，可以不处理就直接返回，此时也可以将进度条显示为成功完成，当然，如果后端 API 返回自定义错误时，HTTP 的状态码仍然是 200 得话，便在这里处理自定义错误，也可以对返回数据做一些处理，注意要将进度条置为完成  
`responseError` ：这个是重头戏，即可以处理标准的 Http 错误，如服务器没有响应时，或者 PHP 之类的 CGI 经常出现的 502 一类，还可以处理 HTTP 状态码不是 200 的各类自定义错误
上面四个中，前两个是请求的前置处理，后两个是针对请求的响应的处理。


## Error: [$injector:cdep] angular 循环依赖解决方案 ##

```code
    app.factory('httpInterceptor', ['$q', '$injector', '$state', ($q, $injector，$state) => {

      let httpInterceptor = {
          // 响应
          response: (response) => {
              // 判断权限
              if (response && response.data && response.data._NO_PERMISSION_) {
                  $state.go('app.no_permission', {
                      message: response.data._ERROR_MESSAGE_ || '没有访问权限'
                  });
              }
              return response;
          }

    }]);
```

直接注入$state会导致循环注入错误

```code
    Error: [$injector:cdep] http://errors.angularjs.org/1.3.10/$injector/cdep?p0=$http <- $templateFactory <- $view <- $state <- httpInterceptor <- $http <- $translateStaticFilesLoader
```

解决：在response方法中，手动注入$state

```code
    app.factory('httpInterceptor', ['$q', '$injector', ($q, $injector) => {

        let httpInterceptor = {
            // 响应
            response: (response) => {
                let $state = $injector.get('$state'); // 手动注入

                // 判断权限
                if (response && response.data && response.data._NO_PERMISSION_) {
                    $state.go('app.no_permission', {
                        message: response.data._ERROR_MESSAGE_ || '没有访问权限'
                    });
                }
                return response;
            }

    }]);
  ```