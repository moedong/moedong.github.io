---
title: gulp对seajs的模块做合并压缩
date: 2017-12-14 20:53:28
tags: [node,koa2,seajs,gulp]
---


###### 基于gulp对seajs的模块做合并压缩的方式

gulpfile.js

```
//seajs合并模式
gulp.task("seajs", function () {
    return merge(
        gulp.src('dist/js/!(cmdlib|ckeditor|plugin|app)/**/*.js',{base:'dist/js/'})
            .pipe(transport())
            .pipe(concat({
                base: "dist/js/",
            }))
            .pipe(gulp.dest('dist/js'))
    );
});

```
<!-- more -->

> 这个是seajs合并工作中比较关键的一点，它不像requirejs，直接做concat即可；它必须先经过一个transport的任务处理，将匿名模块变成具名模块，同时用define的第二个参数来描述这个模块的所有依赖，就像requirejs那样。只有做完了transport，才能利用gulp-seajs-concat做合并。原因请参考：https://github.com/seajs/seajs/issues/426。
gulp-seajs-concat做合并的时候，就很简单了，只要告诉它一个 `base选项` 即可，这个base选项跟js/common.js中base选项保持一致。因为gulp-seajs-concat根据base和transport之后的模块，就能找到它所依赖的其它模块文件。

[参考文章](http://blog.csdn.net/sinat_17775997/article/details/52295209)
[具体例子](https://github.com/liuyunzhuge/blog/tree/master/seajs)