---
date: 2023.09.01
---

> 最近Spring框架用的也很多了，但是想要参加一些面试，不管是大学也好、大厂也好都对于原理理解要求比较高。要想深入理解原理呢，应用层面的经验就不太充分了，因此要进入到源码层面。

> 这一篇记录一下环境搭建的过程，比较全面，也比较麻烦，希望对你有帮助。

> 环境：wsl2 -> arch linux

# 编译Spring
## Spring源码准备
这个很简单，只需要 `git clone https://github.com/spring-projects/spring-framework.git` 即可。

这里要提一下准备环境最麻烦的问题，网络。说句不恰当的，在中国互联网中配环境简直是天崩开局，一方面由于墙的存在访问外网非常缓慢，另一方面中文的资料质量真的参差不齐。

解决网速问题可以采用两种方式：
1. 最方便的是访问镜像站，镜像站又包括几类，
    1. Linux发行版仓库镜像站（arch中很简单，只需要解注释`/etc/pacman.d/mirrorlist`中对应的行即可）；
    2. 各种编程语言包仓库的镜像站，如python、java等，往往有阿里云、清华、中科大等镜像站可选；
    3. 某些github上著名的项目gitee上往往会有对应的镜像。
2. 实在没办法还可以配置代理
    1. 全局代理可以通过设置`http_proxy`/`https_proxy`/`all_proxy`等环境变量的方式配置；
    2. 不同的软件还需要额外配置本软件的代理才行，比如git、gradle等

## Java工程构建工具gradle
gradle的下载很简单只需要`sudo pacman -S gradle`即可。麻烦的是构建项目中需要jdk21，而pacman的core/extra/community中都没有jdk21，因此摆在面前的就两条路
1. 自己从源码编译openjdk
2. 使用万能的AUR（Arch User Repository）

这两条路我都试过。~~别问，问就是openjdk编译错版本了~~ 

其实手动编译jdk也不困难，官方有[详尽的教程](https://github.com/openjdk/jdk/blob/master/doc/building.md)，这里提两点建议
1. 如果你是没有耐心的人只要每次遇到缺少工具的错误，搜索一下对应的工具名称即可，我印象中包括但不限于autoconf/alsa/x11等；
2. 做一个有耐心的人，文档中下方给出了各种依赖包的完整解决方案，照做即可。

最后吹一波AUR，jetbrains提供了jdk21编译好的版本，clone对应的PKGBUILD文件后，执行`makepkg -si`就可以安装完整的jdk21。

按照[教程](https://github.com/spring-projects/spring-framework/wiki/Build-from-Source)，就可以顺利编译Spring源码啦。

Happy Reading!
---
