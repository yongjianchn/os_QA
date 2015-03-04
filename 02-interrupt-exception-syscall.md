##基础知识

Q1:
段寄存器的内容是否就是段选择子？
段选择子中的RPL部分除了在CS的情况下有判断请求特权级的作用之外，对DS、SS等其他段寄存器而言是否有意义？ 

A1:
下面链接很好地解释了DPL、RPL和CPL的含义和关系。
http://hi.baidu.com/ozwarld/item/fa6120471e4d91e7bdf45182

##Ucore实验相关

Q1:
请问end是在哪里定义的

文档说bootloader加载ucore的结束地址放在变量end里面了，但是我grep end都是extern char end[]的declaration，并不是定义。我把所有二进制文件nm了一遍都显示Undefined，但是编译没有错误，求教这个变量是在哪里定义的。。

A1:
end是由链接脚本tools/kernel.ld定义的，请查看该文件中类似于PROVIDE(xxx = .);这样的行

lab2里练习5的第一问需要设置Page Fault的中断处理程序，中间需要调用一个叫做pgfault_handler的函数，我看过所有的.h文件里面都没有这个函数。请问助教
这个函数在哪里，它的原型是什么？ 
ps.应该不是直接调用do_pgfault函数，这个函数需要Struct mm*作为参数，在中 
断处理过程中是根本无法获得它的
答：
http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OS2011/lab2%3Faction%3DAttachFile%26do%3Dget%26target%3Dlab2-20110316-v2.tar.bz2&usg=AFQjCNEgiM0QS1kpMoSzjX6oL-oa70ANQQ

homework2实验相关第二题：lab2的ucore的小os的load addr和link addr分别是多少？ 
我从kernel.ld得到load addr是0xC0100000，但不知道link addr从哪里得到，需要虚拟地址还是物理地址？希望老师给出答案。 
答：
ucore 设定了ucore运行中的虚地址空间，具体设置可看 memlayout.h "Virtual memory map: "图 
所以 tools/kernel.ld描述的是 link_addr , link addr是0xC0100000 是一个虚地址，在ucore建立内核用页表时，设定的虚实映射关系是： 
phy addr + 0xC0000000 = virtual addr 
但bootloader把OS load到内存，那时还没有启动页表映射，用的是bootloader阶段设置的段模式，其映射关系(bootasm.S最后有段描述符表)是： 
linear addr = phy addr = virtual addr 
所以查看 bootloader的实现代码 bootmain::bootmain.c 
readseg(ph->p_va & 0xFFFFFF, ph->p_memsz, ph->p_offset); 
p_va=0xC0XXXXXX 
但 ph->p_va & 0xFFFFFF= 0x0XXXXXX 
了所以bootloader load OS的load addr是0x0XXXXXX, 是物理地址，这个是通过分析bootmain函数的实现得到的。 
简言之： OS的link addr 在tools/kernel.ld中设置好了，是一个虚地址 
OS的load addr在bootmain函数中指定了，是一个物理地址 

陈老师： 
> 我在做elf解析那部分的时候发现proj2的ucore.img编译过程中会发生错误。错误是ld 
> 生成出来的bootblock..out的大小是560字节超过了510字节...... 
> 我想是不是我的编译工具的版本不对还是别的问题。 
> 我的环境是Fedora16, gcc 4.6.2 , ld 2.21.53.0.1-6.fc16 
> proj1生成的bootblock.out的大小是456字节，如果这个也和标准结果不一样那就很有可能是环境的问题。 
> 祝好! 
答：
这种现象主要是由于GCC照成的。新版本的GCC在生成执行代码时产生了更多的数据，导致bootloader的size超过了510字节。 
建议的解决方法： 
1 在Makfile中增加gcc的编译选项 -Os ， 目的是做空间优化，减少生成代码的size。 
2 用老版本的gcc, 比如gcc 4.3等，我记得fedora和ubuntu中也可以安装老版本的gcc软件包 
3 用 virtualbox 
http://www.google.com/url?sa=D&q=http://www.virtualbox.org&usg=AFQjCNG4LsAoAEACyWRaH97HSLecwplLew和做好的磁盘镜像 
（http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OS2011%3Faction%3DAttachFile%26do%3Dget%26target%3Dlab4student2011.7z&usg=AFQjCNFEu7K1oRwWaDRnP6QeBK-OiFbLKA）。
磁盘镜像中的ubuntu linux包含了编译和运行labs需要的各种软件。
