##基础知识

是否一般认为fork后父子进程的先后执行顺序是不确定的，vfork出来的子进程必定先于父进程执行？ 
答：
fork后父子进程的先后执行顺序是不确定的。 
vfork后父进程会等子进程结束或调用exec()。 
详细描述参见：
http://www.google.com/url?sa=D&q=http://en.wikipedia.org/wiki/Fork_%2528operating_system%2529&usg=AFQjCNGPk2EeS_rM888sdYOXTiO081-DBg

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



##Ucore实验相关
【lab4文档问题】
各位老师同学： 
在做lab4的时候发现文档中的一个问题，其中两次谈到priority>1，由于priority为整数，因此等价于priority>=2（不考虑机器表示¬）。按照priority>=2，bigstride理应为0xffffffff。我一开始就是这样做的，结果调试的时候才发现是由于priority取1导致¬的错误。 
在此提醒一下各位同学，也希望助教能够修订一下，谢谢！ 
程芃祺 
【lab4的问题】
老师您好： 
问一个关于lab4的问题，为什么我lab4完成之后make grade的时候primer总是wrong的，但是我单独运行primer的时候却没有问题呢？ 
-- 
付心仪 
答：
看 grade.sh 里面，primer 应该输出的是1000个左右素数。 
grade.sh 只测试了部分素数以及部分输出。你确定你的单独运行的输出里面，他测试的地方都存在就好了。
【lab4文档】
lab4的文档上说“running的进程不会放在run-queue中”，但后面又说“所有从running态变成其他状态的进程都要出run-queue，¬反之被放入某个run-queue中”，怎么感觉两者有矛盾…… 
答：
你说的是lab4的实验文档吗？还是oslab-chap4.pdf文档？ 
"所有从running态变成其他状态的进程都要出run-queue，反之被放入某个run-queue中"这句话有误。 
我的理解是： 
1 处于就绪态且没有占用CPU执行的进程都被放到run-queue中。 
2 如果run-queue中的某进程被schedule函数选择做为下一个占用CPU运行的进程，则此进程将从run-queue中去掉。 
3 占用CPU运行的进程其实其proc->state还是PROC_RUNNABLE，只是它不在run-queue中。 
4 占用CPU运行的进程如果自愿放弃CPU（sys_yield）or 时间片到了（RR调度算法设计），则会重新放放入到run-queue中。 
5 占用CPU运行的进程如果由于等待某资源（比如空闲内存）或事件（定时器），则会挂到等待队列或timer_list定时器队列中，对应的proc->stat¬e会是PROC_SLEEPING。一旦资源或事件得到满足，其proc->state会变成PROC_RUNNABLE，并重新放放入到run-queue中¬。

