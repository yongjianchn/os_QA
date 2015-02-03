##基础知识

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


##Ucore实验相关

