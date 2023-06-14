自己平时由于不熟练而去Google的命令都要写到这里

### 系统管理

* systemctl与service

```bash
systemctl restart mysql
service mysql restart
```

* uname -a

  查看OS型号和cpu架构

* 查看cpu和mem info

  ```bash
  cat /proc/cpuinfo | vim - #忘了vim下如何不换行显示
  cat /proc/meminfo
  ```

  

  



### docker

```bash
docker-compose up -d //根据yml文件开启所有的容器
docker-compose ps
docker-compose stop //关闭所有的容器
docker-compose stop nginx

docker-compose --help
```

docker还是得学学啊，边缘计算平台全是微服务。

### 包管理



### 系统信息

```bash
uname 
```



### vim

* 查找

  在normal模式下按下`/`即可进入查找模式，输入要查找的字符串并按下回车。 Vim会跳转到第一个匹配。按下`n`查找下一个，按下`N`查找上一个。
  

### 进程查看

#### top使用

top就类似于Windows下的资源管理器。

这nm完全没有使用经验啊，



### 用户管理

从这次实验室开发的经验中获取，每次使用Linux一定不要暂时的省力气。

不同的用户开发一定要创建不同的用户！



## 文本三剑客

这些都是永远绕不过去的问题，必须熟练使用。分析日志的时候很有用。

还有正则表达式这些都是绕不过去的话题。

### 常用的正则表达式

正则表达式提供了更强大的模式匹配能力，但语法相对复杂，需要一定的学习和练习。通配符则更简单直观，适用于简单的文件名匹配。

在实际使用中，根据需求选择适当的工具和模式匹配方法，通常可以同时使用正则表达式和通配符来实现更复杂的模式匹配和搜索。

grep使用的正则表达式，find使用的是通配符。

### grep

*global regular expression print*

grep就是搜索字符串，在指定的文件中查找特定的字符串模式。注意与find的区分，find是在文件系统里面搜索指定的文件或者目录。grep主要在文件里面搜索字符串。

`grep [option...] patternStr [filename...]`

这里面文件名和模式字符串都可以使用正则表达式，比如`grep str ./*.txt`搜索所有的txt文件。

可以指定多个filename，而且可以使用管道。

grep的工作方式是这样的，它在一个或多个文件中搜索字符串模板。如果模板包括空格，则必须被引用，模板后的所有字符串被看作文件名。搜索的结果被送到标准输出，不影响原文件内容。

grep匹配的单位是行。

**常用的选项：**

* -i 忽略大小写
* -r 递归的搜索目录里面的所有文件，不然grep是不查找目录的
* -l 只显示包含指定字符串的文件名，可以和-r参数配合
* -n 显示匹配的行号，当多个文件的时候会自动的添加文件名
* -e 可以用来表示or的关系，多个匹配模式串
* -v 用于反选，也就是打印出没有匹配的行，也就是过滤匹配项
* 这里的选项太多了，用到了再添加。

```bash
grep 
```

其他的就是一些正则表达式的知识了。理解grep的原理，知道就是在文本中搜索匹配模式串的行。

### sed

### awk



## 文件查看

cat与vim就不说了太常用了。

tail与head分别是查看开头与结尾的几行。

### tail

对于实时更新的文件，tail是非常有用的，如果说是仅仅搜索过去的一段时间内的（也就是离线日志，分析过去的行为）那么使用grep没啥问题。

如果是实时更新的，那么先使用tail获取到更新的，然后再使用grep来搜需要的！
```bash
tail [option] fileName
* -n 指定要显示的行数eg： tail -n 100 显示尾部的100行
* -f 实时的查看文件新增的内容，适合于正在写入的日志
```

### head

查看前几行，也就一个-n参数。

### less

在vim无法使用，cat又是一下子全部输出的情况下，less可以用来查看文件，而且可以滚动和查找。

反正肯定没有vim功能那么多。

```bash
less [option] filename
* 使用 / 进行搜索
```



### more





## 文件查找

### find

`find` 是一个强大的命令行工具，用于在文件系统中查找符合指定条件的文件和目录。它可以根据不同的搜索标准进行高级文件搜索，并支持各种搜索选项和操作。find使用的不是正则表达式，而是一些通配符

不仅仅可以用来搜索，而且可以对搜索到的文件执行一些操作。find默认的就是递归的查找

`find [pattern] [expression]` 

```bash
find ./ -name uring -type d #在./文件夹下查找所有名为uring的目录
find ./ -name uring.hpp -type f #在./文件夹下查找所有名为uring.hpp的文件
find ./ -name CMakeLists.txt -type f
find ./ -name uring.hpp -type f -exec grep include {} \;
#递归的查找当前的文件夹下所有名为uring.hpp的文件，然后对所有的文件执行命令
```



### which

which用于查找可执行文件的路径，which直接查找PATH变量下的目录，所有快很多。
