---
layout: post
title:  "ubuntu中npm安装全局插件提示没有root管理员权限"
date:   2016-07-28 11:21:03 +0800
tags: js,node,nodejs,nvm,sudo,linux,npm
---

玩过Linux或者linux发行版本的人都知道,linux对用户的权限管理非常严格,这也是linux开发人员喜爱的原因之一,可是有时候对于一般的用户来说,这时常使他们困惑和头疼不已.

## 遇到的问题

我一直使用linux作为生产工具,后来重装了一次系统,当我安装完`nodejs`的时候发现问题来了.

普通用户使用`npm`安装全局插件:

```shell
# -g 表示安装到系统目录下
npm install -g gulp
```

然后你可能会看到类似这一堆的错误:

```shell
npm ERR! Error: EACCES, open '/Users/chietala/.npm/-/all/.cache.json'
npm ERR!   code: 'EACCES',
npm ERR!   path: '/Users/chietala/.npm/-/all/.cache.json' }
npm ERR! Please try running this command again as root/Administrator.
........
```

重点是第三行:"Please try running this command again as root/Administrator.",大概意思就是:"请使用管理员模式再次运行这个命令".

## 第一种方法

使用npm安装全局插件的时候提示需要root权限.很多人就想着那就提升权限来执行npm命令好了,这当然没有问题.

于是很多人都这样了输入命令:

```shell
sudo npm install -g gulp
```

能成功吗?当然能成功了,root管理员可是上帝!

问题暂时解决了,于是你开心的输入命令`gulp your_task`准备开始一天的工作的时候,你又傻了,你看到了这一堆东西:

```shell
fs.js:549
  return binding.open(pathModule._makeLong(path), stringToFlags(flags), mode);
                 ^

Error: EACCES: permission denied, open '/usr/lib/node_modules/.gulp_npminstall/node_modules/.1.0.1@arr-flatten/index.js'
    at Error (native)
    at Object.fs.openSync (fs.js:549:18)
    at Object.fs.readFileSync (fs.js:397:15)
    at Object.Module._extensions..js (module.js:416:20)
    at Module.load (module.js:344:32)
    at Function.Module._load (module.js:301:12)
    at Module.require (module.js:354:17)
    at require (internal/module.js:12:17)
    at Object.<anonymous> (/usr/lib/node_modules/.gulp_npminstall/node_modules/.2.0.0@arr-diff/index.js:10:15)
    at Module._compile (module.js:410:26)
```

你仔细看看,是不是看到了`permission`这个词,说明了我们没有权限去执行这个命令,因为刚才那个安装命令是root用户执行的,普通用户无法使用这个命令,你这肯定想这不是坑爹吗,凭什么我不能用呢!

好了,我们还是老老实实的找解决办法吧,不能说又提升权限去执行这一条命令吧,而且就算说你`sudo su`也只能一个终端用是吧,什么!?你要用管理员登陆?你这不是找死吗,等你不小心删了什么东西,你就只能哭了,等着老板爸爸来干你.

第一个方法很简单,只有一条命令,非常粗暴有力.

```shell
sudo chown -R $(whoami) ~/.npm
```

我来解释一下:这条命令是改变`~/.npm`目录及其文件用户和群组,也就是说直接将这个目录和这个目录下的文件更改为属于自己的.既然你说这个不属于和我没有这个权限,我就抢过来写上我的名字,这不就是我的了吗,简单粗暴.而`$(whoami)`就是使用shell中的变量$符号,`whoami`是linux中一个返回当前工作用户的用户名,这条命令用的真是让人大开眼界!

**注意:第一种方法并不推荐,只有当你非常清楚这个目录下没有敏感的数据时候才可以这样操作,否则出现的一切问题你需要自己承担.**

## 第二种方法

这是推荐的做法,因为这里没有涉及到权限的操作,但是有点麻烦和复杂,所以看你需要了,如果你是想长期使用的话你可以跟着一步步的来,如果你只是懒得动,那第一种方法适合你.

> NVM:全称是Node Version Manager,也就是node版本管理器,当然还有一个n,暂且不管n.

**安装步骤:**

#### 1. 卸载Node,可能需要root权限.

```shell
sudo apt-get remove nodejs
```

#### 2. 移除你之前的全局node_modules包.

```shell
#执行前请确认这个包是否存在这个位置
sudo rm -rf /usr/lib/node_moudles
```

#### 3. 安装NVM

你可以移步NVM的[Github](https://github.com/creationix/nvm),或者看我的脚本安装.

```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.4/install.sh | bash
```

如果提示你没有curl,那就安装一个吧.

```shell
sudo apt-get install curl
```

好了,现在可以用了吧.

```shell
#将
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
```

你可以测试一下:

```shell
nvm -v
#如果安装了会提示nvm版本
```

#### 4. 使用node安装node

```shell
nvm install node
#默认安装最新的6.2
```

安装这个NVM有什么好处呢?我觉得最大的好处就是你可以安装多个版本的node到你的系统里.比如现在node有4.2稳定版和最新的6.2版本,6开头的版本支持最新的ES6,如果你想体验ES6,那你不得不安装这个版本了,可是你升级到6开头的版本会发现你原先的一下包会无法使用.

是的,目前很多包还没有升级到6,所以NVM的作用显得非常有用了,直接一条命令就可以切换到4开头的版本.

```shell
nvm use 4
#或者你可以
nvm run node --version
```

当然你得先安装4开头的node,现在NVM默认安装6开头的了,所以你要手动安装4开头的版本.

```shell
#你需要指定版本,或者让nvm自己选择一个
nvm install 4.2
```

每次打开一个新的终端你必须手动指定一个node版本,这挺麻烦的,你可以使用命令设定默认的版本.

```shell
nvm alias default 4
```


## 总结

有时候你安装nodejs的时候系统并不要求你是root身份,可是有时候却要你以root身份来安装,所以当你以root身份安装完nodejs后,再用普通用户身份运行npm命令也就要求你也是root身份了.


参考资料
- [NPM throws error without sudo.](http://stackoverflow.com/questions/16151018/npm-throws-error-without-sudo)
- [NVM](https://github.com/creationix/nvm)
- [使用 nvm 管理不同版本的 node 与 npm](http://www.tuicool.com/articles/Vzquy2)
- [Install npm packages globally without sudo on macOS and Linux](https://github.com/sindresorhus/guides/blob/master/npm-global-without-sudo.md)