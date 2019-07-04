---
title: gulp 搭建自己的工作流
date: 2019-07-04 11:23:38
tags:
---

### 准备工作

```
npm i gulp -g
```

新建项目文件，在根目录下创建gulpfile.js文件

> 提前定义了几个变量，方面更改文件路劲
```
const proSrc = 'src'; // 项目文件夹
const outSrc = 'dist' // 输出code
```



<!--more-->


### 处理html

暂时想到的：
 - 对html进行压缩处理，其中htmlIsCompress 是控制是否压缩html的变量

```
// 处理html
gulp.task('html', function () {
    return gulp.src(`${proSrc}/**/*.html`)
        .pipe(gulpif(htmlIsCompress, htmlmin({
            collapseWhitespace: true
        })))
        .pipe(gulp.dest(outSrc))
})
```

### 处理css

- 对css进行压缩处理, 使用cssIsCompress来控制
- 使用autoprefixer来添加前缀
- 使用postcss、postcss-px-to-viewport来做自适应

```
const vwProcessors = [
    pxtoviewport({
        viewportWidth: 1920,
        unitPrecision: 5,
        viewportUnit: 'vw',
        selectorBlackList: [], // 需要忽略的选择器
        // exclude: [/reset.css/], // 需要排除的文件
        minPixelValue: 1,
        mediaQuery: false
    })
];

// 处理css
gulp.task('css', function () {
    return gulp.src(`${proSrc}/css/*.css`)
        .pipe(postcss([autoprefixer()]))
        .pipe(postcss(vwProcessors))
        .pipe(gulpif(cssIsCompress, cssmin({
            "maxLineLen": 80,
            "uglyComments": true
        })))
        .pipe(gulp.dest(`${outSrc}/css`))
})
```

### 处理js

暂时只是做到了是否对js进行压缩

```
// 处理js
gulp.task('js', function () {
    return gulp.src(`${proSrc}/js/*.js`)
    .pipe(gulpif(jsIsCompress, uglify()))
    .pipe(gulp.dest(`${outSrc}/js`))
})
```

### 处理图片

-  imgIsCompress控制图片是否压缩

```
// 处理图片
gulp.task('images', function () {
    return gulp.src(`${proSrc}/images/*.{png,jpeg,jpg,svg}`)
        .pipe(gulpif(imgIsCompress, imagemin()))
        .pipe(gulp.dest(`${outSrc}/images`))

})
```

### 最后配置默认任务

```
// 清除dist文件
gulp.task('deleteAll', function () {
    return del(outSrc)
})

// default
gulp.task('default', gulp.series('deleteAll', gulp.parallel('html', 'css', 'images', 'js')))
```

最后项目代码：https://github.com/yuan-xiaogang/gulp-work