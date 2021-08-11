---
title: linux 源码包安装拾遗
date: 2021-08-11 22:01:06
categories: 
    - Coding
tags:
    - 随笔
    - 后端
---

不要当一个迂腐的老实人。要宽容，但不要纵容；要大方，但不要任人宰割；你可以打破规则，但要有底线。

<!--more-->

![呼伦贝尔-秋千女孩](https://lilu-pic-bed.oss-cn-beijing.aliyuncs.com/my-blog/20210811-linux-pkg-install-in-source/swing-girl-hulunbeier.jpeg)

## 源码包安装和 `apt-get/yum` 的区别
#### 安装前的区别：概念上的区别
- `rpm` 和 `dpkg` 包是经过编译过的包，并且其安装位置由厂商说了算，厂商觉得安装在哪里合适，就会装在哪里，而源码包则是没有经过编译的文件，大部分由c语言写的，需要gcc编译器进行编译使用，如同java源文件和二进制文件的区别。
- 源码包执行效率更高。
#### 安装后的区别：安装位置不同
- `rpm` 和 `dpkg` 包的安装位置是厂商说了算，而源码包是自己说了算。
- 以下是 `RPM` 包默认安装路径，仅供参考：
    - `/etc/` 配置文件安装目录
    - `/usr/bin/` 可执行的命令安装目录
    - `/usr/lib/` 程序所使用的函数库保存位置
    - `/usr/share/doc/` 基本的软件使用手册保存位置
    - `/usr/share/man/` 帮助文件保存位置

## 源码包安装位置
- 源码包保存位置一般是 `/usr/local/src/`
- 安装位置一般是：`/usr/local/软件名/`
    - 这个相当于 windows 下的 program files
- `RPM` 和 `dpkg` 包安装的服务可以使用系统服务管理命令（service）来管理，例如RPM包安装的apache的启动方法：
    - `/etc/rc.d/init.d/httpd start`
    - `service gttpd start`
- `service` 找的就是 `/etc/rc.d/init.d/` 目录下的启动文件

## 源码包安装三部曲

#### `./configure` 软件配置与检查
- 定义需要的功能选项
- 检查系统环境是否符合安装要求
- 把定义好的功能选项和检测系统环境的信息都写入将要生成的 `Makefile` 文件，用于后续的编译。
- `./configure --prefix=/usr/local/apache2`

#### `make` 编译
- 如果编译失败。可以使用 `make clean` 来清理

#### `make install` 编译安装
- 将编译完后的文件复制到目标文件夹

## 源码包卸载
- 不需要卸载命令，直接删除安装目录即可，不会遗留任何垃圾文件。

## `.sh` 脚本安装
- 所谓的的一键安装包，实际上还是安装的源码包与RPM包，只是把安装过程写成了脚本，便于初学者安装
- 优点：简单、快速、方便
- 缺点：
    - 不能定义安装软件的版本
    - 不能定义所需要的软件功能
    - 源码包的优势丧失