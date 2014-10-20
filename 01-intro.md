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
