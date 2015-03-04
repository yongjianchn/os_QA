##基础知识
##Ucore实验相关

I am tring to do the first part of lab3.
Checking the content of memory by the internal debug command, I know i have copy the the right code and date into the memory. So I set the breakpoint at the last instrucion of the func env_pop_tf, and step to the next the instruction. The bochs debugger told me cpu has get the first instruction of the app, then I try to run next instruction again. the eip suddenly changed to a strange value, it seems jump back to the first instruction of BIOS. Did anynoe know this problem?
attachment: I didn't touch the func idt_init(), i don't know whether that can cause the phenomeon. 
答：
I had the same experience as you before. 
In my case, the app code, CR3, the stack, and CS:EIP were all initialized correctly (confirmed with bochs debugger). But when the first instruction was being executed, a general protection exception happened and therefore the system rebooted. 
I spent a lot of time on it and finally found out that I didn't grant PTE_U permission to a new page directory entry, created within pgdir_walk() of pmap.c, as I remember. 
This bug shows up only when in user-mode. For the previous lab, it just doesn't matter. 
You may have a different bug. But if the app initialization is indeed correct, it is very possibly permission-related, too. 
You may try first to run the app in kernel mode by temporarily changing CPLs of CS & SS to 0. 
Paul 
