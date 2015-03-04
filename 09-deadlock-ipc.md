##基础知识
##Ucore实验相关

lab5中我用make build-sem_wf（以及cdt_pc），然后make qemu，发现程序输出完全正确（多次尝试也正确，只不过有时顺序会稍有不同），但是make grade时却报错，提示缺少很多输出error: missing ......，不知这可能是什么原因？ 
而且有时候并没有改程序，只不过跑两次make grade，结果却不一样…… 
希望老师或助教解答一下，谢谢。 
答：
“error:> missing ....”的问题是因为现在的makegrade会存在执行顺序的检查，而一旦顺序不同就会报错。
我在实验报告中说明了不同的机器上由于使用的qemu版本的不同或者虚拟机配置的不同，可能造成的运行结果可能会不同，这也是为什么我在实验报告中增加了一些实¬验结果的原因。 
现在的makegrade是在我的虚拟机上测试通过的（也就是最终给大家计算成绩的机器），而且在最后计算成绩的时候，我也会重新检查makegrade出问题¬的同学。所以如果大家在makegrade时测试的结果出了问题，也不用紧张，可以检查一下自己的结果，如果确信结果正确可以直接提交。 
【lab5的问题】
什么情况下exit函数中的schedule函数会返回，导致panic("zombie exit")? 
我的程序在执行完大概六七个子进程后会panic("zombie exit")，不知道什么原因。

答：
我个人理解是变成zombie状态的进程应该不能占用CPU执行了。如果zombie状态的进程执行完schedule函数后，还能够占用CPU，则会执行到p¬anic("zombie exit") 这条语句。这是一种错误，所以系统报错。 
你是如何导致这种现象出现的？很有意思！希望能够在里的实验报告中详细说明此事。
----------------------------------------------------------------------------------------------------------------------------
【Implement of lib/fork.c】
hi,everyone~
i have a question about the implemention of lib/fork.c.
the comment says we should remap the page above the UTOP with the perm of COW is proper to use the sys_page_map to implement the lib routine?
as far as i know, the sys_page_map must chech the perm parameter.
// perm -- PTE_U | PTE_P must be set, PTE_AVAIL | PTE_W may or may not be set, 
// but no other bits may be set.
i think it says the COW mark could not be set, shen use the sys_page_map to map the parent's address space into child's address space.
so, how could I fix the makrs int the pte?

答：
PTE_COW is one bit of PTE_AVAIL. And your 'above' should be 'below', I
suppose. 
【关于SLUB的参考资料】
下面链接有几个关于SLUB的文档可参考。 
http://www.google.com/url?sa=D&q=http://os.cs.tsinghua.edu.cn/oscourse/OS2012/lab2%23head-3ca8e9d38a1d7519e114ce05d9d71b1aa4495079&usg=AFQjCNHC93RKzLX0kTkoFafikNwJ12wBNA
--向勇 
【address order first-fit 算法】
根据题目要求和default_pmm.c:default_check的assert序列要求，似乎是要实现address order first-fit算法.
这个算法需要保持地址有序，每次分配都从最低地址拿一个符合要求的block 
但line146 assert((p0 = alloc_page()) == p2 + 1);好像不对啊 
125 list_entry_t free_list_store = free_list; 
126 list_init(&free_list); 
127 assert(list_empty(&free_list)); 
128 assert(alloc_page() == NULL); 
129 
130 unsigned int nr_free_store = nr_free; 
131 nr_free = 0; 
132 
以上建立一个空free_list 
133 free_pages(p0 + 2, 3); 
此时前五页为 
| 0 | 0 | 1 | 1 | 1 | 
1=空，0=占用 
134 assert(alloc_pages(4) == NULL); 
135 assert(PageProperty(p0 + 2) && p0[2].property == 3); 
136 assert((p1 = alloc_pages(3)) != NULL); 
此时前五页为 
| 0 | 0 | 0 | 0 | 0 | 
| | 
p0 p1 
137 assert(alloc_page() == NULL); 
138 assert(p0 + 2 == p1); 
139 
140 p2 = p0 + 1; 
141 free_page(p0); 
| 1 | 0 | 0 | 0 | 0 | 
| | | 
p0 p2 p1 
142 free_pages(p1, 3); 
| 1 | 0 | 1 | 1 | 1 | 
| | | 
p0 p2 p1 
143 assert(PageProperty(p0) && p0->property == 1); 
144 assert(PageProperty(p1) && p1->property == 3); 
145 
146 assert((p0 = alloc_page()) == p2 + 1); 
line146 buggy？ 
按照first-fit，p0即第一个单元以空，地址最低 
此时alloc_page()用第一个Page即可 
p0==p2-1，不应该是p2 + 1吧 
难道我理解错了？ 
答：
你的分析是对的。 
我会尽快更新代码，并放到wiki和网络学堂上。 
谢谢！
【first/best/worst fit算法问题】
前面的和cyh同学的分析一样了。 
在assert((p0 = alloc_page()) == p2 - 1);之前，有 
p2 = p0 + 1; 
free_page(p0); 
free_pages(p1, 3); 
这样三行代码，在free的过程中，由原来的（1表示占用，0表示空余）。 
1 1 1 1 1 
p0 p2 p1 
变成了 
0 1 0 0 0 
p0 p2 p1 
我想问的是，在free的时候，四块空的会不会combine成一块？free_list是双向的（环形），如果在free的时候进行combine， 那么p1位置的三块是不是会与p0的那块合并成一块？ 
如果是这样的话，那么在assert((p0 = alloc_page()) == p2 - 1);的时候，p0应该是位于如下所标示的位置吧？这样的话应该是p0 = p2 + 1吧。 
0 1 0 0 0 
p2 p1 
p0 
请老师指导。 
答：
注意，我记得我课堂上说过是first/best/worst fit算法是用于分配和回收地址连续的空闲内存块。 
p1位置的三块与前面p0的那块无法合并成一个地址连续的空闲块，所以这样合并貌似与需求不符。
【空间分配问题】
请问这个函数的实现是不是有问题阿？无论如何都会报错…… 
具体的实现里面 
nunits = (nbytes + sizeof(Header) - 1)/sizeof(Header) + 1; 
之后会用这个数nu * sizeof(Header)来调用kalloc，这样的话正常情况下都不会是PAGE的倍数阿…… 
或者我理解错了？bow！ 
答：
我今早看到了此email。大家分析很认真，这是值得鼓励的事情！ 
我的理解是， malloc的调用关系是： 
malloc->morecore->sbrk(user level)->(kernel level)sys_sbrk->growproc->kalloc 
malloc传给morecore的是 nunits＝(nbytes + sizeof(Header) - 1)/sizeof(Header) + 1 
morecore传给sbrk的是 if nunits<4096 then 4096 * sizeof(Header) else nunits* sizeof(Header) 
设定此参数为 n，且一定>=4KB 
sbrk->sys_brk->growproc->kmalloc的值没有调整，都为n 
关键是kmalloc如何处理的： 
.... 
nr = n / PAGE; 
p = __alloc_pages(nr); 
...... 
这保证了分配的空间一定是page的倍数。但由于 morecore只用到了n，所以没有充分利用kmalloc分配的空间，但不会有错。 
【list_entry_t问题】
有人不知道 list_entry_t 是干什么的，不知道文档里面是不是解释了。 
这个是 linux kernel 里面的实现，实现的漂亮。 
所有的 le2xxx 实际上都是 macro，调用的 list.h 里面的 to_struct，也是宏。读懂它就知道这个双向链表是怎么实现的了。 
其实就是计算包含某个 list_entry_t 成员变量的数据结构里面该变量的偏移量，从而利用该 list_entry_t 的地址得到数据结构的起始地址，再强制类型转换成相应数据结构的指针。÷ 
【代码调试问题】
各位老师同学： 
请问大家是如何利用gdb调试lab2及以后的程序的？由于entry.S重置了gdt，将内核代码地址设置到3GB以上，从0xc0100000处开始真正的¬kern_init，而gdb却不能正确识别。如果直接用给的gdbinit，会找不到断点，从而一直执行。目前我的办法是b *0x100000，然后经过两次call以后到达真正的kern_init。但是之后设置的断点仍然无法识别，只能单步运行，并且next的执行效果和ste¬p一样了。请问老师有什么办法解决这个问题吗？非常感谢！ 
程芃祺 

答：
弄反了？内核从 3G 以上地址 link 的。entry.S 设置 gdt 保证内核被加载到低地址的物理内存时候也能工作。 
一种比较难看的方法是，编译两个版本的内核。 
一个是从 0x00100000 开始的，另一个是从 0xc0100000 开始的。调试的时候先用第一个，pmm.c 里面，lgdt 以后，开始用第二个。 
1. 可以这么复制 tools/kernel.ld 比如到，tools/tmp.ld，修改 tmp.ld 里面的 0xC0100000 改成 0x00100000. 
2/ 然后 Makefile 里面的 
$(V)$(LD) $(LDFLAGS) -T tools/kernel.ld -o $@ $(KOBJS) 
后面添上一个: 
$(V)$(LD) $(LDFLAGS) -T tools/tmp.ld -o bin/tmp $(KOBJS) 
完成实验记得改回来。 
ps, 实际上，gdb 只 binary 来识别符号。 
【shared memory的作用】
那个shared memory是用来记录等待队列的么？ 
用count和那个attach可以得到多少读者进程， 如果是写者优先的话呢？ 怎么知道有没有写者进程呢？ 
谢谢~ 
答：
shared memory 用于记录当前在读的读者的个数。
【调度策略问题】
请问，练习1中为什么对sub_sched_class进行了初始化，可是没有对sched_class进行初始化？关于这两个调度策略我不是很明白，是否有人¬可以帮忙解释下，谢谢！ 
答：
sub_sched_class用于MLFQ，RR中要不到。 
sched_class_RR定义了RR涉及的函数，在pinit中完成了对一个rq的sched_class的设置。 
这是我理解的初始化。
