+++
title = '在 Windows 上用 Scoop 安装 ST-Link 工具'
date = 2025-11-23T18:13:58+08:00
toc = true
math = false
tags = ["windows", "stm32", "stlink", "scoop"]
+++

最近入手了一块 [STM32 Nucleo-144 board](https://www.st.com/resource/en/user_manual/um1974-stm32-nucleo144-boards-mb1137-stmicroelectronics.pdf)，用来学习 [Bare metal programming](https://github.com/cpq/bare-metal-programming-guide)。因为板子上集成了 [ST-Link](https://www.st.com/en/development-tools/stlink-v3set.html)，所以也不需要再购买额外的编程器，虽然我还有一个 [Muse Lab](https://www.muselab-tech.com/) 的 [nanoDAP-HS](https://github.com/wuxx/nanoDAP-HS) 就是了。最后只需要安装好软件 [stlink](https://github.com/stlink-org/stlink) 和[驱动](https://www.st.com/en/development-tools/stsw-link009.html)可以正常进行烧录和调试了。

## 安装驱动

首先前往 ST 官网下载并安装驱动 [STSW-LINK009](https://www.st.com/en/development-tools/stsw-link009.html)。这一步没啥好说的，注册个账号然后下载安装就好。

## 安装 stlink 工具

可以直接从 stlink 的 [GitHub releases](https://github.com/stlink-org/stlink/releases) 下载，也可以从源码编译安装。我是通过 [Scoop](https://scoop.sh/) 来安装的。

{{< warning >}}
通过 Scoop 安装 stlink 时会有个坑，后面会提到。
{{< /warning>}}

```shell
$ scoop update
$ scoop install stlink
$ st-info --version
v1.8.0
```

安装完成后就会有 `st-flash`，`st-info`，`st-trace` 和 `st-util` 这些命令行工具。

## 坑

安装完成后，如果连接板子并执行 `st-info --chip-id`，或者 `st-flash --reset write` 之类的命令时，都会遇到下面的报错：

```text
C:/Program Files (x86)/stlink/config/chips: No such file or directory
libusb: warning [winusb_get_device_list] failed to initialize device 'USB\VIDxxx'
```

这是因为 stlink 预期你会把文件解压到 `C:\Program Files` 或 `C:\Program Files (x86)` 下。

> From [stlink README](https://github.com/stlink-org/stlink#installation):
>
> However we suggest to move the unzipped application folder to `C:\Program Files\` on 32-bit systems and to `C:\Program Files (x86)\` on 64-bit systems (the toolset is 32-bit).

而 Scoop 安装的 stlink 会把相应的文件放到 `$SCOOP/apps/stlink/current/Program Files (x86)/` 下面，所以才会出现 `No such file or directory` 的报错。

要解决这个问题的话也很简单，要么直接把目录复制到 `C:\Program Files (x86)\` 下，要么就创建一个符号链接：

```powershell
# 要管理员权限
New-Item -ItemType Junction -Path "C:\Program Files (x86)\stlink" -Target "$env:SCOOP\apps\stlink\current\Program Files (x86)\stlink"
```

问题解决。

```shell
$ st-flash --reset write firmware.bin 0x8000000
st-flash 1.8.0
libusb: warning [winusb_get_device_list] failed to initialize device 'USB\VID_xxx'
2025-11-23T18:10:29 INFO common.c: STM32F42x_F43x: 256 KiB SRAM, 2048 KiB flash in at least 16 KiB pages.
file firmware.bin md5 checksum: 597e459b0cb9649579ffd7e7deefb, stlink checksum: 0x000018ab
2025-11-23T18:10:29 INFO common_flash.c: Attempting to write 508 (0x1fc) bytes to stm32 address: 134217728 (0x8000000)
EraseFlash - Sector:0x0 Size:0x4000 -> Flash page at 0x8000000 erased (size: 0x4000)
2025-11-23T18:10:30 INFO flash_loader.c: Starting Flash write for F2/F4/F7/L4
2025-11-23T18:10:30 INFO flash_loader.c: Successfully loaded flash loader in sram
2025-11-23T18:10:30 INFO flash_loader.c: Clear DFSR
2025-11-23T18:10:30 INFO flash_loader.c: enabling 32-bit flash writes
2025-11-23T18:10:30 INFO common_flash.c: Starting verification of write complete
2025-11-23T18:10:30 INFO common_flash.c: Flash written and verified! jolly good!
```

## 其他工具

- [gcc-arm-none-eabi](https://developer.arm.com/Tools%20and%20Software/GNU%20Toolchain): `scoop install gcc-arm-none-eabi`
- [make](http://www.gnu.org/software/make/): `scoop install make`

## 开发板

![board](/img/nucleo-144-board.jpg)

![shell](/img/nucleo-144-board-shell.jpg)

[外壳模型](https://makerworld.com.cn/zh/models/304239-nucleo-144wai-ke-stm32)

{{< note >}}
最好去掉外壳上下的定位柱，因为那些柱子需要把 2.54 排针插进去
。但由于 3D 打印的精度问题，会导致插不进去，从而装不上外壳。
{{< /note >}}
