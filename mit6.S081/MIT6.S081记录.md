# MIT6.S081记录

准备学习他了，并作为一个复试/找工作项目。  为何执行能力这么差？  还是现在开始做吧。



你确实已经浪费了很长时间，但是现在不执行下来，浪费的时间只会更长。



相信自己吧，想完成OS lab这麽久了



## 实验环境准备

一些资源：        

* [前人经验：](http://doraemonzzz.com/2021/07/20/2021-7-20-MIT-6.S081-Operating-System-Enginerring-%E5%AE%9E%E9%AA%8C%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE/)
* xv6中文翻译：版本有点老了,不是riscv的https://th0ar.gitbooks.io/xv6-chinese/content/content/preface.html
* 课程中文翻译：https://mit-public-courses-cn-translatio.gitbook.io/mit6-s081/非常推荐

读一遍网站内容。       



配好环境，实验VScode调试vx6.



好吧，配饰环境就卡住了，试试docker。        环境一大堆依赖&&错误，看看如何使用docker吧。



好吧，困难小人开始工作了，确实南大的是一个好的选择，我想做的是调试xv内核代码。



但是6.S081仅仅是mit的课程类似南大，xv6貌似是完整的！！！所有就是做南大的lab，调试xv6。



依赖太难搞！！！      今天就用来配置一下！！！



算是配好了，但是不知道qemu怎么使用，如何退出？ctrl+a按下之后（抬起），按下c.

* 参考1：https://blog.csdn.net/Leo_h1104/article/details/115580753   配置
* 参考2：https://zhuanlan.zhihu.com/p/501901665    配置vscode调试xv6



通过命令行GDB调试以及通过vscode调试只能选一个。

`.gdbinit`中有`target remote 127.0.0.1:26000`,这个文件会被gdb最先执行一遍.

此外,我们在`launch.json`中指定了` "miDebuggerServerAddress": "127.0.0.1:26000"`,同一个remote address被配置了两次.

只需要把`.gdbinit`中的注释掉即可

```text
set confirm off
set architecture riscv:rv64
@REM target remote 127.0.0.1:26000
symbol-file kernel/kernel
set disassemble-next-line auto
set riscv use-compressed-breakpoints yes
```

`@REM`就是`.gdbinit`的注释符号.其实直接使用#也是可以的！！！

要是你使用命令行gdb,记得再改回来.         



配置好了，真是太舒服了。



安装了RISC v的工具链。          

生成compile_commands.json文件，但是没有使用bear生成该文件也可以跳转➕补全。       bear使用适合老的makefile文件，cmake是可以自动生成compile_commands.json文件的。 

```bash
# cmake条件下使用
cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1
# makefile条件下使用
bear make
```





环境打造好了！！！



如何在qemu里面调试，查看进程信息，`info mem`     

不知道如何在qemu启动的时候输入调试信息。



先看看文档吧，RISC v再说吧。看看qemu的doc。       

https://www.qemu.org/docs/master/system/mux-chardev.html   好吧，其实啥也没看出来。ctrl+a c进入monitor与退出，这个ctrl+a c中间是分开的！！！       



layout：用于分割窗口，可以一边查看代码，一边测试。主要有以下几种用法：
layout src：显示源代码窗口
layout asm：显示汇编窗口
layout regs：显示源代码/汇编和寄存器窗口
layout split：显示源代码和汇编窗口
layout next：显示下一个layout
layout prev：显示上一个layout
Ctrl + L：刷新窗口
Ctrl + x，再按1：单窗口模式，显示一个窗口
Ctrl + x，再按2：双窗口模式，显示两个窗口
Ctrl + x，再按a：回到传统模式，即退出layout，回到执行layout之前的调试窗口。

**最常用的就是`layout asm/src/split` 以及退回`ctrl+x [pause] a`**







## lab01 utilities

xv6book是必看的，翻译必看！！！    还有答案。



一定要写啊！！！tmd git的操作都忘得差不多了！！！呃呃 vscode里面的分支操作很好。



一个一个写，别管那麽多了！！！



这个lab是学习使用xv6的syscall接口来实现一些user程序。和普通系统编程一样，这个不过需要修改下makefile







## lab01 syscall

这个实验就是为内核添加sys call了，第一次深入内核。
