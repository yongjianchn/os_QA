##基础知识

##uCore实验相关

1 能否不使用提供的虚拟机镜像来完成实验？

```
可以，只需要uCore的编译环境（编译器系列工具），QEMU（模拟器）就可以编译和运行uCore
```

2 make qemu或者make qemu-nox之后如何停止？

```
鼠标点击QEMU窗口的'x'按钮，或者开启新的命令行窗口输入'killall qemu-system-i386'
```

3 Windows下如何配置uCore实验环境？

```
可参考一个学生的配置教程[链接](http://pan.baidu.com/s/1i3JxZZR)
```

4  明明安装了QEMU，却依然报 Couldn't find a working QEMU executable.

```
大部分时候安装QEMU的可执行文件名叫qemu-system-i386或者其他，而不是叫qemu
针对这种情况只需要：
1. which qemu-system-i386
查看 qemu-system-i386 的完整路径，例如 /usr/bin/qemu-system-i386
2. ln -s /usr/bin/qemu-system-i386 /usr/bin/qemu
用刚才查到的qemu-system-i386的绝对路径创建一个软链接
```

5 之前没用过arm，在配置到android list avd显示
Available Android Virtual Devices:
Name: ucore
Path: /home/lyk423/.android/avd/ucore.avd
Target: Android 4.1.2 (API level 16)
ABI: armeabi-v7a
Skin: WVGA800
之后执行./uCore_run出现了
AVD not specified. Fetch the first avd from "android list avd" and get "ucore"
emulator: ERROR: Could not load OpenGLES emulation library: libOpenglRender.so: cannot open shared object file: No such file or directory
emulator: WARNING: Could not initialize OpenglES emulation, using software renderer.
无法启动是什么原因呢？

```
请检查/bin/sh指向的是哪个shell，如果是dash的话会因为dash不支持某些shell语法导致编出来的image里初始地址不正确，把/bin/sh指向/bin/bash即可。
```

6 下断点但是程序根本停不下来

```
我这里是自己原来的Ubuntu 12.04 32bit, 使用 apt-get 直接安装的 qemu，版本为 QEMU emulator version 1.0 (qemu-kvm-1.0)，遇到的问题是下0x7c00的断点但是并不能停止而是直接继续启动完了。
 
后来查到相关问题，偶然尝试而解决，方法是在启动 qemu 时加入参数 -no-kvm，可以加到 Makefile 里方便使用。
 
在正常运行的同学那里看到，在用 qemu -monitor stdio 启用 qemu 自己的命令行中使用 info kvm 可以看到默认为禁用的，但是我这里默认为启用。
 
默认设置为什么不一样，原因不明…… kvm 支持为什么会造成断点失效，原因不明……解决方法仅供参考～
```

