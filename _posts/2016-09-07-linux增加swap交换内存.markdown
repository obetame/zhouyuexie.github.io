---
layout: post
title:  "linux增加swap交换内存"
date:   2016-07-28 11:21:03 +0800
tags: deepin,ubuntu,linux
---

由于前端工作需要使用到很多自动化插件和工具,导致内存越来越吃紧,甚至导致了几次死机事件,还好喜欢`crtl+s`,否则后果不堪设想.后来发现自己没有设置swap交换区,于是就有了这个记录.

## swap介绍

#### 什么是swap

当系统的物理内存不够用的时候，就需要将物理内存中的一部分空间释放出来，以供当前运行的程序使用。那些被释放的空间可能来自一些很长时间没有什么操作的程序，这些被释放的空间被临时保存到Swap空间中，等到那些程序要运行时，再从Swap中恢复保存的数据到内存中。这样，系统总是在物理内存不够时，才进行Swap交换。

#### swap类别

Linux下可以创建两种类型的交换空间，一种是swap分区，一种是swap文件,由于硬盘已经都分区完了,所以只能选择swap文件方式创建swap.

#### 查看swap方式

大家都知道在linux平台使用命令`free`可以看到你的内存使用状态:

```shell
quietboy@quietboy-pc:~$ free -m
              total        used        free      shared  buff/cache   available
Mem:           3828        1802         316         329        1709        1668
Swap:          7812           3        7808
```

上面的`Mem`是物理内存,`Swap`是虚拟内存,如果你没有设置虚拟内存,那么你的`Swap`这行是为0的.

还可以通过命令top,free命令查看当前的交换分区。也可以使用`cat /proc/swaps`查看交换分区

## 增加swap

**提示:以下操作可能需要管理员方式.**

1. 切换到一个路径,这个路径你可以随便选择:

```shell
# 我使用这个路径,如果提示没有此目录可是自己新建一个
cd /mnt/swap
```

2. 在这个路径下新建一个足够大的swap文件,大小规则:

**4G以内的物理内存，SWAP 设置为内存的2倍。**
**4-8G的物理内存，SWAP 等于内存大小。**
**8-64G 的物理内存，SWAP 设置为8G。**
**64-256G物理内存，SWAP 设置为16G。**

由于我的内存是4G,所以我可以设置8G或者4G,这个选择看你了.

```
dd if=/dev/zero of=/mnt/swap/swapfile bs=1024 count=8192000
# count的值等于1024 x 你想要的文件大小, 8192000是8G
```

创建的内存越大需要的时间越长,所以请耐心等候.处理完后你可以看到这个文件有8G大小.

3. 将这个文件变成swap文件.

```shell
mkswap /mnt/swap/swapfile
```

4. 启用这个swap文件

```shell
swapon /mnt/swap/swapfile
```

这时候你就能看到swap已经在使用了.

## 最后工作

如果你重启后这个swap是没有工作的,需要你重复上面第四个步骤,这是很麻烦的事情,所以我们需要让电脑每次启动的时候自己去启用这个swap:

我们打开`/etc/fstab`这个文件,增加这一行代码`/mnt/swap/swapfile swap swap defaults 0 0`.

如果你的路径和我不一样,那你需要更改你的路径.

## 其它

分区不同,启用和控制的命令也不同:

1. ext分区是否启用由`mount`及`umount`控制。
2. swap分区是否启动，由`swapon`及`swapoff`控制。

所以你看到上面第四个步骤中的启用swap文件命令是`swapon`了,那显然的关闭这个swap用的就是`swapoff`了.

```shell
# 关闭swap
swapoff /mnt/swap/swapfile
```

**最后编辑于:2016-09-08**