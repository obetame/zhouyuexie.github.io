---
layout: post
title:  "使用nvm导致"sudo node"找不到命令"
date:   2016-08-26 11:21:03 +0800
tags: shell,nvm,nodejs
---

nvm是一个很好的nodejs版本管理器,使用他我们可以很好的管理node版本,这个工具在我的另一篇文章里就有提到[ubuntu中npm安装全局插件提示没有root管理员权限](https://zhouyuexie.github.io/ubuntu%E4%B8%ADnpm%E5%AE%89%E8%A3%85%E5%85%A8%E5%B1%80%E6%8F%92%E4%BB%B6%E6%8F%90%E7%A4%BA%E6%B2%A1%E6%9C%89root%E7%AE%A1%E7%90%86%E5%91%98%E6%9D%83%E9%99%90/),有兴趣的同学可以取看看.

可是当你使用管理员模式启动的时候:

```shell
> sudo node
sudo: node：找不到命令
```

你会发现shell会提示'找不到命令',因为这个nvm并不全局安装,她将你的node安装到你的账号下面,而shell默认去$PATH里查找文件,具体是这样的:

```shell
# 在自己账户下面输入$PATH
> $PATH
/home/quietboy/.nvm/versions/node/v4.5.0/bin:/usr/local/bin:/usr/bin:/bin
```

shell会返回你当前账户的环境变量,每个路径使用":"隔开,其中`/home/quietboy/.nvm/versions/node/v4.5.0/bin`就是你得node路径地址,当你在当前账户下输入`node`的时候shell就在这个路径下寻找`node`文件,找到后就执行这个命令了.而当你使用管理员账户执行这个命令的时候:

```shell
# 在自己账户下面输入$PATH
> sudo $PATH
/usr/local/bin:/usr/bin:/bin
```

`/usr/local/bin/`路径去找`node`文件,可是那里并没有`node`文件,所以才会出现上面的'找不到命令',那这时候怎么办呢?毕竟有时候需要使用管理员启动node执行一些需要root命令,比如启动http服务需要80端口.

**解决办法**:既然你没有这个路径,那我们就给你增加一个,我们可以考虑'软链接',也就相当于windows下面的快捷方式.

```shell
# 我执行的命令
> ln -s /home/quietboy/.nvm/versions/node/v4.5.0/bin/node /usr/local/bin/node

# 你需要更改的地方
> ln -s /home/你的账户名/.nvm/versions/node/版本/bin/node /usr/local/bin/node

# 请确认文件路径正确与否,路径因nvm的版本而有所不同
```

上面的命令使用了`ln`来添加一个软链接,大概的格式就是`ln -s a b`,意思就是创建a位置的快捷方式放到b处,这时候访问b位置就相当于访问a位置.

当系统没有提示问题的时候你可以打开`/usr/local/bin/`路径查看是否有一个`node`文件,这是一个链接文件,右击查看属性你就看到了.