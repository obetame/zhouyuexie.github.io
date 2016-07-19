---
layout: post
title:  "服务器如何控制HTPPS和HTTP访问"
date:   2016-07-15 12:21:03 +0800
tags: https,http
---

## 1.前言

:::success
这几天研究了一下Nginx,用来控制使用不同协议访问不同的页面,不过由于Nginx资料太少,看来好多都没法理解如何配置,只能`rewrite`所有的请求到https的服务器上,可是一般网站只有注册登录的时候才会用到https.
:::

---

## 2.https网站问题

#### 1.无法使用外部文件(比如js脚本)

对于使用了外部js文件的网站来说,使用了https后浏览器会显示类似于`Blocked loading mixed active content "http://*****"`的提示,这是浏览器为了防止网站被攻击而拒绝连接外部脚本的,用户通过HTTP访问一个网页时其连接是开放的容易被窃听和中间人（MITM）攻击。而一个用户通过HTTPS访问一个网页时，他们与Web服务器的连接经过身份验证和SSL加密的，因此可以防止窃听和中间人攻击。具体的细节可以[点击这里](https://developer.mozilla.org/en-US/docs/Web/Security/Mixed_content/How_to_fix_website_with_mixed_content)查看.

#### 2.需要CA机构wosign的颁发的SSL证书

我就不多说了,对于没多说资金的个人站长来说花钱的都是不好的.

#### 3.用户不友好

对一般人来说输入网站地址不会输入http或者https,而浏览器默认是连接80端口的网站,而开启了https后的网站端口是443的,可想而知用户等待返回的是一个`无法访问此网站`的信息.

---

## 3.解决问题

> 我使用的[Express](http://www.expressjs.com.cn/),想法是创建两个服务器,然后通过写路由控制访问不同的页面.比如我通过`http`访问`login`页面的时候路由判断使用的如果是http协议,则返回303使浏览器跳转到https页面.

**声明:我的express服务器是`express-generator`生成的,你可以安装express后再安装它,然后使用命令`express your-project`生成一个`your-project`项目.**

假设我的服务器是`quietboy`,则我的目录大致是:

- bin
  - www_https *=>这个是https启动项*
- app_https.js *=>这里配置路由*

#### 1.首先创建两个服务器入口

在Express中入口文件在`/bin`文件下,我们不需要重写一个服务器,只需要复制一个你的https服务器入口文件(我们命名为`www_https.js`),复制后的服务器入口文件命名为`www_http.js`,然后将`www_http.js`里的https改为http(其它改动比如端口等待我们不讨论).记得将`www_http.js`里的

```javascript
var app = require('../app_https');
//改为下面这个
var app = require('../app_http');
```

#### 2.创建两个app服务器

和上面一样,复制`app_https.js`改名为`app_http.js`,再更改里面细节内容.

更改后的目录大致是:

- bin
  - www_http *=>这个是http启动项*
  - www_https *=>这个是https启动项*
- app_http.js *=>这里配置路由*
- app_https.js *=>这里配置路由*

#### 3.修改http路由

比如我们想要将访问http服务器的`/login`和`/logup`请求导向到https中时,我们可以这样写http中的路由:

```JavaScript
app.get('/login',function(req,res){
 res.redirect(301,"https://www.quietboy.net/login");
});
app.get('/logup',function(req,res){
 res.redirect(301,"https://www.quietboy.net/logup");
});
```

301代码告诉浏览器这个页面永久性的转移了,你需要使用我现在发给你的链接.

这样我们就可以正常使用https请求网页了,但是我们有一个网页使用了外部js脚本那该怎么办呢?

#### 4.修改https路由

和上面的代码改动的差不多,比如我想改`/articles`的页面:

```JavaScript
app.get('/articles',function(req,res){
	res.redirect(301,"http://www.quietboy.net/articles");
});
```

可是一般的网页我们都有参数的,如果我这样跳转参数丢失了那这个也没什么用了,所以我们可以这样改:

```JavaScript
app.get('/articles',function(req,res){
 if(req.query.user && req.query.indexes){
  res.redirect(301,"http://www.quietboy.net/articles?user="+req.query.user+"&indexes="+req.query.indexes);
 }
 else{
  res.redirect(301,"https://www.quietboy.net/newarticles");
 }
});
```

---

## 结语

对于`http`服务器不处理的请求我们需要在`https`服务器处理,反之亦然.

如果你看完这篇文字觉得这可以用nginx配置完成的话请务必留言告诉我,就当我写了这么多的份上吧......