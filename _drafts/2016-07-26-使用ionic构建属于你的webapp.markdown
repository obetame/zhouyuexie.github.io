---
layout: post
title:  "使用ionic构建属于你的webapp"
date:   2016-07-26 11:21:03 +0800
tags: ionic,angularjs,js,webapp
---

WebApp是指基于Web的系统和应用，其作用是向广大的最终用户发布一组复杂的内容和功能。WebApp在体验上接近native app，开发上具备更多的灵活性和跨平台性。运行WebApp只需要一个浏览器,而且可以借助一些打包软件比如phonegap,appcan等这些可以快速制作成apk发布.

---

## 准备ionic

ionic 是目前最有潜力的一款 HTML5 手机应用开发框架。通过 SASS 构建应用程序，它提供了很多 UI 组件来帮助开发者开发强大的应用。 它使用 JavaScript MVVM 框架和 AngularJS 来增强应用。提供数据的双向绑定，使用它成为 Web 和移动开发者的共同选择。ionic是一个专注于用WEB开发技术，基于HTML5创建类似于手机平台原生应用的一个开发框架。ionic框架的目的是从web的角度开发手机应用，基于PhoneGap的编译平台，可以实现编译成各个平台的应用程序。

当然我们可以直接将项目中的html,css和js文件直接发布到服务器提供服务.

#### 1.安装ionic

```shell
npm install -g ionic
```

*注意:ionic需要全局安装,如果你使用的是 OSX/Linux 平台,你需要在这个命令前加上`sudo`*

#### 2.开始你的项目

```shell
ionic start myapp [template]
```

`[template]`这个命令是一个模板,ionic提供三个模板:

- [tabs](https://github.com/driftyco/ionic-app-base)(默认)
- sidemenu
- blank

*注意:使用模板可以快速开发,但是有时候会一直提示`Download...`,如果几分钟一直这样,你可以`ctrl+c`来停掉程序,然后再试上面那个创建命令.*

创建好了后切换到你的项目文件夹里,然后安装项目所依赖的开发插件:

```shell
#切换到你的目录
cd myapp

#安装项目依赖
npm install
```

此时我们可以开始运行这个项目了:

```shell
#输入下面这个命令
ionic serve

多个地址。请选择使用哪个地址通过输入数量从下面的列表:
 1) 192.168.31.86 (本地连接)
 2) 10.10.10.1 (VMware Network Adapter VMnet1)
 3) 192.168.128.1 (VMware Network Adapter VMnet8)
 4) localhost
选择一个地址:  localhost   #可能会要求你输入一个地址
已选择的地址: localhost
Running live reload server: http://localhost:35729
Watching: 0=www/**/*, 1=!www/lib/**/*
项目开发端口地址:  http://localhost:8100
Ionic server commands, enter:
  restart or r to restart the client app from the root
  goto or g and a url to have the app navigate to the given url
  consolelogs or c to enable/disable console log output
  serverlogs or s to enable/disable server log output
  quit or q to shutdown the server and exit

#下面你就进入了ionic控制台
ionic $ 

#以上翻译了一部分的英文,大概意思就是已经运行成功,然后会弹出浏览器打开那个页面.
```

![ionic运行](http://oavd4gltg.bkt.clouddn.com/ionic-1.png)

其它一些简单的命令:

- 重启服务器:直接输入`r`.
- 日志输出:直接输入`c`切换.
- 服务器日志:直接输入`s`切换.
- 停止服务器:直接输入`q`.

##### 其它的一些命令:

1. `ionic serve --address 68.54.96.105`:指定运行地址.
2. `ionic serve --lab`:让用户在iOS和Android平台可以更方便地测试您的应用程序。

其它的一些可以你去[官网](http://ionicframework.com/docs/cli/test.html)查看.


## 现在构建你的webapp

如果你想构建`android/ios`平台上的应用,那你可以增加一个目标平台:

```shell
ionic platform android

#以下是输出信息:
Updated the hooks directory to have execute permissions
Adding android project...
Creating Cordova project for the Android platform:
        Path: platforms\android
        Package: com.ionicframework.yyhuopinwebapp222568
        Name: yyhuopin_webapp
        Activity: MainActivity
        Android target: android-23
Android project created with cordova-android@5.1.1
Installing "cordova-plugin-console" for android
Installing "cordova-plugin-device" for android
Installing "cordova-plugin-splashscreen" for android
Installing "cordova-plugin-statusbar" for android
Installing "cordova-plugin-whitelist" for android

               This plugin is only applicable for versions of cordova-android greater than 4.0. If you have a previous platform version, you do *not* need this plugin since the whitelist will be built in.

Installing "ionic-plugin-keyboard" for android
Running command: "C:\Program Files\nodejs\node.exe" E:\work\yyhuopin-webapp\hooks\after_prepare\010_add_platform_class.js E:/work/yyhuopin-webapp
add to body class: platform-android
```

*注意:如果你需要增加ios平台,你需要一台苹果系统的电脑.*

然后bulid它:

```shell
ionic build android

##下面这是我的输出:
Running command: "C:\Program Files\nodejs\node.exe" E:\work\yyhuopin-webapp\hooks\after_prepare\010_add_platform_class.js E:/work/yyhuopin-webapp
add to body class: platform-android
Error: Failed to find 'ANDROID_HOME' environment variable. Try setting setting it manually.
Failed to find 'android' command in your 'PATH'. Try update your 'PATH' to include path to valid SDK directory.
```

**上面的错误信息说明了你没有安装Android的开发环境,所以无法导出为apk包,如果你需要导出为apk包,那你需要安装Android的开发环境了,具体如何安装,请Google.**

如果你的没有提示错误信息,那接下来就是模拟app:

```shell
ionic emulate android [options]
```

运行到你的设备上:

```shell
ionic run android [options]
```