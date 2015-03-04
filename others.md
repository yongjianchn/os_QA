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

cueroqu: 操作系统课程的实验部分有时候需要汇编语言的知识。然后有些语法一时很难查到具体什么意思。不知到有没专门此类介绍汇编语言语法的网站呢？或者说还是先略过这些实验然后先对操¬作系统有一个大致的了解（比如看后续的课件）？ 
答： 
chyyuu: 
OS实验用的是GNU AS 汇编器和GNU CC内联汇编技术，相关文档如下： 
X86的指令集详细文档，可查到所有的X86汇编：ia32-instrset.pdf 
http://www.google.com/url?sa=D&q=http://www.bytelabs.org/pub/ct/arch/intel/ia32-instrset.pdf&usg=AFQjCNEf14eSMFxGAQT_V2hwsKuzf11sig
是所有的机器指令说明文档。此目录下的另外两个文档对理解x86硬件工作模式和硬件系统系编程很有帮助。 
中文介绍文档：Linux 中 x86 的内联汇编 
http://www.google.com/url?sa=D&q=http://www.ibm.com/developerworks/cn/linux/sdk/assemble/inline/index.html&usg=AFQjCNGsJhhoKP0uHuZL7ZlJRyefhQwRtw
英文GNU汇编专业电子书，掌握此书，将是汇编绝对高手
《Professional Assembly Language》英文版 ，作者 Richard Blum 
http://www.google.com/url?sa=D&q=http://forum.eviloctal.com/thread-31189-1-4.html&usg=AFQjCNGCnNrpdZtqNFxDEyEFjgmyHFv8mA
另外可以通过 google等搜索 "GNU" "Assembly" 或 “GNU” “汇编”等查找相关信息 
【扩展实验：用户态与系统态】
陈老师， 
今天根据课上讲的我试做了扩展实验1，现在碰到一个问题一晚上无法解决，还请帮忙指教： 
我的程序先从系统态跳到了用户态，然后在用户态下执行了int 0x80，结果出现错误： 
qemu: fatal: invalid tss type 
qemu直接关闭。 
然后我试着查阅tss相关资料，了解到tss是task上下文切换用的，在用户态到系统态发生特权等级变化时要用到。而我们的proj4默认没有设置tss的操¬作，我便试着在gdt中加入tss项：
SEG_ASM(0x89, 0x00000, 0x20ab) 
然后ltr进去0x28 
这样操作后，遇到第一个call或者jmp指令就会导致qemu重启。然后试了很久毫无进展，还望老师指导一下。 
具体错误log: 
(qemu) qemu: fatal: invalid tss type 
EAX=00100028 EBX=00010094 ECX=00000528 EDX=00000010 
ESI=00010094 EDI=00000000 EBP=0013fffc ESP=0013ffd4 
EIP=0010000a EFL=00003202 [-------] CPL=3 II=0 A20=1 SMM=0 HLT=0 
ES =0000 00000000 00000000 00000000 
CS =001b 00000000 ffffffff 00cffa00 DPL=3 CS32 [-R-] 
SS =0023 00000000 ffffffff 00cff200 DPL=3 DS [-W-] 
DS =0000 00000000 00000000 00000000 
FS =0000 00000000 00000000 00000000 
GS =0000 00000000 00000000 00000000 
LDT=0000 00000000 0000ffff 00008200 DPL=0 LDT 
TR =0000 00000000 0000ffff 00008b00 DPL=0 TSS32-busy 
GDT= 00007c54 0000002f 
IDT= 0010f040 000007ff 
CR0=00000011 CR2=00000000 CR3=00000000 CR4=00000000 
DR0=00000000 DR1=00000000 DR2=00000000 DR3=00000000 
DR6=ffff0ff0 DR7=00000400 
CCS=00000028 CCD=0013ffd4 CCO=SUBL 
EFER=0000000000000000 
FCW=037f FSW=0000 [ST=0] FTW=00 MXCSR=00001f80 
FPR0=0000000000000000 0000 FPR1=0000000000000000 0000 
FPR2=0000000000000000 0000 FPR3=0000000000000000 0000 
FPR4=0000000000000000 0000 FPR5=0000000000000000 0000 
FPR6=0000000000000000 0000 FPR7=0000000000000000 0000 
XMM00=00000000000000000000000000000000 
XMM01=00000000000000000000000000000000 
XMM02=00000000000000000000000000000000 
XMM03=00000000000000000000000000000000 
XMM04=00000000000000000000000000000000 
XMM05=00000000000000000000000000000000 
XMM06=00000000000000000000000000000000 
XMM07=00000000000000000000000000000000 
Aborted 
----Shengwei Ren 
答：
challenge 代码给的可能不是很全。 
tss 应该在 gdt 里面，实际上 boot 到 kernel 以后还会再设置一次 gdt。里面会设置 tss。不过 proj4 还没有给这段程序。 然后 tss 里面得设置 stack 位置（查看 intel 手册 / google 了解一下这个 stack 的作用） 
SEG_ASM(0x89, 0x00000, 0x20ab) 这个似乎不对。而且这个不是 challenge 的重点。不过你可以先自己尝试一下设计一个。 
我问问陈老师，看看是不是稍后给出缺的那段程序。 
【内存管理算法】
向老师：
> 您好！
> 我们是做内存管理小组的。
> 目前我们把proj7.1的swap相关文件嵌入了ucore-mp64文件，同时仿造已有算法的框架完成了另外三个算法的实现。 
> 但是现在我们对于tick_event操作的调用地方没有概念，在咨询助教后放在了trap里的时钟中断调用里，但是这样会产生一个进程，在qemu运行最后无¬法消除。 
> 另外，因为已有的working_set和自己实现的lru/page_fault_frequency算法需要tick_event操作，不断地更新每个节点¬对应的调用时间，所以无法进行测试。所以我们想问一下向老师有什么建议？ 
> 计81 元升高
> 2011-12-17
答：
tick_event是应该由时钟中断触发，可以借见的做法是Windows中的延迟过程调用。大致的的意思是，中断中触发或设置一个tick_event任务¬，在延迟过程调用中执行，它的执行是优先于所有进程执行的。目前ucore还没有对它的支持。一种可能做法是，把tick_event直接放在中断服务例程中执¬行。 
关于你说的“这样会产生一个进程，在qemu运行最后无法消除”，没有太明白。系统中会有一直存在的进程，如IDLE进程就是一直存在的。 
关于测试的事，可以通过输出日志，并在事后进行统计来完成。如果日志太多，可以在内核中维护一些调试数据结构，只输出统计结果。当可以跟踪算法状态后，还需要写¬一些进行特定操作的应用程序，从而测试算法的特征。 
如果仍有问题，请与我约时间当面交流。
【kernel启动问题】
> 向老师， 
> 您好，我今天开始尝试扩展实验，目前做到把GRUB安装在U盘上。我自己查了一 
> 些相关资料，但是使用kernel启动不行，而使用ucore.img直接启动，以及添加 
> multiboot header到ucore.img，都停在[Linux-zImage, setup=0x800, 
> size=0xbe00]（这个img是否不是zImage？）。不知道主要还需要对其做什么修 
> 改，有什么资料可以参考，还请您告诉我，谢谢！
答：
ucore应该是只能直接启动，它已包含了GRUB的部分功能。在上学期也有同学做过尝试，相关结果参见下面链接。
http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OsTrain2009/FlashDisk&usg=AFQjCNHOz8aVMNbSiEpVKOW5jHpXgYhx7g
这些尝试对应的代码可从下面链接找到。
git://cherry.cs.tsinghua.edu.cn/xv6-usb-2010
希望你的尝试能有新进展。
--向勇
【进程同步算法】
> 老师你好，我操统实验二选择了进程同步，这个在操统课lab5里是有相应的实现 
> 的，
> 但是wiki上的版本里边没有信号量的相应实现。
> 向老师求救。
> 谢谢！

答：
是的。在目前的版本中有的进程同步机制是管道（pipe.c）和消息(sysmsg.c)，没有信号量。你可以对这两种机制进行测试。
另外，自旋锁（spinlock.c）也与同步机制密切相关，但我没有想明白如何能用用 
户态测试对它进行一些测试。你也可以考虑是否有可能对它进行测试。
--向勇

fork后父子进程的先后执行顺序是不确定的。 
vfork后父进程会等子进程结束或调用exec()。 
详细描述参见： 
http://www.google.com/url?sa=D&q=http://en.wikipedia.org/wiki/Fork_%2528operating_system%2529&usg=AFQjCNGPk2EeS_rM888sdYOXTiO081-DBg
--向勇 
【USB bootloader资料】
今天我又在网上找了一些关于USB bootloader的信息，见下面的列表。
http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OsTrain2009/FlashDisk%23head-d2da5a334828683b20f00739aa4b73cf00895240&usg=AFQjCNHjYCaoefCuU1w0C6E_xMj00VN5rw
--向勇
【显卡驱动的已有工作】
> 向老师： 
> 您好，我报名了课程设计中的U14，不知道上次答疑时提到的之前实现的可以显示jpeg的简单实现在哪里？在2011年的project里面找不到。 
> 另外本次lab1作业中，challenge部分对于报名课程设计的是不是必做呢？另外challenge部分提到说在1周内在另外开的窗口提交，但是现在网络¬学堂还没有看到，不清楚是不是今天交。另外challenge部分实验内容不太清楚，完成switch to user和switch to kernel就可以通过make grade了，不知道提到的使用sys_call获取当前时钟ticks是否需要做，有没有规范如何写的要求。

答：
显卡驱动的工作是2010年，可参见下面链接。
http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OsTrain2010/DriverReuse&usg=AFQjCNGaj82Z1B1QwZ6L9tiu5iCZVAgR_w
关于实验中的选做部分，对于报名课程设计的同学是必做的，与基本要求一起在网络学堂中提交。如果你还没有提交，可以发给我。这一部分目前没有特别的报告撰写要求¬。 
--向勇
【syscall功能扩展】
想做第一个Challenge B，即扩展proj4,增加syscall 功能，即增加一用户态函数（可执行一特定系统调用：获得时钟计数值），当内核初始完毕后，可从内核态返回到用户态的函数，而用户态的函数又通过系统调用得到内核¬态的服务。
想问下这里如何可以返回到用户态？一般来说是否需要内存管理和进程管理，但这些在lab1中都还没有。 
------------------------------------- 
主要是如何完成 x86的ring0<->ring3之间的正确切换，需要了解x86相关细节，也可看xv6了解。不需要内存管理和进程管理。

答：
如果选作，我觉得，大体可以按照下面要求完成： 
1. lab1 里面整个都是 kernel 态的。kern/init/init.c 最后是 while(1) 循环；在此之前请插入一段函数，完成从kernel mode -> user mode -> kernel mode 的函数，可以通过 int 指令实现（syscall）。 感兴趣的同学也可以通过 callgate 实现（这个实现非常复杂，也不容易理解，但是查阅相关资料对了解 x86 特权级保护有很大帮助。） 
2. 比如插入 switch_mode 函数完成切换，能够依次调用 switch2kern, switch2user, switch2kern 完成状态切换。每次完成切换以后，能够通过在 switch_mode 函数里面，打印出当前状态的 cs/ss/ds/es (fs,gs) 寄存器状态表示当前的身份状态。 
3. HINTS：硬件在中断发生时，特权级变化和不变化时压栈和弹桟的行为是不是一样？特别是 iret 指令。（参见 intel 手册） 
谨慎函数桟的维护 
【Android内核编译】
请问有没有同学编译过android内核，遇到一些问题想请教一下。 
谢谢！

答：
U10小组的同学应该编译过 android for x86。可咨询这两位同学。 ("张超" <Eric....@gmail.com>, "EndlessRoad1991" <endlessroad1...@gmail.com>,) 
我的一个研究生刘金钊编译过 android for ARM （HTC G7），也可咨询一下。 "inno mentats"
【ucore代码问题】
【add_timer函数】
问题：kern/schedule/Sched.c中的add_timer函数，了解大概意思就是在time_list这个静态的表中去找，找到具体位置之后插¬入到链表中去，但下面这段代码就不懂了： 
while (le != &timer_list) { 
timer_t *next = le2timer(le, timer_link); 
if (timer->expires < next->expires) { 
next->expires -= timer->expires; 
break; 
} 
timer->expires -= next->expires; 
le = list_next(le); 
} 
尤其是这句： next->expires -= timer->expires 为什么改变next的睡眠时间呢？ 

 	答：
timer 是一个链表。里面每一个元素的实际 sleep 的时间实际上是从链表头到该元素所有经过的 timer_t 的 expires 的和。 
这样的好处是，每次产生时钟中断的时候，只需要检查链表头确定是否有 timer 到期了。而不是遍历整个链表。 
add_timer 把 le 插入到链表里面了。那么必然需要更新它插入的位置后面元素的 timer_t 的 expires 的值。 

陈渝老师，您好： 
最近在做作业的过程中有一些未能理解的地方，不知是我理解不当还是 manual的瑕疵，望老师不吝指正： 
1.1.6 
1. grade.sh中检验输出时用了"grep -F"（line 218），但根据grep的说明，-F之 后的PATTERN "is a set of newline-separated fixed strings"，而PATTERN实际用的是regexp，似乎没办法用来校验结果......
1.1.7.5：
1. "中断向量表"指的是实模式下使用的中断向量表还是中断描述符表？
"实验＆报告要求"中：
1. proj3_your_proj和proj6_1_your_proj不存在；若指proj3.1和proj4，应交原 目录还是make 
handin得到的打包文件？
2. tar不接受jzf（"Conflicting compression options"），选项是否应该是cjf？
盼复，并祝好。 
您的学生 
茅俊杰 
答：
grade.sh 里 grep 有 2 种方法，-E 和 -F.
lab1 的测试用的是 -E。

1.1.7.5: 
1. 那个指的是idt表，可以结合第二问理解 
实验报告要求：
1. 指的是proj3.1和proj4。请在目录中执行make handin之后，将生成的压缩包复制到lab1_result中，并且与报告一起打包上传。
2. 正确选项为cjf，也可以用jcf，jcvf...... 
【panic错误】
运行答案时，有非常小的几率出现panic错误，也就是结果不稳定。 
发现get_proc_RR函数中，num_procs_moved = (rq->proc_num)/2;这条语句在最前面。之后有一个测试输出的 
部分： 
#ifdef LOAD_BALANCE_OUTPUT 
if(num_procs_moved != 0) 
{ 
cprintf("before load_balance\n"); 
release(&(rq->rq_lock)); 
procdump(); 
acquire(&(rq->rq_lock)); 
} 
#endif 
这中间释放了锁，那就有可能在这里出现问题，比如num_procs_moved＝2，在释放锁的瞬间rq减少了3个进程，则会出现在后面转移的时候出错。不知道理解的对不对？

答：
恩，我感觉也是这里的问题。
在LOAD_BALANCE_OUTPUT的前后num_procs_moved可能不同，所以下面遍历数组时再用到num_procs_moved就有可能出现错误。 
LOAD_BALANCE_OUTPUT后面也就是#endif后面再加一句num_procs_moved = (rq->proc_num)/2;就没有错误了，但就是前面output的东西没有意义了。。。干脆LOAD_BALANCE_OUTPUT部分不要算了。。。 8核时候出现的错误也来源于这里，只要去掉LOAD_BALANCE_OUTPUT后八核也能正常运行了。
【HOME_DIR问题】
I have the problem during setting bochsrc.
What does this instruction mean? (cp 2008-spring/tools/bochsrc$HOME_DIR/.bochsrc) in this command line, what is $HOME_DIR? (means address in detail).
thanks.

答：
$HOME_DIR is an environment variable, for example: /home/username/

$HOME_DIR is an environment variable, which indicates the location of your "home". 
It should be '/root' if you're using 'root', or '/home/os' if you're using 'os' user. 
And the "home" of Linux is like "My Documents" on Windows system. :)
【kernel.asm问题】
Here is a piece of code in kernel.asm:
f0100adf <vprintfmt>:
f0100adf: 55 push %ebp
f0100ae0: 89 e5 mov %esp,%ebp
f0100ae2: 57 push %edi
f0100ae3: 56 push %esi
f0100ae4: 53 push %ebx
f0100ae5: 83 ec 3c sub $0x3c,%esp
f0100ae8: 8b 7d 08 mov 0x8(%ebp),%edi
f0100aeb: 8b 5d 10 mov 0x10(%ebp),%ebx
f0100aee: eb 03 jmp f0100af3 <vprintfmt+0x14>
f0100af0: 8b 5d e8 mov 0xffffffe8(%ebp),%ebx
f0100af3: 0f b6 03 movzbl (%ebx),%eax 
f0100af6: 43 inc %ebx
f0100af7: 3c 25 cmp $0x25,%al
gdb thought f0100af0 is the start of the function, while many calls of <vprintfmt> does not touch this address because of the jmp instruction above it.
When I step into this function(command s in gdb), gdb will tell bochs to set breakpoint on
f0100af0. The problem is it never stops...
Is it a bug of gdb? Or some problems on my gcc?
How to solve it? Thx 

答：
None of us (40ers) used gdb when we were doing this lab. Maybe you should come to 50ers, or we can explore it together.
this lab needs to trace every line of functions in .c 
how do you trace it without gdb? 
it seems that bochs only supplies asm-level debug...

Maybe they just read the source code and figured it all out directly. 
Besides value fmt and ap, it seems that other values such as *fmt, *ap and c can be calculated from the source code.
【addr格式】
All materials tell the command to be:
(qemu) b addr 
But what's the format of 'addr'? How to indicate the 0xf section?

答：
addr format is 0x127f OR 1234
Hex format or decimal format is ok
what is oxf section?

a）首先在分段情况下，应该如何计算地址，一般来说，应该是 [$段寄存器 << 4 + $偏移量] 来得到目的地址。也就是说 lab1 里面提到的地址 0xF:0xFFF0 对应的应该是 0xF0 + 0xFFF0。不过，这个地址不是 bios 的地址，实际上文档写错了，应该是0xF000:0xFFF0，那么对应的线地址就应该是0xF0000 + 0xFFF0 = 0xFFFF0所以可以使用如下命令来添加这个断点：
b 0xFFFF0不过需要说明的是，这样设置断点也是不正确的。正确的设置应该是：
b 0xFFFFFFF0
另外，bios 是开机以后第一个运行的程序，所以，应该不可能快到直接给 bios 入口加断点。不过，如果 qemu 使用 -S 参数开启的话，就可以让 qemu 开机后停止，此时已经是 bios 的入口了，可以使用 r 命令查看当前的寄存器，s 命令进行单步进入 bios，不需要再额外的插入断点。
b) 很遗憾，qemu 目前还不能够通过 0xF000:0xFFF0 来直接设置断点，所以必须先把这个断点算出来，然后用breakpoint_insert 来手动的插入。 
【调度器报错】
When I ran the test file for scheduler, it printed that: 
[ 80.296404] BUG: unable to handle kernel NULL pointer dereference at 
00000000 
[ 80.296409] IP: [<c02f24ce>] rb_erase+0x112/0x250 
[ 80.296417] *pde = 00000000 
[ 80.296422] Oops: 0000 [#1] 
[ 80.296425] Modules linked in: kvm_amd kvm 
[ 80.296428] 
[ 80.296432] Pid: 3068, comm: test Not tainted (2.6.27.5 #124) 
[ 80.296436] EIP: 0060:[<c02f24ce>] EFLAGS: 00010046 CPU: 0 
[ 80.296440] EIP is at rb_erase+0x112/0x250 
...... 
how to find the location where it crash? 
答：
you can use objdump to disassemble the vmlinux, then you can search the rb_erase src code and assemble code, and will find some error in the rb_erase function.
【linux启动】
how to make linux boot info in serial：
qemu/kvm parameters: 
Change 
qemu -hda userver.img 
TO 
qemu -hda userver.img -serial stdio 
OR 
Change 
kvm -hda userver.img 
TO 
kvm -hda userver.img -serial stdio 
Change grub's menu.lst 
kernel ... 
TO 
kernel .... console=ttyS0,9600 console=tty0 
then, then you can see boot messages in host console. 
【oprofile问题】
while using oprofile, I met some troubles:
1. if i install oprofile with apt-get, opreport would report "opreport error: basic_string::erase" whatever i've done, e.g: 
>opcontrol --init
>opcontrol --start --no-vmlinux
>opreport
...
2.after that i tried to compile & install oprofile from source code 0.9.4, while compiling, i met
"error: call to '__open_missing_mode' declared with attribute error: open with O_CREAT in second argument needs 3 arguments", and I found a patch from http://www.google.com/url?sa=D&q=http://www.nabble.com/arguments-of-open()-td14924588.html&usg=AFQjCNGYfzlZsU74mdysoGhPzqdCZ4fISA, and patched /usr/include/bits/fcntl2.h, then completely passed make and make install. But then when i tried:
>opcontrol
but it told me no opcontrol found at /usr/bin/opcontrol...
Thx~
答：
oprofile usage: 
1 you should build a oprofile enabled kernel. 
make menuconfig 
General setup 
--> OProfile system profiling (EXPERIMENTAL) 
should be choose. 
then, the .config file should have line 
CONFIG_OPROFILE =y 
or 
CONFIG_OPROFILE=m 
2 now get the oprofile package 
apt-get install oprofile 
or you can get a gui package 
apt-get installoprofile-gui 
3 use oprofile to measure a benchmark 
you can write a shell to do the work 
------- 
#!/bin/sh 
echo "profile the kernel:" 
opcontrol --vmlinux=/boot/vmlinux 
opcontrol --setup --event=CPU_CLK_UNHALTED:90000::1:1 
opcontrol --start-daemon 
opcontrol --reset 
opcontrol --start 
./BENCHMARK pipe 1 thread 5000 64 16 
opcontrol --stop 
opcontrol --dump 
opreport --symbols > log.out 
opcontrol --shutdown 
【网卡的中断处理】
我在做代码注释和分析的实验。
用的ucore版本是20110913-xinhaoyuan-ucore-mp64-8000c4c.zip 
我选择的题目是各类设备的中断处理过程。
我在trap.c和trap.h中都没有找到网卡的中断处理函数，请问是本来就没有，还是我应该到其他文件中找？
是否有同学老师遇到过相关的问题？谢谢！
答：
貌似这个版本就不支持网络吧 
我在最新的版本里大概的找了一下，没有找到和网络有关的东西。
网络支持也是上学期的扩展，还没有合并到我们分析的代码中。相关代码请参见下面链接。 
http://www.google.com/url?sa=D&q=http://code.google.com/p/u5proj/source/checkout&usg=AFQjCNGsfUFVerdW7Yjg6BPROTdg6Yjbpg
--向勇 
【串口中断】
>> 老师好，关于串口的中断处理函数的分析我也遇到了一个类似的问题。就是case语句中没有执行任何操作： 
>> case IRQ_OFFSET + IRQ_COM1: 
>> case IRQ_OFFSET + IRQ_KBD: 
>> …… 
>> 请问这是为什么，我在写代码注释和文档分析的时候应该怎么说明这个问题？谢谢。 
答：
按下面的函数调用顺序，中断信号会触发串口的处理操作。 
kcons_getc() 
cons_getc() 
serial_intr() 
cons_intr() 
serial_proc_data()
【管道容量】
> 向老师，袁助教：
> 您好，代码分析部分实验的插图我按照那个链接提示把图片放到附件里并链接到相应的位置去了，具体链接如下：
http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OsTrain2011/ca/6_1%EF%BC%8C&usg=AFQjCNG3y4Pu9LpJKJkMiK-OsU5N5HEdrQ
但是我不小心将图片也作为附件添加到如下位置去了：
http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OsTrain2011/test/fs%3Faction%3DAttachFile%EF%BC%8C&usg=AFQjCNFdo9i1F1gXrr3qq8UN7IPjra6LVA我好像不能删除。 
> 另外，代码测试部分实验我觉得usr-core里面的关于文件系统（fs）的测试部分已经很全面了，我选择管道这样一个很小的方面进行测试（当然usr-cor¬e里面已经存在与管道相关的测试文件）。根据我自己测的结果，在一个进程里面，当使用管道传递超过4000字节的内容qemu会崩溃（多进程不会出现这种情况）¬，相关链接如下：
http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OsTrain2011/test/fs%EF%BC%8C&usg=AFQjCNEa5pjW7zZEFF38o9zv8HkHW6W5Kg我不知道这样理解是不是正确的。如果这样理解是正确的话，我是不是可以尝试下修改VFS或SFS里的内容使之在单进程中不能使用管道或者使用管道有限制？ 
> 此致，
> 敬礼！

答：
我已删除你错放在“/test/fs”中的图片，并更正了在“/ca/6_1”中的图片显示格式。现可在页面中正常看到你的图片了。 
关于管道的容量问题，任何管道都是会有容量的限制，不管是一个进程内还是在两个进程用管道。你测试到的情况表明，ucore中的管道实现是不完善的。你可以对比¬一下Linux和ucore中你的例子时的区别。如果可能，把你比较的结果和原因分析告诉我。 
--向勇
【管道容量】
> 向老师： 
> 您好，今天我做了一些小的测试，分别在ubuntu和ucore的qemu中测试了管道的容量，我的感觉是，好像在多个进程之间管道的容量好像并不受限制，我测¬试了1000000字节，完全没问题，可能我测的数量还不够多。另外，在单进程之间有一些差异，ubuntu在65536字节时还能正常保证管道正常运行，但在¬65537字节时就不行了；而ucore的这一数据是4000/4001。我没有查到真正的理论数值，只是觉得ucore可能也想取2的整数倍（4096），不¬过由于自身结构占去了一部分字节。而在ubuntu中对这一问题做了相应的修改，在定义时附加了这一部分控件，使之能成为2^16，不过这也只是我自己的想法。¬不过相同的一点是，在同一进程时，ubuntu和ucore中在管道容量越界之后都会死掉不执行。 
> 在这两部分中我使用了相同构架的代码，具体的结果我通过截图的形式保存来了起来，分别保存为"Linux_ubuntu.PNG"、"Linux_ucore.¬PNG"，其中在ubuntu中我在越界时使用"Ctrl+C"退出，在ucore中我直接在终端中使用命令"q"退出，所以在qumu中会有"_"，我将两张¬截图都放在了附件中。 
> 此致，
> 敬礼！

答：
现在你可以查一下ucore和Linux的实现代码，就能解释你现在看到的现象。对这个现象的探究能让你真正理解管道的容量的含义。下面是一些提示。 
1）ucore-mp中文件“pipe_state.c”中定义的“PIPE_BUFSIZE”就是管道容量的上限，只要在管道读写的过程中已写入但没有读出的¬数据不超过这个上限，读写操作就能正常返回。你可以通过交替读写的例子来测试在这个边界处的现象。 
2）Linux中与pipe容量相关的代码如下：
http://www.google.com/url?sa=D&q=http://lxr.linux.no/linux%2Bv3.1/include/linux/pipe_fs_i.h%23L21&usg=AFQjCNHw4bzBK8qtQw0vq0ttjuu_QDjeyw
相关数据结构：
struct pipe_buffer
struct pipe_inode_info
unsigned int pipe_max_size = 1048576;
相关内核函数：
pipe_write
pipe_set_size
pipe_fcntl
相关手册：
http://www.google.com/url?sa=D&q=http://linux.die.net/man/7/pipe&usg=AFQjCNGonXa74EstcEbYyNenCnHoCSpM9A
pipe(7) - Linux man page
http://www.google.com/url?sa=D&q=http://linux.die.net/man/2/fcntl&usg=AFQjCNHGvKHUDLDdU3Wmo-1S0-Jg1n5_Lw
Changing the capacity of a pipe
--向勇
【磁盘管理】
1.free_pages_bulk的时候要检查相邻的块是不是可以合并，按理说应该前后相邻都要检查，而函数中使用语句buddy_idx = page_idx ^ size，只检查了一个方向啊？这有什么说法吗。 
2.free_pages_bulk的while循环中是不是应该在末尾增加语句size=size<<1，让块合并继续下去呢 
3.UVPT和VPT是什么

答：
每个buddy都是预先分好的，譬如在order=3这一层，1101000就和1100000一组。(注意，低三位为0) 
orz..我也觉得是这样 
Virtual Page Table。 页表的物理地址？

First，设计不是完美的，可以改进，如果你能够用例子说明你的改进是有效的，just do it! 
1 没有说法，可能是例子考虑不周 
2 可以的。 
3 UVPT是给用户态用的，完成基本要求可以不考虑UVPT. 
【调度算法】
> 老师好， 
> 我是祥明，计72的。我在弄调度算法，想分析各算法的吞吐率，等待反应时间 
> 等。。不过xv6好像不能直接用c库中的time.h，是不是想访问系统时间要像 
> printf那样从新编写一个单独函数？另外，测试性能时能否不用时间而用中断次 
> 数来判断？
答：
xv6中目前没有获取系统时间的系统调用，你需要写一个相关的系统调用来获取当前时间。系统中有多种不同精度的时间，利用时钟中断的计时就是一种。
--向勇
【磁盘中断】
> 向老师你好，我是李曦泽（2008011418），我在做第一次实验（代码注释）的过程遇到了一个问题，想问你一下。
> 我选的是各类设备中断处理的这个题目，关于磁盘I/O中断的部分我找到了中断信息的定义：
> IRQ_IDE，然后在trap.c中的dispatch函数里的一个switch语句中找到了它的处理过程，但是这个函数中是这样写的： 
> case IRQ_OFFSET + IRQ_IDE1:
> case IRQ_OFFSET + IRQ_IDE2:
> /* do nothing */
> break;
> 也就是对IDE中断什么也不做，请问这是为什么（或者是我没有找到正确的中断处理函数）？
答：
这是因为目前在“https://github.com/xinhaoyuan/ucore-mp64”中的IDE操作并没有使用中断方式，而是在磁盘读写操作时直接使用轮询方式完成的，参见“ide.c”中的函数。
对这一部分的改进工作可参见“http://www.google.com/url?sa=D&q=http://code.google.com/p/u9proj/source/checkout%E2%80%9D&usg=AFQjCNFp5yEFrXvnEtVur8pJHJX7KlD0XQ。这是上学期操作系统课时完成的一个扩展，支持IDE的中断和DMA。 

