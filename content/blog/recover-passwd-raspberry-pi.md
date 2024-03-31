+++
title = '重置树莓派用户密码'
date = 2024-03-31T23:39:25+08:00
draft = false
toc = true
math = false
tags = ['raspberrypi', 'manual']
+++

如果你也有在[树莓派](https://www.raspberrypi.com/)上跑服务的话，那么难免会碰到忘记用户密码的情况。这里记录一下如何在不重新安装系统的情况下重置密码。

## 准备

首先你需要满足以下条件：

1. 可以接触到树莓派
2. 一个显示器和一个键盘
3. 一台电脑和一个读卡器

## 修改 `cmdline.txt` 文件

在断掉树莓派电源的情况下，把 SD 卡拔出来并通过读卡器连接到电脑上，在文件浏览器中找到它里面的 `cmdline.txt` 文件，然后用编辑器打开它。

`cmdline.txt` 的内容是类似于下面这样的一行文本：

```text {linenos=false}
dwc_otg.lpm_enable=0 console=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait
```

只需要在上面那行文本的最后加上 `init=/bin/sh` 来让树莓派系统启动后自动进入 `/bin/sh`，然后就可以把卡插回树莓派重新启动。

## 修改密码

给树莓派接上显示器和键盘后，输入以下命令来重新挂载系统的根目录：

```sh {linenos=false}
$ mount -o remount, rw /
```

接着就可以通过这条命令修改指定用户的密码了，我用的是 [DietPi](https://dietpi.com/) 镜像，所以这里以 **dietpi** 用户为例：

```sh {linenos=false}
$ passwd dietpi
```

回车后输入两次密码，就可以输入下面两条命令来正常启动系统：

```sh {linenos=false}
$ sync
$ exec /sbin/init
```

## 恢复 `cmdline.txt` 文件

最后还需要再次拔出 SD 卡，把 `cmdline.txt` 文件里的 `init=/bin/sh` 移除后重启，就可以通过新密码正常登录了。
