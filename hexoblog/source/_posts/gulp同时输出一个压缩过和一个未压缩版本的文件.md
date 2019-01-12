---
title: gulp同时输出一个压缩过和一个未压缩版本的文件
date: 2017-02-12 21:24:50
categories: 
  - Gulp
tags:
  - gulp
---

## 同时输出一个压缩过和一个未压缩版本的文件

同时输出压缩过的和未压缩版本的文件可以通过使用 gulp-rename 然后 pipe 到 dest 两次来实现 (一次是压缩之前的，一次是压缩后的)：

```code
var gulp = require('gulp');
var rename = require('gulp-rename');
var uglify = require('gulp-uglify');

var DEST = 'build/';

gulp.task('default', function() {
  return gulp.src('foo.js')
    // 这会输出一个未压缩过的版本
    .pipe(gulp.dest(DEST))
    // 这会输出一个压缩过的并且重命名未 foo.min.js 的文件
    .pipe(uglify())
    .pipe(rename({ extname: '.min.js' }))
    .pipe(gulp.dest(DEST));
});
```

[gulp-load-plugins](https://www.npmjs.com/package/gulp-load-plugins)  从包依赖项加载gulp插件并将它们附加到您选择的对象上。

[gulp-sequence](https://www.npmjs.com/package/gulp-sequence)

gulp-sequence 是一个异步转同步的包 可以让gulp 的异步任务 阻塞同步执行

1.给每个任务加上 return 关键字。
2.调用方法时，将方法名作为参数放在 __sequence__ 内使用

```code
var gulp = require('gulp');

var sequence = require('gulp-sequence'); //异步转同步的包

gulp.task('copyFile1',function(){
    return gulp.src('file1.txt')
        .pipe(gulp.dest('./dist/'))
})

gulp.task('copyFile2',function(){
    return gulp.src('file2.txt')
    .pipe(gulp.dest('./dist/'))
})

gulp.task('default',function(){
    return sequence('copyFile1','copyFile2',function(){
        console.log('任务完成')
    })
})
```

## 运行watch时，会出现顺序执行的错误： Error: The thunkFunction already filled ##

> 用了gulp-sequence插件导致的，解决办法参考：https://github.com/teambition/gulp-sequence/issues/2

```gulp
gulp.task('index-js', gulpSequence(['angular', 'vendor', 'mapbase', 'app', 'app-service']));

修改为：

gulp.task('index-js', function(cb) {
    gulpSequence(['angular', 'vendor', 'mapbase', 'app', 'app-service'])(cb);
});
```

## Gulp中的增量编译 ##

   参考：https://blog.csdn.net/dong123dddd/article/details/51868574