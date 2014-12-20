# QQ群聊天记录精选（请勿转载）

## 20141016QA

A：同学在实验中碰到的问题，由于我们目前还缺少直接连接到同学实验环境的手段，所以目前想到的方法是请做实验的同学能够在github上fork mooc os lab,　建立自己的git repo（通过github网站可很容易完成）。然后把出现的问题直接写到github上对应实验的目录下。比如完成了lab1,把lab1合并到lab2代码中，然后完成lab2的过程出了错误，需要帮助，则可以在lab2下写一个文档test_err.txt，内容是你碰到的问题，把输出也写出来，你的困惑等。我们的助教会争取在同一个目录下写test_err_feedback.txt；说明我们的想法，对问题的理解，以及可能的修改等，这需要请各位把我们的助教的id加为你的git repo的contributor。目前，可以尝试加入chyyuu objectkuan xuyongjiande。我们的email是 yuchen@tsinghua.edu.cn ，xuyongjiande@gmail.com ，objectkuan@gmail.com 。

Q：那tss该定义在gdt段里还是ldt段里

A：Gdt里面  

Q：我定义一个ldt段时 其对应的dpl和rpl有什么意义

A：ucore实验不用考虑有ldt，tss在gdt中定义了，可以看实验的视频和代码。有讲到。x86的硬件细节比较复杂，为了减少学生学习的负担，我尽量不讲与ucore实验无关或对ＯＳ原理等不太必要的细节，否则会成为另外一门课“计算机原理”了。另外，尹治宏(yinzhihong12@msn.com)同学在lab1学习中增加了一个有趣的功能“用键盘实现用户模式内核模式切换”，我已经添加到 https://github.com/chyyuu/mooc_os_lab中，mooc_os_lab 下的
labcodes_answer/lab1_result/下 ，主要修改了trap.c代码，有兴趣的同学可以看看！
任务切换其实应该到讲进程时才涉及到。慢慢来吧。lab2还有其他挑战。

Q：default_pmm.c里面default_check通过的话，可以保证default_alloc_pages和default_free_pages正确吗？我的代码可以通过测试，不过和答案里面的差别挺大  

A：通过测试表明测试的那些用例下代码是对的。

陈渝老师：在　http://chyyuu.github.io/mooc_os/ 　和　http://chyyuu.gitbooks.io/mooc_os_info/content/ 上更新了答疑信息，请大家有空看看。希望这样答疑更有效率。
@操作系统公开课的学生 如果没有解决，可以看看试试基于github的实验交互方式？目前已经把代码放到了github上，也把其他大部分资料（音视频不适合，除外）放到github上了，这样大家共享会更加方便。接下来是完善扩展内容。我们也希望和鼓励大家一起完善资料（指出我们的错误，帮助我们增加或改进代码和文档内容），使得我们一起进步。多谢！
 
## 20141018QA

Q：bootmain系统启动的时候为什么p_va & 0xffffff，为什么要&操作  

A： 这是地址转换操作
http://blog.chinaunix.net/uid-368446-id-2414228.html   
lab 1: 从bootloader跳转到内核。
http://geezer.osdevbrasil.net/osd/ram/index.htm 
Physical memory layout of the PC

Q：ucore和日本人写的《30天自制操作系统》有什么差异？  

A：区别很大，基本的操作系统原理都差不多，但是里面内容差的很多；段页式内存管理，虚存管理，用户进程，VFS，文件系统方面ucore深入些，那本书在界面方面很丰富。
由于有些问题或答案是由图片形式给出的，在聊天记录中没有体现，无法整理，敬请谅解。

## 20141025QA

Q：之前没有计算机编程和计算机原理等方面基础，做实验感觉不知道从何入手。不知道您是否可以给些建议。 

A：如果这样，建议在听课的同时或之前，自己学习一下计算机原理，了解基本的CPU和计算机系统结构，然后就可以看ＯＳ原理方面的视屏了。如果要想动手编程，最好再自学C语言（有比较好的网上教学视屏），如果没有学习C语言或基本的数据结构知识，建议不必做实验

Q：可否推荐一下C语言方面视频或者链接？  

A：google 搜索一下“李明　C语言”

Q：计算机原理方面，看什么比较好呢

A：软件硬件接口 csapp　是很好的课。
易->难或先后顺序: python, tsql, C, 数据结构，计算机原理，OS；tsql不是必须的。
英语对搞计算机的来说还是很重要的。数据结构可以看清华的学堂在线的邓俊辉老师的课，”计算机组成与设计:硬件/软件接口“和CMU的一本书“深入理解计算机系统”是很不错的计算机原理的书  
COLDFIRE：我建议先学C语言，搞懂结构体，链表等，然后数据结构，算法导论，操作系统原理，编译原理

Q：关于帮助理解硬件方面的那个手册有没有书？Intel方面的

A：http://www.intel.com/content/www/us/en/processors/architectures-software-developer-manuals.html  
https://www.coursera.org/course/pkuco   
https://www.coursera.org/course/comparch   

Q：老师 x86 mmu是硬件实现的(记得课程有说)mips 是软件? 那 arm也是软件？

A：MMU都是硬件实现的，只是MMU中的ＴＬＢ单元产生了ＴＬＢ项缺失的时候，是硬件自动完成从页表到ＴＬＢ的拷贝，还是硬件产生中断，由软件来完成这个拷贝工作。这是两种选择。x86, arm是第一种选择；mips是第二种选择，这里说的x86, arm, mips都是指的硬件架构，或CPU架构，cpu architecture决定了这个cpu的指令集和功能等。
计算机领域学习路线图 https://github.com/halida/blog/blob/master/%E8%AE%A1%E7%AE%97%E6%9C%BA%E9%A2%86%E5%9F%9F%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%BA%BF%E5%9B%BE.rst  
arm是架构，mips也是架构，也是指令集

陈渝老师：lab3的实验指导书已经更新，大家可以访问　
http://objectkuan.gitbooks.io/ucore-docs/  
答案在：https://github.com/chyyuu/mooc_os_lab/tree/master/labcodes_answer  
如果没有时间做，也可看看是如何具体实现的。
 
## 20141122QA

Q：context switch的时候线程的状态是不是要保存

A：是，主要是线程的上下文信息，线程可以看成是进程的一部分（表示进程的控制流），或者是一种特殊的进程（比如linux os）

Q：那进程间可以切换，线程间可以吗 

A：也可以切换呀。 

Q：关于进程切换代价大，具体是大在哪里呢？ 

A：进程切换需要切换页表。处于同一进程内的线程切换不需要切换页表。这会导致TLB中的页表项缓存都失效了，使得内存访问开销大了。

Q：陈老师还有一个问题，在一个arm平台移植了Linux内核，如何给他构建发行版呢？

A：我没做过。不过我记得ubuntu, fedora都有arm版本。现在还没有ARM的桌面机或服务器，不过我觉得明年就会有了！ 

Q：两个任务，编译任务和视频播放任务，都耗cpu，都有io和睡眠，在cpu超负荷的情况下，调度器肿么保证视频播放的实时性，怎么保证视频不卡。调度器肿么知道对视频播放任务必须要比编译任务好？

A：如果是linux或其他通用OS，编译任务和视频播放任务都不是实时进程。现在有显卡硬解码，所以视频播放任务基本上耗CPU不大。如果没有类似linux cgroup机制，其实现在的调度器没法保证视频播放的实时性。调度器不知道对视频播放任务必须要比编译任务好。但通过类似linux cgroup机制，可以确保给视频播放任务的CPU占用比率，从而在一定程度上可以保证频播放的实时性。编译任务，比如编译linux kernel的任务，其实算是CPU密集型。现在的linux的O(1), CFS等调度器，在进程没有耗尽cpu的情况下，其实主要基于分时进行调度，且保证一定的公平性，这样一般就够了。

Q：在PC机上，VMware，Virtual Box这样虚拟化以后可以运行windows,linux
那在ARM，手机芯片上有没有做这样的软件，虚拟化后跑android的和IOS。（IOS好像不开放，PC上VMware不知道是不是支持）  

A：新一代的ARM CPU支持一定程度的硬件虚拟化，明年arm64用于服务器后，应该可以有这样的能力。  
www.rtems.com   www.baike.com/wiki/rtems   
http://www.sylixos.com/   

Q：就是作为学生 有必要深入理解makefile么  

A：如果是学os, 不必。或者说非不得已，没必要深入理解makefile。其实你只需看makefile带来的编译过程即可，这是为了你知道一个os是如何编译出来的。至于它如何编写，其实在lab1中没有要求。

Q：请问"屏障管理"有什么用途?  

A：屏障管理，我理解是 barrier机制，是一种同步手段。

Q：并行文件系统GPFS设计和普通的文件系统设计有什么不同？需要特定的硬件支持吗？  

A：不同很大呀，一个面向集群系统，一个只是单机系统可以这样实现。比如google fs--GFS也是这么做的，且实现在用户态。
也有实现在内核态的分布式并行文件系统，比如linux中的ceph，然后进了内核。
 
## 20141203QA

陈渝老师：http://os.cs.tsinghua.edu.cn/oscourse/ 有清华历年os课的一些wiki记录 

向勇老师：http://os.cs.tsinghua.edu.cn/oscourse/OS2013/projects/U12   ，我不确定同学们是否能访问。如果可能，在完成课程的8个基本实验后，仍然有兴趣的同学，可以参与到我们的ucore+完善工作中。

Q：linux内核的问题。内存管理部分有点各种绕不明白，有个小问题希望大家帮忙...
在内存管理的初始化阶段，Linux内核使用buddy system替换初始阶段的bootmem分配器，在meminit()实现

```
meminit()
  |--->free_unused_memmap_node(node);
               |--->free_memmap(node, prev_bank_end, bank_start);
                         |--->free_bootmem_node(NODE_DATA(node), pg, pgend - pg);
                  
```

free_unused_memmap_node这个函数搞不懂，看名字大概就是说释放掉未使用的页。我的理解，unused的意思大概也就是说bank与bank之间有些不可用的地址，需要找到这些地址，然后把它从可分配的地址空间中刨除。在操作系统自动为申请内存操作分配页框时，就不会把这些未使用的内存地址分配出去。这个刨除unused地址区域的函数到最后调用的是free_bootmem_node()，让我搞不懂的地方来了，这个函数是将bitmap中相应的页框置0，表示可分配的页。明明这个地址对应的页框未使用，后面又将bitmap中对应的位设置为可分配，是什么原因呢？这些bitmap中设置为0的区域将来应该是要交给buddy system管理的可供分配的内存啊？是我理解有问题么？

A：我还没有这么深入地分析linux内核。我看到的一本对较新的Linux内核分析比较好的书： Linux内核设计与实现(原书第3版)，深入linux内核架构， 较老的有：《Linux内核源代码情景分析》上、下，O'Reilly：深入理解LINUX内核（第3版），最早的linux内核分析：Linux内核设计的艺术：图解Linux操作系统架构设计与实现原理， Linux内核完全剖析，0.11版本内核。不过，我也没有彻底读完和读懂这些书。

陈渝老师：我们现在有一个git mirror 站点， http://coop.tuna.tsinghua.edu.cn/git 
http://huaiyusched.github.io/  是一个大四本科生上这个课，做实验等写的一些记录。

Q：copy on write只需要在虚拟内存管理vmm.c pgfault里面加一段就可以了吗？    

A：这是在有进程fork后可以具有的一个性能优化手段。即lab5可以做。基本功能设计process fork, vmm, irq 等方面的综合实现。不是“设计”，而是“涉及'。  

Q：思路是process 里面要判断是读还是写，如果是写，就调用中断再copy一次，  

A：不仅仅这样。要看这个地址是否是合法的mm, 注意vma中的描述，在vma中合法，且能读写。但在页表中，设置的是只读。这样的页符合copy on write的特征，所以在中断处理时，查看有这样的地址，就需要复制一份，实现了copy on write的目标。 

Q：有相关的答案或者测试方法吗  

A：其实有一个参考实现，但没有公布。建议你在github or bitbucket上放置你的实现源码，这样我可以帮你看看你的实现。鼓励尝试挑战！

Q：老师，清华对推免硕士会比较看重什么呢？  

A：对于计算机系而言，更看重博士。硕士量也很少，因为他们一般还有出国的考虑。总体而言，对于硕士，看排名，获奖，工程能力。

Q：谁知道老师提供的做好的虚拟硬盘的mooc-os密码呀？

A：一个空格

Q：老师，请问一下rt mutex的应用场景

A：rt_mutex应该是用在rt-linux中的吧？
http://www.mjmwired.net/kernel/Documentation/rt-mutex-design.txt 

Q：陈老师，关于生产者消费者问题，银行家问题会在后面课程里面有讲授吗？  

A：银行家算法应该在死锁章节讲，会讲。  
http://wiki.0xffffff.org 是一个西邮的大四本科生写的，做得很不错。大家有空可看看。生产者消费者问题，哲学家问题，读者写者问题都会讲。http://cdmd.cnki.com.cn/Article/CDMD-10532-2006131961.htm   有一些相关的内容。可以参考一些。西邮的陈莉君老师组织了一个很好的，有较长历史的linux兴趣小组，他们Linux讲的比较深入，吸引了一批同学加入小组，有不少都做得很好，毕业后去了大公司做linux开发。他的几本书不错。

Q：陈老师您觉得在多核系统上，做一些内存管理，和进程管理的project意义大吗。  

A：os for 多核从2006年以来有了很多研究。在调度方面，我觉得创新性的空间不大了。在内存管理，IO管理方面,vmm方面还是有很多需要研究的。不过挑战较大。在调度方面，我觉得创新性的空间不大了。在内存管理，IO管理方面,vmm方面还是有很多需要研究的。不过挑战较大。我记得前几年（2010?）有一篇hpca的best paper，讲异构(指令相同，但性能不同的异构cpu core)调度。

Q：OS理论对功耗方面、电源管理、温控管理意义大吗？  

A：功耗方面，电源管理是移动端os的一个难点和要害。不过只靠os比较难做。

风行烈:我是一直做调度的，不过不是在OS层，而是异构环境，我觉得空间还是蛮大的

陈渝:我记得前几年（2010?）有一篇hpca的best paper，讲异构(指令相同，但性能不同的异构cpu core)调度。

风行烈:片上的异构重庆大学吴凯劫做的还是挺好的

陈渝：你说的片上异构是体系结构方面的研究吗？还是for os的研究。

风行烈：现在做能耗都是在数据中心层的调度

陈渝：对于分布式系统，有很广阔的研究点。我知道的有berkeley的AMPLab, MIT的PDOS组都做得很好。

风行烈：是的。单从OS角度，对于我来说太难了

陈渝：我对分布式系统涉及很少，不过这个方向很好，但问题和实验环境在互联网公司。建议多与公司合作。其实还有一个方向目前国内外都很热，就是系统安全。我们最近的一个研究就是找os的bug，挺有意思，也挺有挑战的。

风行烈：Linux的吗？

陈渝：做这方面的有UCSD的周圆圆，芝加哥大学的卢山，华盛顿大学的王曦，berkeley的dang song, UIUC的谢涛，太多华人在这方面的了。

风行烈：安全还是bug方面？

陈渝：我比较关注的是分析linux kernel 或其他os kernel的bug/漏洞。严重的bug就成了安全问题了。bug好一些。漏洞还有有POC的攻击代码。更麻烦一些。比如前段时间爆出了底层漏洞，很多网络支付公司都紧急升级，做系统安全方面，我接触过的国内有名的有keen team公司。
http://soft.cs.tsinghua.edu.cn/os2atc2014/ppt/keynote/keynote-niesen.pdf 他们的一个年轻专家前些日子做过一个报告。大家有兴趣可以看看。

风行烈：这会涉及指令系统吗？

陈渝：我说的都还是软件方面的系统bug/安全。

风行烈：谢涛在搞大数据了？

陈渝：我刚看到一篇asplos14的paper,谢涛教授和MSRA的研究员在找windows的性能异常方面的问题。国内外也有做硬件安全（攻防）方面的研究。硬件有张焕国。

Q：编译器方面的问题  

A：我记得群里有人问编译器方面，我们最近找bug，用到程序分析技术，主要的一个工具就是LLVM，如果大家有兴趣，也可以看看。我觉得它比gcc在很多方面都好一些。唯一不足的是还不能完全编译Linux kernel，需要打patch.

陈渝老师：我对大家的一个建议是：其实OS学习不是非要很聪明的人学才学的好，主要是坚持，深入。太聪明了也许学不好，或不愿意学OS

https://class.coursera.org/compilers/lecture/preview 我觉得这个课应该不错吧。一个小的麻雀，从零开始，即使是读懂，理解，而不是自己做，收获也会很大。

旅叶：Compilers课程
https://class.stanford.edu/courses/Engineering/Compilers/Fall2014/about 

Q：我们这门课的lab经常会给人摸不着头脑的感觉

A：对于lab，我们也一直在摸索，感觉很难让学生很容易理解和完成实验。也许我应该讲讲：如果是我是一个学生来做，应该如何一步一步低完成这些实验。我下次尝试一下。

Q：课堂讲的太少，还是看书多，现在觉得书也讲的不深，想看论文  

A：想彻底懂，需要动手实践，看是远远不够的。每周与大家聊的时间还是很短。争取从下周开始，每周三晚上有半个小时与大家再交流一下。
风行烈：我个人学Linux的切入是从写驱动开始，如果开始就去研究进程和内存管理会很枯燥
陈渝：每个人特点和基础不同，没有绝对的学习方式。希望大家能够探索一条适合自己的路。比如，对于学习os而言，我比较喜欢分析一个小的但比较全面的可实际工作的os。理解比较清楚后，再看linux的相关东西，并写写kernel module。

Q：老师，minix怎样：  

A：minix不错呀。不过现在体检也很大了。我个人觉得微内核架构与单体内核架构差别还是较大。除非你照着minix作者的教材“操作系统设计与实现--基于minix”学，那我建议你看minix.。design and implementation  这本书就是围绕minix。

Q：您提到的小的os，就是我们实验用的那个吗  

A：小os有很多，xv6, ucore,....，还有国内的linux-0.11等，harvard 的OS161, U of Maryland：geek os, berkeley: nachos，stanford: pintos，我比较欣赏xv6，简单！

## 2014/12/17

Q：陈老师，您觉得哪本书对实现一个操作系统比较合适？  

A：推荐计算机专业的同学我推荐看 于渊的《Orange’s:一个操作系统的实现》，及其早期写的《自己动手写操作系统》；《30天自制操作系统》，不过感觉有点不深入；《Linux内核完全剖析：基于0.12内核》，赵炯，很细，不过对于为什么这么做解释的较少。《Linux内核源代码情景分析》毛德操，说明了怎么做，也说明了为什么这么做，挺不错的书，不过难度和深度较大。推荐非计算机专业的同学看《嵌入式实时操作系统μC/OS-II》邵贝贝老师翻译的，简单易懂。

Q：OS领域那些会议或者期刊的论文值得关注？  

A：OS领域以会议为主，期刊较弱。顶级会议包括SOSP、OSDI、ASPLOS；一流会议包括USENIX、EUROSYS、VEE、HOTOS、FAST；不错的会议APSYS；领域沾边的会议NSDI、HPCA、MOBISYS、SIGCOM…。 期刊有IEEE Trans of Computer，ACM Trans of …，还有系统安全领域、软件工程领域，都与OS沾边。

Q：单cpu情况下，操作系统在调度进程时候，如何调度操作系统本身？  

A：操作系统本身是不需要调度的，只是被动的提供服务，用户进程/线程和内核线程需要OS调度。

Q：软件工程学到什么程度会和操作系统打交道？  

A：软件工程不是学来的，而是实践中体会到的，所以两者不存在依赖关系。

Q：https://www.cs.purdue.edu/homes/cs252/ 课程怎么样？  

A：这个课程还不错，但不是分析理解OS的实现，而是理解如何正确使用OS。

Q：老师，介绍点linux os paper。  

A：直接到网址上看每年的SOSP,OSDI,USENIX,EUROSYS的paper、ppt、video，网上都是免费的，ACM这点做的不错。
还有：https://www.usenix.org/conference/osdi14/technical-sessions 
http://sigops.org/sosp/sosp13/program.html
https://www.usenix.org/conference/atc13/technical-sessions

Q：对于刚接触操作系统，只有操作系统原理的一些基础，怎样编写代码实践，比如linux内核，光看书也不知道如何实践？  

A：看邵贝贝老师的《嵌入式实时操作系统μC/OS-II》吧，然后把代码理解，编译，运行。

Q：如果不想简单的抄代码，而是能够修改，应该这么做？  

A：先重复吧，如果重复完了，并且理解了，再尝试修改，一口吃不成胖子，一步一步来。
