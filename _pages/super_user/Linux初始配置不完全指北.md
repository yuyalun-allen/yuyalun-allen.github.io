---
date: 2024.09.18
---

# Linux初始配置不完全指北

> 为什么不使用Windows系统呢？可能是Linux系统的开源性、稳定性、安全性、自由性等等原因吧。~~也可能是因为Windows系统的升级策略、隐私策略、广告策略、商业策略等等原因吧。~~

本文主要涉及如何从 *archlinux* 的 iso 文件到一个可以日常使用的操作系统，包括但不限于：

1. 安装系统
2. 安装桌面环境
3. 安装常用软件
4. 配置系统
5. 配置软件

> Disclaimer: 本文仅供参考，不保证完全正确，不保证完全适用于你的系统，不保证不会出现问题，不保证不会损坏你的系统，不保证不会损坏你的数据，不保证不会损害你的人身财产安全，不保证不会引起任何不良后果。请谨慎操作，自行承担风险。本文仅提供部分环节的细节参考，完整的过程请参考[官方文档](https://wiki.archlinux.org/title/Main_page)，RTFM。

## 安装系统

前置步骤下载镜像、制作启动盘、U盘启动、连接网络等等略过，按照官方文档操作即可。略微复杂的地方可能是分区和挂载，这里简单介绍一下。
首先要明白无论你通过何种方式制作了启动盘，启动盘可以将一个完整的系统加载到内存中，而分区、挂载和安装事实上就是将你的固态硬盘制作为一个可以启动的系统的过程。
按照官方文档的步骤，你需要将你的硬盘分为几个分区，分别是：

1. EFI分区（1G）；
2. Swap分区（约等于内存大小，如32G）；
3. Root分区（剩下的所有空间）；

其中EFI分区如果采用UEFI启动方式，现代处理器和主板都支持，可以选择GPT分区表，否则选择MBR分区表。Swap分区是用来作为内存不足时的临时存储，一般设置为内存大小的两倍，但是现代内存足够大，可以设置为内存大小即可。Root分区是系统的根目录，所有的文件都存放在这里。

安装系统最后想要提醒的就是需要设置引导程序，如果你的选择UEFI启动方式，可以使用`efibootmgr`，更通用一些并且配置简单可以直接使用`grub`。这里提供更简洁的引导方式使用`efibootmgr`，因为它引导过程非常简单，没有`grub`那样的选择系统界面。

假定你按照上述方法分区，想要使用zen内核，从swap分区中恢复休眠，可以使用如下命令设置引导：

```bash
sudo efibootmgr --create \
 --disk /dev/nvme0n1 --part 1 \
 --label "Arch Zen" \
 --loader /vmlinuz-linux-zen \
 --unicode 'root=/dev/nvme0n1p3 resume=/dev/nvme0n1p2 rw quiet initrd=\initramfs-linux-zen.img'
 ```

> Tip：ChatGPT 指出了使用设备位置作为引导可能引起安全问题，可以使用UUID或者LABEL来代替，具体方法请参考[官方文档](https://wiki.archlinux.org/title/EFISTUB)。但是我认为这种方式更加简洁（正常人哪能完整打完UUID且不出错呢），而且在单系统的情况下，安全性问题不大。

## 安装桌面环境

选择一个你喜欢的桌面环境安装即可，如果喜欢Mac风格，可以选择`gnome`，如果喜欢Windows风格，可以选择`kde`，如果喜欢自定义极简平铺式风格，可以选择`i3`或`sway`。
个人很喜欢`gnome`，使用如下命令可以安装完整的gnome桌面环境，包括窗口管理器（`mutter`）、桌面环境（`gnome-shell`）、应用程序（`gnome-apps`）、显示会话管理器（`gdm`）等等。

```bash
sudo pacman -Sy gnome
```

这里想要指出默认`gnome`安装的一个坑，现代`gnome`默认安装`wayland`作为其显示服务器，而且不提供`Xorg`服务器的安装，导致无法启动`Xorg session`。如果你的显卡驱动不支持`wayland`，可以使用如下命令安装`Xorg`服务器：

```bash
sudo pacman -Sy xorg-server
```

也有一个简单的方法，可以通过安装sddm来解决这个问题，sddm是一个简单的显示会话管理器，可以在`gdm`抽风的时候有个备选，可以选择`Xorg`或`wayland`作为显示服务器。安装sddm的命令如下：

```bash
sudo pacman -Sy sddm
```

## 安装常用软件

提供一个常用软件list，可以根据自己的需求安装：

- vim
- git
- tmux
- oh-my-bash
- firefox
- kitty
- ...

