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

7
我在做elf解析那部分的时候发现proj2的ucore.img编译过程中会发生错误。错误是ld 
生成出来的bootblock..out的大小是560字节超过了510字节...... 
我想是不是我的编译工具的版本不对还是别的问题。 
我的环境是Fedora16, gcc 4.6.2 , ld 2.21.53.0.1-6.fc16 
proj1生成的bootblock.out的大小是456字节，如果这个也和标准结果不一样那就很有可能是环境的问题。 

```
这种现象主要是由于GCC照成的。新版本的GCC在生成执行代码时产生了更多的数据，导致bootloader的size超过了510字节。 
建议的解决方法： 
1 在Makfile中增加gcc的编译选项 -Os ， 目的是做空间优化，减少生成代码的size。 
2 用老版本的gcc, 比如gcc 4.3等，我记得fedora和ubuntu中也可以安装老版本的gcc软件包 
3 用 virtualbox 和做好的磁盘镜像, 磁盘镜像中的ubuntu linux包含了编译和运行labs需要的各种软件。
```

8
操作系统课程的实验部分有时候需要汇编语言的知识。然后有些语法一时很难查到具体什么意思。不知到有没专门此类介绍汇编语言语法的网站呢？或者说还是先略过这些实验然后先对操­作系统有一个大致的了解（比如看后续的课件）？ 

```
OS实验用的是GNU AS 汇编器和GNU CC内联汇编技术，相关文档如下： 
X86的指令集详细文档，可查到所有的X86汇编：ia32-instrset.pdf 
http://www.bytelabs.org/pub/ct/arch/intel/ia32-instrset.pdf
是所有的机器指令说明文档。此目录下的另外两个文档对理解x86硬件工作模式和硬件系统系编程很有帮助。 
中文介绍文档：Linux 中 x86 的内联汇编 
http://www.ibm.com/developerworks/cn/linux/sdk/assemble/inline/index.html
英文GNU汇编专业电子书，掌握此书，将是汇编绝对高手
《Professional Assembly Language》英文版 ，作者 Richard Blum 
http://forum.eviloctal.com/thread-31189-1-4.html
另外可以通过 google等搜索 "GNU" "Assembly" 或 “GNU” “汇编”等查找相关信息
```

