## gdb

### 调试常用的命令



### 调试运行中的进行



### 调试多线程





## vim





## 三剑客

### grep

### sed

### awk





## 进程

### top/htop

htop的使用更加方便，交互式的。

还是不太会用，看看面积补充吧

### ps





## 内存泄漏检测

* 编码层面，使用RAII，智能指针，这些工具尽量避免
* 检测层面，只会valgrind

```bash
valgrind --leak-check=full ./a.out
```



Valgrind是一款非常强大的工具，它可以用于检测程序中的内存泄漏和其他错误。下面是一些经常使用的Valgrind技巧：

1. 使用--leak-check选项：--leak-check选项用于检测内存泄漏，它有三种模式：none、summary和full。一般情况下，使用--leak-check=full模式可以检测出所有的内存泄漏。例如：

   ```
   valgrind --leak-check=full ./program
   ```

2. 使用--show-reachable选项：--show-reachable选项用于显示所有可以访问到的内存块，即使它们没有被释放。这对于查找一些难以定位的内存泄漏问题非常有用。例如：

   ```
   valgrind --leak-check=full --show-reachable=yes ./program
   ```





## 压测工具

自己平时写程序很少关注这一块，一般就是跑几个样例，测试一下，确实是短板。

但是我知道经常使用的一些压测工具比如apache jmeter（Java写的再说吧），

apache bench：http测试工具。



