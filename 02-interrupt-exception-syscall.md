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
