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

6 特别是各种garbage collection的内存管理优化技术等有很好的介绍
http://www.google.com/url?sa=D&q=http://www.cs.kent.ac.uk/people/staff/rej/gc.html&usg=AFQjCNEo4g_Ss36ewwwrChke-GDjKpHQGA
感兴趣的同学可以瞧瞧。
感谢提供此信息的Yang Xi (the Ph.D. student of Prof. Steve Blackburn) ! 

