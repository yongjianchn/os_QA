1  在configure中enable swap之后，发现check_mm_swap过不去。
然后把代码切回到最原始的版本，依旧如此。也就是说目前提供的ucore_plus代码是有问题的。
但是我想当初把swap合并进来的人应该能成功测试通过的。。所以我想请问能否联系到原作者。

如果我没有记错，这一部分工作应该是王乃峥做的。你可以尝试给他（wnzheng@gmail.com）发邮件问一下。

OS子系统功能验证与代码演化：相关材料
以下是一些与OS子系统功能验证与代码演化相关的现有工作和工具。

1. 系统建模、测试与验证
1.1 工具：
    Coq（http://coq.inria.fr）：交互定理证明（ITP）工具
    Z3（http://z3.codeplex.com）：SMT可满足性求解工具
    Alloy（http://alloy.mit.edu/alloy）：基于Relational Calculus的模型检测工具
    BLAST（http://mtc.epfl.ch/software-tools/blast/index-epfl.php）：基于CEGAR框架的通用模型检测工具
    NuSMV（http://nusmv.fbk.eu）：符号模型检测工具
1.2 参考工作：
    SLAM/SDV（http://research.microsoft.com/en-us/projects/slam）：面向Windows驱动的模型检测工具
    Formal Verification of the Heap Manager of an Operating System using Separation Logic：基于Coq和Separation Logic的堆管理验证（http://dl.acm.org/citation.cfm?id=2105412）
    seL4：基于Isabella/HOL和Haskell的L4微内核验证（http://ssrg.nicta.com.au/projects/seL4）
    Verification of mutual exclusion algorithms with SMV system：基于Symbolic Model Checking的同步互斥算法正确性验证（http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=1248127）
    Commuter：基于抽象建模的系统调用并行性分析（http://pdos.csail.mit.edu/commuter/）

2. Linux子系统接口演化
2.1 相关资料：
    Linux开发流程：http://www.linuxfoundation.org/content/how-participate-linux-community（第2部分）
    Documenting and automating collateral evolutions in linux device drivers（http://dl.acm.org/citation.cfm?id=1352592.1352618）
2.2 工具：
    Coccinelle（http://coccinelle.lip6.fr）：C代码重构工具（语义补丁）


【关于arm的配置】
之前没用过arm，在配置到android list avd显示
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

除了存储进程的trapframe，内核堆栈还有什么用呢？ 
我看进程拷贝的时候只拷贝了trapframe，并没有拷贝整个内核堆栈

答：
堆栈是两个东西，heap 和 stack. 
Stack是用来维护execution flow. stack存储了一些临时数据，包括 local data and return address. 
Kernel stack 就是在kernel mode 下的 stack, 也一样包括了 local data and return 
address(包括切换回user mode的). Kernel stack也是用来维护execution flow的，kernel mode 下的execution flow 包括 thread 和 interrupt handler. 
至于是不是每一个thread都需要一个stack, interrupt handler 使用谁的stack, 这个在实现时候都可以按需要考虑。 
肤浅的理解，欢迎纠正 :)

哦，我对你的提问的理解是：程序的运行总是需要 stack 的，比如函数 call、局部变量等等，在内核里面也一样，从用户程序进入内核以后，执行内核代码所使用的 stack 就是你在进程创建的时候分配的 stack。其次，在由用户程序进入内核的时候，这个 stack 还有一个作用就是保存当前所有的 寄存器 的值，参见 trapasm.S。当然，从内核回到用户程序，也需要类似的操作。所以，这个 stack 很有用。
至于你说为什么不拷贝内核堆栈，是指 fork 的时候吧，不拷贝，因为确实是没有用。这个 stack 对于用户程序而言，就是一个临时的东西。 

可以从下一个进程是否会重用kernel stack来理解fork时拷贝。只要下一个进程不会占用的，就可以不复制。这样，就只有寄存器是必须复制的了，其他的都可以不复制。
--向勇
【XV6 虚存管理】
XV6 虚存管理
http://www.google.com/url?sa=D&q=http://docs.google.com/Doc%3Fid%3Ddhn9b47f_0gj39xzhd%26invite%3D&usg=AFQjCNFW_PmrUR8_2tDKIowPGEDMKdH7-A
【xv6内存管理】
各位 
xv6在原版的基础上，经过老师和学长们不断的改进，添加了页式内存管理的支持。由于开发消息队列的需要，我们组阅读了xv6内存管理的代码，并对它进行了测试¬。发现了以下问题： 
1. 代码混乱 
首先，原有代码被注释掉，同时原来的注释还留着，这样容易引起混淆。 
现有系统与内存管理有关的接口有： 
// buddy.h 
struct Page * __alloc_pages(int nr); 
void __free_pages(struct Page *page, int nr); 
struct Page *alloc_pages_bulk(int order); 
void free_pages_bulk(struct Page *page, int order); 
// defs.h 
char *kalloc(int sz); 
void kfree(char *addr, int sz); 
int growproc(int n); 
// pmap.h 
char *alloc_page(); 
int map_segment(pde_t *pgdir, paddr_t pa, vaddr_t la, uint size, uint perm); 
其中pmap.h中的alloc_page应该放在buddy中。 
由于历史原因，页式管理的一部分初始化在kalloc中。
2. 内存分配浪费
现有的分配下，如果需要10byte的空间，会在buddy中一个页。对__alloc_pages来说，如果要9个页，则实际会分配16个页。这样的空间浪费¬实在太大。出了代码编写本身的问题外，接口的设计也是问题。
3. 有严重bug
a)安全性 
初始化页表将所有页都设了PG_USER。这有严重安全问题。
譬如在用户态读取0x80008000，即可获得e820返回的内存表个数(6)。
b)执行usertests会产生Triple Fault 这个问题还在查找中... 
针对以上问题，我们拟重新设计内存相关接口，但保持原有实现不变。新接口具有如下形式： 
// buddy.h 
struct Page * alloc_pages(int order); 
void free_pages(struct Page *page); 
// pmap.h 
int map_segment(pde_t *pgdir, paddr_t pa, vaddr_t la, uint size, uint perm); 
// kalloc.h 
void kinit(); 
char *kalloc(int sz); 
void kfree(char *addr); 
这个设计的关键是明确各个组件的功能。buddy专门负责2的幂次的物理页的管理，kalloc,kfree专门面向内核提供形如malloc,free的服务¬。而需要分配页进行映射时，则采用alloc_pages, map_segment的组合。这里的一个重要变化是取消了任意连续物理页的分配（因为有了页式管理的机制，对连续物理页的需求大大减少）。如果未来有需要，可¬以考虑在buddy中加入这一功能。 
这个，改动中，除了改变接口，一个主要功能就是重写kalloc，和kfree。这拟由我们组完成。 
另外，我们准备改正上面找到的bug 
【页式管理机制】
看到考试上的一些题目问到某个页式管理机制最多一共有多少个页表。 
那如果考虑自映射机制的话，一级页表的其中一个表项指向的是一级页表本身，是否应该在统计的时候减1？
答：
你的理解是正确的。自映射的页表占了两个位置。 
--向勇 
【线性地址、物理地址、虚拟地址】
线性地址、物理地址、虚拟地址具体的联系和区别是什么？
答：
这个，看IA32手册吧。
简单来说
Virtual Address ------ Segment ------>
Linear Address ------ Paging --------->
Physical Address
最后Physical Address 即是访问物理内存的地址。
【管程的实现】
各位老师同学： 
课余研究了一下Hansen和Hoare两种管程的实现，对证明有了一点简单的了解，希望跟大家讨论 
附件中是hoare证明monitor=semaphore的论文证明分为两个部分：
第一部分：monitor>=semaphore，亦即管程可以实现二值信号量（等价于多值）。
第二部分：semaphore>=monitor即证明两者等价。 
重点在于第一部分
第2页给出了管程的实现，可以发现代码中的acquire和release两个函数和二值信号量的p/v能完全一一对应在代码下面的注记2中提及：acquire中使用if而非while，是由release的立即唤醒所保证，且过程中不被打断。
在使用if的情况下，代码和二值信号量的p/v才完全对应，从而才能证明monitor>=semaphore
Hansen-style没有立即唤醒，因此使用while，正是这一点导致其无法与二值信号量直接对应，因此证明有一定难度。
以上是本人的一点拙见，若有谬误还望老师指正，欢迎大家探讨！ 
程芃祺 

答：
程芃祺，你好： 
我理解你对论文的解释是对的。另外，其实monitor的机制在java/pascal/mesa等语言中有体现，在Linux的pthread有线程的实现（¬用的是Hansen-style，即需要while）最终要用到linux 
kernel的semaphore机制来实现，这在一定程度上也说明了可以用sema来实现条件变量。 
但我观察到在Linux和其他通用os中好像都没有monitor的实现，甚至没有condition variable的实现。我猜想主要是由于如下几点： 
1 用semaphore可以实现monitor的功能 
2  monitor主要体现了一种更加简单一些的编程方式，在语言级支持monitor可能更好。Linux等采用的是C语言，在语言级别无法扩展monitor，¬这样还不如用semaphore的函数方式实现类似monitor的功能更加比较容易做。
3 如果采用hoare style方式，虽然证明容易了，但实现复杂，特别是在在scheduler方面，需要确保signal的线程和被唤醒的wait线程之间不会有其他线程或因素¬存在导致wait的条件又为真了。 
4 如果采用Hansen-style，则用lock + sema方式实现也很直观，在内核里面，好像也不太需要monitor这一层的抽象了。 
【Darwin的资料】
下面两个链接是OS专题训练课上提到的Darwin的相关信息。有兴趣的同学，可深入阅读。 
http://www.google.com/url?sa=D&q=http://en.wikipedia.org/wiki/Darwin_%2528operating_system%2529&usg=AFQjCNHyddi-y12_1gQsKQNiqg2bx4j5hg
http://www.google.com/url?sa=D&q=http://sourceforge.net/projects/darwinsource/&usg=AFQjCNH2HktgzcJPBb4gYs0ElseZJeAMHg
--xyong 
【进程优先级】
是否一般认为fork后父子进程的先后执行顺序是不确定的，vfork出来的子进程必定先于父进程执行？ 
答：
fork后父子进程的先后执行顺序是不确定的。 
vfork后父进程会等子进程结束或调用exec()。 
详细描述参见：
http://www.google.com/url?sa=D&q=http://en.wikipedia.org/wiki/Fork_%2528operating_system%2529&usg=AFQjCNGPk2EeS_rM888sdYOXTiO081-DBg
--向勇 
【LRU算法】
在某些地方看到说LRU算法不会出现belady现象，但貌似记得老师上课说过可以找到一个队列也使LRU出现belady现象。或许是我自己记错了。 
what about clock or second chance? 
Thanks~ 
答：
你记错了。
【面包店算法】
> 向勇老师您好！我想问您一个有关面包店算法的问题。主要是关于那个choosing 
> 数组的必要性。从程序上看，choosing数组的主要作用是当进程Pj在“取号”， 
> 也就是设置number[j]的时候，不允许其它进程访问number[j]。而对于choosing 
> 数组的必要性，从代码number[j] = max(number[0], 
> number[1].......number[n-1])+1上来看感觉不太明显，但是如果将取号部分的 
> 程序改为 
> for (i = 0; i < N; i++)
> if (number[ i ] > number[ j ])
> number[ j ] = number[ i ];
> number[ j ]++;
> 那么用choosing加锁的必要性主要就体现在当for循环没有终止时，当前
> number[ j ]的值是不正确的。因此必须要做完number[ j ]++后才可以访问 
> number[ j ]。不知道我的这个理解是否正确。
> 另外，如果“取号”部分的代码改为
> int temp = number[0];
> for (i = 0; i < N; i++)
> if (number[ i ] > temp )
> temp = number[ i ];
> number[ j ] = temp + 1;
> 想请问一下这样做（也就是number[ j ]的唯一一次赋值操作得到的正确的“号 
> 码”值）的话，是否还需要利用choosing数组加锁。
答：
关于choosing数组的作用是避免两个进程同时依据当前的number[]来计算自己的 
number[j]。如果不用choosing数组，会出现下面情况，这时会出现两个进程同时 
进入临界区。
假定使用你的第二种max函数实现进行最后的赋值，且j<i。
1）进程i和j同时依据当前的number[]计算自己的number[]（这时它们的计算结果 
是相同的），且进程j先进行number[j]的赋值，进程i后赋值number[i]。
2）在进程i赋值number[i]前，进程j已进入临界区；(如果用了choosing数组，这 
一步不会出现！)
3）这时进程j会由于while number[j]!=0 and (number[j],j)<(number[i],i)不成 
立而进入临界区。
这样就出现两个进程都在临界区的情况。
关于面包店算法的详细内容可参见下面的两个链接。
http://www.google.com/url?sa=D&q=http://en.wikipedia.org/wiki/Lamport%2527s_bakery_algorithm&usg=AFQjCNF58r9teNk_08T2AbIF76zvzbpDOQ
http://www.google.com/url?sa=D&q=http://www.cs.umd.edu/~shankar/712-F04/chapters-9-10.pdf&usg=AFQjCNHokwMCIcl45UCv9lsMQ7GkVfMI1Q
--向勇
【信号量】
请问，有些考卷上的题提到：不允许使用信号量集 
这里信号量集是不是指AND型信号量机制（相应函数是swait, ssignal）？ 
谢谢 
答：
是的。如果用信号量集，信号量的技巧就都没有了，而且信号量集的等待行为是很难预测的。 
--向勇 
【管程/信号量】
老师，假设使用管程和条件变量实现，符不符合题目要求利用信号量实现的范畴？ 
答：
从概念上来说，管程与信号量是不同的。通常在题目中会明确指出要求。 
--向勇
【BIOS问题】
把 BIOS 代码反汇编打log打了出来，好长。。有 11185 行 
发现里面有3次模式切换： 
在20-32行进入保护模式： 
0x000ffecf: mov %cr0,%eax 
0x000ffed2: or $0x1,%eax 
0x000ffed6: mov %eax,%cr0 
0x000ffed9: ljmpl $0x8,$0xffee1 
4897-4900 行又退出保护模式 
0x000fc755: mov %cr0,%eax 
0x000fc758: and $0xfffffffe,%eax 
0x000fc75c: mov %eax,%cr0 
0x000fc75f: ljmp $0xf000,$0xc764 
4961-4964 行再次进入保护模式： 
0x000ffecf: mov %cr0,%eax 
0x000ffed2: or $0x1,%eax 
0x000ffed6: mov %eax,%cr0 
0x000ffed9: ljmpl $0x8,$0xffee1 
1，	BIOS 切换来切换去干吗？ 
2，	执行 loader 的时候已经是在保护模式中了，为什么 loader 还要再设置一次进入保护模式呢？ 

答：
我的理解是 
A1： 
BIOS大部分时间工作在实模式。但实模式有一定限制（比如访问超过1MB的内存等），所以可能在某些模式需要到保护模式。 
不过完成lab1应该不需要分析所有BIOS，而是前几行。 
A2: loader假定在执行loader前是CPU处于保护模式的（一般好像就是这样的），所以它需要切换到保护模式。 
不能因为发现BIOS有三处（注意：不是三次）切换的代码，就认为BIOS执行了3次模式切换。 
【信号量输出】
请问有名信号量的标准输出是什么样的呢？ 
实验指导书上的样例不完整，而且如果出现了输出顺序不一致的情况，或者 
named semaphore test OK 
在 
NS: 4 child_2 
sys_sem_post 2 
sem_post: count 0 
之前出现，请问是不是不符合标准的，谢谢…… 
答：
但是，在test退出前会有sem_destroy 
答案由于在safestcpy时没有正确传入长度参数，应该为name_len+1,而答案为name_len。按下Ctrl+S，可以看到信号量名称为sem_tes，而不是sem_test.
所以3个孩子都是create sem，第2、3个孩子应该是find sem。 
并且最后destroy后，好像孩子还没有结束，会发送sem error。 
不知道是不是特殊情况.反正我改了后，就发生这种问题了。
【实模式到保护模式的切换过程】
有同学问到这个切换过程，有些共性，于是把回答写在Ｗiki上了。希望对其他同学也有借见作用。 
http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OS2009/Exp1%23head-f258b8862ee06c8d4f379fbc7aecd9e7fe5ff3a5&usg=AFQjCNHhLWYmAL8Gp3E8eujv6n8VTp8v0g
【关于互斥的问题】
在第九讲ppt的P42页中提到了一种用非spin lock解决互斥方案，他用一个queue记录了所有正在等待锁的进程（具体伪码如下）。可是感觉这个方案实现的时候好像有点问题： 
class Lock {
int value = 0;
WaitQueue q;
}
Lock::Acquire() {
while (test-and-set(value)) {
add this TCB to wait queue q; （注意这一句！！！）
schedule();
}
}
Lock::Release() {
value = 0;
remove one thread t from q;
wakeup(t);
}
由于queue是一个共享的变量，所以我标出的那一句应该仍然需要是一个原子操作，而将一个元素加入queue中显然是不能用一条指令完成的，所以这个时候就需要为这句话再加上一个互斥锁。也就是为了实现一个互斥锁，我们要用另外一个互斥锁，这样的话不就会无穷递归下去了吗？
不知道是不是哪儿想的有问题，希望大家指正！ 
答：
在single-cpu下，Lock::Acquire()和Lock::Release()放在Kernel下做吧 
我觉得把 
Lock::Acquire() { 
while (test-and-set(value)) { 
add this TCB to wait queue q; 
schedule(); 
} 
} 
改成 
Lock::Acquire() { 
if (test-and-set(value)) { 
add this TCB to wait queue q; 
schedule(); 
} 
} 
就可以了吧。
【无文件系统的情况下能起一个进程吗？】
答：
可以。
所谓文件系统，无非是把一个永久的外部存储阵列抽象成文件树的形式。这个概念应该是和进程独立的。
即，操作系统要创建进程，有多种途径，可以不用文件系统。 
1. 类似多线程，直接以一个函数为入口建立进程。 
2. fork直接从已有的进程建立。 
等等。。 
当然，上面都是举出特别的例子来说明这个问题。 
实际还是从文件读二进制到内存中在建立进程比较方便。 
【磁盘缓存】
向老师: 
您好! 
有个问题想请教您:关于磁盘的缓存的问题,我们平常说的磁盘(硬盘)缓存是指在内存中的一部分,还是指硬盘自己带的缓存(比如硬盘控制器上的芯片) ,还是在内存中有缓存,在硬盘上也有缓存,它们的目的都是为了提高缓存,只是方法不一样,在内存中的缓存是为了保证批量数据的提前预读和延后写入,而硬盘上的缓存是为了将由磁信号转变为数字信号的数据暂时放在缓存中(这样一次可多转换一些磁信号) ? 
如果在内存和硬盘都有缓存的话进程将数据写到磁盘上是不是要经历这么几个过程: 
进程自己的内存空间 -> 系统缓冲区(I/o 缓冲区 在内存中) -> 硬盘缓存 ->磁盘 ? 
还有个问题,就是关于I/o缓冲区的,这个缓冲区是不是操作系统内核管理的?对用户来说是透明的? 如果要将数据输出是不是都要经历这么几个过程? 
将在进程自己内存空间的数据复制到 I/o缓冲区 然后再输出 (进程不能直接操作I/O缓冲区,也就是省去第一步直接将要输出的数据放到I/O缓冲区中) 
如果从外设读数据,是不是也要先读到系统I/O缓冲区,然后进程复制到自己的内存空间中使用,进程不能直接从 系统I/O缓冲区取数据使用? 
我对这里面的过程不清楚,麻烦向老师帮助我解答,谢谢!
祝您:工作顺利,身体健康!
敬礼!

答：
1）硬盘上的缓存和内存中的缓存作用是类似的，都是读写缓存和频繁读写数据的暂存。不同之处在于，内存的缓存是由操作系统控制的，而操作系统只能启用或禁用硬盘上的缓存功能，不能对缓存算法进行控制。我从网上找到一段文字（http://www.google.com/url?sa=D&q=http://article.pchome.net/&usg=AFQjCNF3jVedGxN1-exMfKjMl_s9Nm3UeAcontent-447812-2.html），比较准确地说明了硬盘缓存的作用；而硬盘上缓存的大小对性能的影响主要体现在延迟写入上（http://www.google.com/url?sa=D&q=http://article.pchome.net/content-447812-3.html%EF%BC%89&usg=AFQjCNFDoICqV3Cv77jN6cveitMGoBqU9A，这也引入了数据安全问题，需要小心使用。
2）关于写数据到硬盘的过程，你说的基本正确。一个小的修正是，硬盘缓存对写入数据的缓存是可以由操作系统禁用的。
3）内存中I/O缓存是由操作系统管理的，通常对用户透明；但也有例外，数据库系统通常是不用操作系统的缓存机制的，它会自己来管理缓存。
4）关于用户进程是否能直接从I/O缓存读取数据来使用，我不确定。可能的线索是，通常用户进程不能直接访问系统的I/O缓存；但如果使用文件映射，我就说不太清楚I/O缓存的作用了。也许可以设计一个实验测试一下。 
--向勇 
【磁盘工作原理】
在实际的操作系统中，通常能顺序被唤醒，但相关文档描述并不保证被阻塞进程按顺序被唤醒。好象Windows的API文档就专门说到这一点。
我认为PC机上是使用通道技术的。SCSI硬盘接口卡应该就是用的通道方式。也许你 
可以查一下IDE硬盘接口的工作原理，也许它也是的。如果可能，你可以把查到的 
结果告诉我一下。谢谢！
--向勇
答：
对于第一个问题，我补充一下，其实在大部分OS的实现中关于唤醒的具体实现有如下几种：
1 由于阻塞进程是按照被阻塞的先后顺序挂在一个链表上，这样如果只唤醒一个进程，可以很方便地安装FIFO的方式实现唤醒 
2 对于实时进程而言，由于进程的优先级很重要，高优先级需要先执行，所以会存在某个数据结构来按优先级的方式顺序存放被阻塞进程，这样可以基于优先级来唤醒进程 
3 发生某个事件后，也可能需要唤醒所有阻塞的进程，但容易出现“惊群”现象 
但进程和等待队列的具体定义会有很多属性，比如某些进程在等待时有timeout、等待队列有EXCLUSIVE、优先级级考虑等属性，导致实际情况下无法保证任意情况下按FIFO顺序唤醒。 
具体实现可以看看Linux的sched.c的__wake_up_common、sleep_on_common等函数 
对于第二个问题， 
SCSI的通道是一个属于SCSI领域的概念，实际上是用来描述SCSI设备的总线（BUS）。对于PC而言，它也有自己的总线，比如PCI，SCSI卡也需要¬插在PCI或PCIe总线上，从这点上看，连接在PCI上的设备还是需要通过中断和DMA来与CPU、MEM交互的。 
【FAT32资料】
XV6下的FAT32支持进展：http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OsTrain2009/VFS&usg=AFQjCNH3JRh4c2cSc_xGYB__6CVYnRQ6pw
--xyong 
【写者优先】
> 向老师： 
> 您好！
> 关于写者优先部分，课间中给出的伪代码为什么要在P(rsem)前P(z) 
> 阿，我在wiki上找到的伪码不需要这步操作。我觉得P(z)和P(rsem)有重复的感 
> 觉，没有必要把……请您解释下～ 
> 谢谢！
答：
对于写者优先的读者-写者问题的解法的解释：
对写者优先的实现在William Stallings的第五版教材的表5.5中有一个比较清楚的描述。即把所有可能的情况分成4种情况：1）只有读者；2）只有写者；3）同时有读者和写者，且当前正在读；4）同时有读者和写者，且当前正在写。有了这个分类，再来理解下面的解释会容易一些。
如果没有信号量z，在下面情况时会有大量的读者等待在信号量rsem上。这种情况是，当前正在进行写操作，随后有大量读者到达。
随后写者们完成了最后一个写操作，可以进行读操作了。于是所有等待在信号量rsem上的读者开始逐一获得信号量rsem，然后申请信号量x，检查自己是否是第一个读者，确认不是第一个后开始读操作。如果这时的读者的数目很多，这个过程需要一定的时间。
设想这时又有一个写者到达，它就没有办法赶在正在逐一申请信号量x的读者前面进行写操作了。
如果这时有信号量z的作用，这个写者就可以在第一时间挡住正在获得信号量rsem的读者，最大限度地实现写者优先。
