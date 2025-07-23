+++
title = '安装使用 NixOS-WSL'
date = 2025-07-16T23:58:24+08:00
draft = false
toc = true
math = false
tags = ["NixOS", "WSL", "Windows", "note"]
+++

最近在折腾 [Nix](https://nixos.org/)，打算在我的日常 Windows 机器上安装 NixOS，所以尝试了 [NixOS-WSL](https://github.com/nix-community/nixos-wsl)。在安装过程中碰到了一些问题，于是就有了这篇记录。

## 启用 WSL2

对于新系统来说，可以在有**管理员权限**的 PowerShell 或 CMD 中运行以下命令来启用 WSL2：

```powershell {linenos=false}
wsl --install --no-distribution
```

### 手动安装

对于较早的系统来说，则需要分别启用以下可选功能：

- [适用于 Linux 的 Windows 子系统](https://learn.microsoft.com/en-us/windows/wsl/about)

```powershell {linenos=false}
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart`
```

- [虚拟机平台](https://learn.microsoft.com/en-us/windows/wsl/troubleshooting#error-0x80370102-the-virtual-machine-could-not-be-started-because-a-required-feature-is-not-installed)

```powershell {linenos=false}
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

重启完成后，还需要安装[WSL 2 内核更新包](https://github.com/microsoft/WSL2-Linux-Kernel)。

安装完成后，可以通过以下命令设置默认版本：

```powershell {linenos=false}
wsl --set-default-version 2
```

## 安装 NixOS-WSL

接下来就是到 [NixOS-WSL 的 Release 页面](https://github.com/nix-community/NixOS-WSL/releases)下载 `nixos.wsl`，并通过以下命令进行安装：

```powershell {linenos=false}
wsl --install --from-file <path-to-nixos.wsl> --location <path-to-install> --name NixOS --version 2
```

{{< tip >}}
也可以直接双击 `nixos.wsl` 文件来进行安装。
{{< /tip >}}

如果你安装过 2.4.4 版本前的 NixOS-WSL，则可以通过 `--import` 来安装新版本：

```powershell {linenos=false}
wsl --import NixOS $env:USERPROFILE\NixOS <path-to-nixos.wsl> --version 2
```

安装完成后，可以通过以下命令查看已安装的发行版：

```powershell {linenos=false}
wsl --list --verbose
```

进入 NixOS-WSL：

```powershell {linenos=false}
wsl -d NixOS
```

设置默认的 distribution 为 NixOS：

```powershell {linenos=false}
wsl -s NixOS
```

## 更新 NixOS-WSL

在安装完成后，需要先运行一次以下命令来更新 channels，才能正常使用 `nixos-rebuild`：

```shell {linenos=false}
sudo nix-channel --update
```

如果使用的是 flake，则需要运行以下命令来更新：

```shell {linenos=false}
nix flake update
```

Rebuild 系统配置：

```shell {linenos=false}
sudo nixos-rebuild switch
```

## 问题记录

如果你在安装了 NixOS-WSL 后，发现 `wsl -d NixOS` 一直卡住不动，那么大概率你的 WSL 默认版本不是 2，可以通过以下命令来检查：

```powershell {linenos=false}
wsl --list --verbose
```

如果你看到 NixOS 的版本是 1，那么可以通过以下命令来设置 NixOS 的版本为 2：

```powershell {linenos=false}
wsl --set-version NixOS 2
```

然后应该就能正常进入 NixOS-WSL 了。

## 学习 Nix

- [Zero to Nix](https://zero-to-nix.com/)，推荐
- [NixOS 与 Flakes](https://nixos-and-flakes.thiscute.world/zh/)，推荐
- [NixOS 中文](https://nixos-cn.org/)
