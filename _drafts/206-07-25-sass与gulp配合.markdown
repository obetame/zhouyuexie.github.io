---
layout: post
title:  "sass与gulp的配合开发"
date:   2016-07-25 16:21:03 +0800
tags: sass、css、scss
---

`sass`是一个`css`预处理器,当然还有`less`这种的,这里我们就只讲`sass`.

### Sass

对于入门前端的同学来说,一个`css`文件就够用了,但是对于想要构建大型网站的开发人员来说,写`html`就够自己累死的了,而`sass`不仅使`css`变得更像一门编程语言,而且使我们再编写`css`的时候更轻松不少.这里不讲如果使用`sass`,其实它很简单,感兴趣的同学[点击这里](http://www.sasschina.com/guide/).

### [Gulp](http://www.gulpjs.com.cn/)

官网是这样介绍的:Gulp.js 是一个自动化构建工具，开发者可以使用它在项目开发过程中自动执行常见任务。Gulp.js 是基于 Node.js 构建的，利用 Node.js 流的威力，你可以快速构建项目并减少频繁的 IO 操作.

[入门指南](http://www.gulpjs.com.cn/docs/getting-started/).

### 准备开发工作

**你必须首先安装:N[odeJS](https://nodejs.org/en/download/)**
**选择安装:[git](https://git-scm.com/downloads)**

##### 安装Gulp

1. 任意目录下全局安装Gulp:

```shell
npm install -g gulp
```

2. 创建一个你的工作目录然后切换进去:

```shell
mkdir your-workDir
cd your-workDir
```

3. 创建gulpfile.js文件:

```shell
touch gulpfile.js
```

4. 创建package.json文件:

```shell
npm init
```

然后你可以按照提示生成这个文件,我们后面要安装的依赖都必须写入到这个文件里,这样多人协同工作的时候可以简单很多.

##### 安装[gulp-sass](https://www.npmjs.com/package/gulp-sass)插件

```shell
npm install gulp-sass --save-dev
```

如果没有`package.json`文件,会提示出错.

此时你的工作目录情况是:

- node_modules
- package.json
- gulpfile.js

我们再次将Gulp作为项目的依赖安装到`node_modules`中去.

```shell
npm install gulp --save-dev
```

##### 配置使用

1. 我们再创建两个个文件夹,一个`sass`,专门放sass样式文件,
一个`css`文件夹,专门放`gulp-sass`处理后的文件夹.

此时的目录情况是:

- node_modules
- package.json
- gulpfile.js
- sass
- css

2. 打开gulpfile.js文件夹,我们添加如下代码:

```javascript
var gulp = require('gulp');
var sass = require('gulp-sass');

//表示新建一个sass任务
gulp.task('sass',function(){
  return gulp.src('./sass/*.scss')//应该处理的文件位置
    .pipe(sass.sync().on('error',sass.logError))//使用sass处理
    .pipe(gulp.dest('./css'));//处理完后输出到css文件夹
});
```

OK,然后保存.此时我们的sass目录没有任何文件呢,我们就先创建一个sass文件夹试试吧.

```css
$nav-color:#fff;
$margin-width:100px;

nav{
  width:100%;
  margin:$margin-width;
  a{
    color:#000;
    &:hover{
      color:#fff;
    }
  }
}
```

将上面的代码保存到sass文件夹下,重命名为`my-sass.scss`,也许你会奇怪文件后缀为什么不是`sass`.

其实 sass 有两种后缀名文件：一种后缀名为 sass，不使用大括号和分号；另一种就是我们这里使用的 scss 文件，这种和我们平时写的 css 文件格式差不多，使用大括号和分号。本教程中所说的所有 sass 文件都指后缀名为 scss 的文件。在此也建议使用后缀名为 scss 的文件，以避免 sass 后缀名的严格格式要求报错。

好了,现在我们可以尝试生成css文件了.

```shell
gulp sass
```

不需要多久就提示你已经完成了,这是你去捏css目录下看看,是否已经有一个`my-sass.css`文件了.

##### 结语

强烈建议你学会sass等等css预处理器,它对设计师非常友好,著名的bootstrap库用的是less,但是4.0版本后将使用sass,感兴趣的朋友可以去了解一下.