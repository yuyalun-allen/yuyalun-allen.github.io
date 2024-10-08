---
date: 2023.08.17
---
> 每次换电脑的时候都要重新搭建一次熟悉的软件环境，因为大多数时候默认的配置都不那么顺心。~~比方说Powershell更换白色主题后会出现白底白字的情况，这神仙也看不出来是什么内容吧。~~ 因为个人还是使用Windows多一些，曾经用过一段时间Ubuntu + Gnome桌面环境，但是一方面国内这些糟糕的软件适配确实比较劝退，另一方面开箱即用的体验也会省去很多麻烦。~~说你呢，鹅厂，微信就算推出儿童手表版也没有Linux版是吧。很多游戏需要依托wine转义才能运行也非常麻烦。~~ 

> 所以以下配置是关于Windows + wsl的软件配置， ~~（Windows是最佳Linux发行版，不是）~~ 不会涉及到Linux窗口管理器和Linux桌面环境的内容。

> 持续更新……

Windows
---

# Scoop
[scoop](https://scoop.sh/)是类似于linux中apt、pacman的包管理器，虽然Windows下的包管理器仓库软件生态不如Linux丰富广泛，但是如果对应的软件，尤其是编程环境中涉及的软件，提供了scoop的安装方式，无疑是非常省心的。比如nodejs、python、curl、wget。

*当然既然在Windows下，也未必需要依赖包管理器，像Git、python、nodejs这种直接去官网下载安装程序也非常方便，scoop只是提供了另外一种渠道。*

``` Powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser # Optional: Needed to run a remote script the first time
irm get.scoop.sh | iex
```

# Git
[git](https://git-scm.com/)的重要性无需赘述。直接到官网下载，安装即可。

*为了方便后续命令行使用，注意在安装时勾选**创建Windows Terminal  git bash Profile***

# Windows Terminal美化
Windows Terminal在我理解中是为Windows下不同的Shell（包括刚才提到的git bash、powershell）提供统一的GUI接口，作用当然是为了美观啦，虽然默认的状态不怎么美观就是了。而且为了避免白底白字这种逆天操作，还是需要自己DIY一下。

*在Terminal的json配置文件中可以设置背景图片和背景透明度哟，是不是一下子就不单调了呢。*
![](/assets/images/2023-08-18/屏幕截图%202023-08-18%20203343.png)

## Powershell
```Powershell
echo $PROFILE
```
找到Powershell的配置文件位置，设置字体颜色，如果你也喜欢白色主题，可以参考一下我的配置
```Powershell
# syntax highlighting colors

Set-PSReadLineOption -Colors @{
  Command            = 'Black'
  Number             = 'DarkGray'
  Member             = 'DarkGray'
  Operator           = 'DarkGray'
  Type               = 'DarkGray'
  Variable           = 'DarkGreen'
  Parameter          = 'DarkGreen'
  ContinuationPrompt = 'DarkGray'
  Default            = 'DarkGray'
}
 
# Logging / Progress colors
 
$p = $host.privatedata
$p.ErrorForegroundColor    = "Red"
$p.ErrorBackgroundColor    = "White"
$p.WarningForegroundColor  = "Yellow"
$p.WarningBackgroundColor  = "White"
$p.DebugForegroundColor    = "Yellow"
$p.DebugBackgroundColor    = "White"
$p.VerboseForegroundColor  = "Black"
$p.VerboseBackgroundColor  = "White"
$p.ProgressForegroundColor = "DarkGray"
$p.ProgressBackgroundColor = "White"
```



Wsl（Arch版）
---

# Arch安装
1. 开启wsl功能（参考[这里](https://zhuanlan.zhihu.com/p/35801201)）
``` Powershell
wsl --update #更新必要的wsl内核
wsl --install #默认安装ubuntu，如果要安装arch，请执行上一行
```
2. [安装指南](https://github.com/yuk7/ArchWSL/blob/master/i18n/README_zh-cn.md)，获取arch安装程序和初始化磁盘镜像，详细步骤看[这里](https://wsldl-pg.github.io/ArchW-docs/How-to-Setup/)(不多废话了，RTFM)

> Tip:想要愉快地使用pacman需要更换国内仓库源，Arch的维护者已经帮我们想到了这一点，只需要把文件中对应行解注释即可 

``` bash
vim /etc/pacman.d/mirrorlist
```

*感觉一入Arch深似海，之前用Ubuntu或者Debian的时候都没有想要主动学习系统维护相关的知识，可能**一方面**是Arch特性就是依赖使用者自行维护，**另一方面**Arch社区[文档](https://wiki.archlinux.org/)维护得真的非常好，非常利于用户学习。让我们一起成为superuser吧！*

# Linux常用工具套件
- [Oh my bash](https://github.com/ohmybash/oh-my-bash)：让你的bash用起来更顺手，当然你也可以选择使用[fish](https://fishshell.com/)作为交互shell。
- Git：不再赘述
- tmux：终端多任务必备工具
- vim：快速进行文本编辑，当然大佬也可以把vim打造为一款IDE，但我选择不折磨自己。
- vs code server：IDE当然是选择vs code啦！在wsl中安装vs code server非常简单，只需要本地安装vs code，然后在wsl中键入`code`，就会自动下载安装。

这里分享一下我的[dotfiles](https://github.com/yuyalun-allen/dotfiles)，如果你喜欢的话可以参考使用！