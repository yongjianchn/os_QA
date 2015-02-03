##基础知识

是否一般认为fork后父子进程的先后执行顺序是不确定的，vfork出来的子进程必定先于父进程执行？ 
答：
fork后父子进程的先后执行顺序是不确定的。 
vfork后父进程会等子进程结束或调用exec()。 
详细描述参见：
http://www.google.com/url?sa=D&q=http://en.wikipedia.org/wiki/Fork_%2528operating_system%2529&usg=AFQjCNGPk2EeS_rM888sdYOXTiO081-DBg



##Ucore实验相关

