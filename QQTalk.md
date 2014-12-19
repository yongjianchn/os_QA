# QQ群聊天记录精选（请勿转载）

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
