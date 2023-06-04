# GO学习笔记

## 快速学习

### 参数传递

**go仅有by value，但是有一些引用类型（slice-map）**     

https://goinbigdata.com/golang-pass-by-pointer-vs-pass-by-value/

ans：和C一样go仅仅提供pass by value，但是map与slice是引用类型（通过make分配的）基本类似指针。如果想要修改其他的那就使用指针。

Go的引用类型：Reference types in Go are the set of slice, map, channel, interface, and function types. 

Go里面仅仅具有by value

```go
func passTestStr(str string) {
	str = "hello"
}
/*
这样肯定不会改变原来的str，
自己琢磨一下吧
*/
```

### 变量的声明及其定义

但是var和自动推到的有何区别？

```go
var mp map[string]int=map[string]int{
    
}

mp :=map[string]:int{
    
}

var mp map[string]int=make(map[string]int)
```



### Go的OO

Go是如何实现OO的？

* 封装：data+方法：方法与receiver
* 继承：这个基于对象即可实现
* 多态：这个就是接口

### Go的IO

io占领半壁江山，stdio，文件IO，格式化。这些一定要熟练。

读取文件

其实IO也没多少，格式化IO、文件IO。大多继承自C那一套，println知道我们是什么类型。

```go
//基本家族：打印stdio
fmt.Println();
fmt.Printf();
fmt.Print();

//打印到文件与字符串
fmt.Fprintf(os.Stdout, "%d\t%s\n", num, line)
str := fmt.Sprintf("the str:\t%s\n", line)
```

gopl的代码说第13，16章有解释。

### Go工程化

~~目前必须在gopath，goroot下写代码，不然它找不到我们写过的Go包。~~

~~所以我们如何使用自己包里的东西呢？难道功能相同的必须写一块（一个文件）？~~

~~这个不要太过于费心，一个包应该类似于一个头文件、一个类。~~

这个需要拿go来做项目。

### 时间

### 杂记

go的函数先学一下，如果不能想C++那样bind改变参数个数，与lambda那样方便的话，使用起来还是很不方便的。

### 包管理

go目前都是使用module/mod来管理一个项目，之前的gopath方法已经不适用了，但是`GOPATH`依然作为go的环境变量。

>  module is a go-project, package usually is a directory.
>
>  A module has one or more packages, a package usually collect in the same directory.
>
>  import a package.
>
>  并且一个package往往和文件夹一个名字

**pay pttention**      

```bash
import (
	lib010 "github.com/qzQi/leetcodeGo/010"
)
//010文件夹下是我们的lib010，但是这个import路径不是说import到lib‘s name，
//找到对应的文件夹即可，一般库的文件和文件夹名字一样，只不过这里自己没有用一样
//并且，同一个文件夹下不能有两个package
```

今天完成官网上面go的几个教程。

参考教程：这里就当作看的go文章吧。        

[how to write go code](https://go.dev/doc/code)这里面的testing没有看明白，不知道怎么使用。       

[go command](https://pkg.go.dev/cmd/go#hdr-Remote_import_paths)这个不太重要，通过go help可以得到。

[create a module](https://go.dev/doc/tutorial/call-module-code)第二个教程，很好。go module的一系列操作。

```bash
#使用本地的module，使用哪个module版本我们是可以指定的
go mod edit -replace example/greetings=../greetings（本地module的路径）
```

[develop a restful api with go and gin](https://go.dev/doc/tutorial/web-service-gin)    

```bash
设计一个web服务：先考虑一下提供哪些服务（API），设计消息的路由。
设计数据的存储，数据的格式。
```

[the declaration Syntax](https://go.dev/blog/declaration-syntax)go的声明语法为什么这么设计

[go测试框架的使用，以及代码的编写。](https://go.dev/doc/tutorial/add-a-test)



标题如下：

* How to Write Go Code

  > 如何import一个包，哪怕这个包在本module。
  >
  > A package's import path is its module path joined with its subdirectory within the module. For example, the module `github.com/google/go-cmp` contains a package in the directory `cmp/`. That package's import path is `github.com/google/go-cmp/cmp`. Packages in the standard library do not have a module path prefix.
  

从GO1.11版本以来GOPATH的方式已经慢慢不再支持，go module已经是默认的方式了。但是gopath不是完全不用了，我们通过mod down下来管理的还是放在了path。

module如何起名字，有什么限制吗？module这个确实对我来说是陌生的。

Go module是干嘛的？管理依赖，

> When your code imports packages contained in other modules, you manage those dependencies through your code's own module. That module is defined by a go.mod file that tracks the modules that provide those packages. That go.mod file stays with your code, including in your source code repository.

module名字的限制,就是为了让别人可以找到（使用go tool可以获取），如果不准备发布的话随便起一下名字

> In actual development, the module path will typically be the repository location where your source code will be kept. For example, the module path might be `github.com/mymodule`. **If you plan to publish your module for others to use, the module path(module name) *must* be a location from which Go tools can download your module. **

```bash
# create a go module
go mod init example/hello
# 引用import了一个外部的package后,下载后以后再需要相同的版本的话就不需要重复的下载了
# 可以通过clean清除所有下载过的module
go mod tidy
```

目前虽然可以找到子目录下面的package了，但是编译的时候不知道。解决了。

读一下go build命令。

看完上面的基本上就可以完成基本的包管理了。日常写程序可以但是还是得看。      

[go module reference](https://go.dev/ref/mod#go-mod-init)一个大而全的go module管理参考材料。         



### FAQ：          

go的提出本就不是为了提供一种思考性的语言，一切都是为了大规模的现代软件的开发。

https://go.dev/talks/2012/splash.article



> Go's purpose is therefore *not* to do research into programming language design; it is to improve the working environment for its designers and their coworkers. Go is more about software engineering than programming language research. Or to rephrase, it is about language design in the service of software engineering.

address：提出、解决

scales：拓展性

> It's important to recognize that package *paths* are unique, but there is no such requirement for package *names*. The path must uniquely identify the package to be imported, while the name is just a convention for how clients of the package can refer to its contents. The package name need not be unique and can be overridden in each importing source file by providing a local identifier in the import clause. These two imports both reference packages that call themselves `package` `log`, but to import them in a single source file one must be (locally) renamed:
>
> 路径名必须唯一，但是包名package可以不唯一，可以为导入路径起别名



### 项目管理

**同一个项目里的lib之间调用**        

例如：

```stylus
moduledemo
├── go.mod
├── main.go
└── mypackage
    └── mypackage.go  // package mp 定义包名为 mp
```

步骤：

1. 在项目下创建一个 go.mod 文件，文件名只能为这个。

2. 在 go.mod 文件中添加以下代码

   ```go
   module moduledemo  // 设定 moduledemo 为包根目录名，可以随意改变该名字，只需要导入时一致就好
   go 1.14  // 表明版本
   ```

3. 导入想要的包文件

   ```go
   import "moduledemo/mypackage"  // 这里是导入包目录下的包文件名
   ```

4. 使用包文件

   ```go
   mp.MyPackage()  // 使用包中的 MyPackage() 函数
   ```



**不在同一个项目里面**          

例如：

```maxima
├── moduledemo
│   ├── go.mod
│   └── main.go
└── mypackage
    ├── go.mod
    └── mypackage.go  // package mp 定义包名为 mp
```

步骤

1. 在 mypackage 下面创建 go.mod 文件，并添加以下代码

   ```go
   module mypackage
   
   go 1.14
   ```

2. 在 moduledemo 下面创建 go.mod 文件，并添加以下代码

   ```go
   module moduledemo
   
   go 1.14
   
   
   require mypackage v0.0.0  // 这个会在你执行 go build 之后自动在该文件添加
   replace mypackage => ../mypackage  // 指定需要的包目录去后面这个路径中寻找
   ```

3. 导入和使用

   ```go
   import "mypackage"  // 因为该包目录本身就是包文件所以无需添加下一级路径
   
   mp.MyPackage()  // 使用包中的 MyPackage() 函数
   ```

## a tour of go

现在觉得之前记的还不错。这个只能来学习一些基本的语法方面的。



### method

下面写的其实就是，method接收者的问题。https://go.dev/tour/methods/8

> Comparing the previous two programs, you might notice that functions with a pointer argument must take a pointer:
>
> while methods with pointer receivers take either a value or a pointer as the receiver when they are called:
>
> 参数类型与method的接收者类型



这一块需要注意的就是method的接收者类型：1）指针、2）value。



我们知道在普通的这个是不需要区分的，go的语法糖自动解引用，但是在实现接口的的时候需要区分是这个type实现的还是这个指针类型实现了接口。



比如我们为一个类型实现一个write接口，这样在需要write interface的时候就可以传入一个实现了这个接口（所定义方法）的类型。

```go
type ByteCounter int
func (c *ByteCounter)Write(p []byte)(int ,error){
    *c+=len(p)
    return len(p),nil
}

func main(){
    var c ByteCounter
    
    c.Write([]byte("hello"))//c可以调用自己所有实现的method
    
    //Fprint需要一个的参数类型是io.Writer，没什么要疑惑吧，这个是**参数类型**
    //emmm，这个例子主要是说明method 接收者确实有差别
    fmt.Fprintln(&c,"hello world")//ok
    fmt.Fprintln(c,"hello world") //error ByteCounter didn't impl the interface
    
    fmt.Println(c)//17,println结尾有换行
}
```





## go blog系列

### slice/string

https://go.dev/blog/slices          

go里面的引用数据类型（类似于oop，引用相同的底层元素）。

```go
type Slice{
    Size int
    Elems *interface
}

// 内置函数
cap(slice)
len(slice)// for most
```

切片的范围是左闭右开的，include left and exclude the right。

https://github.com/golang/go/wiki/SliceTricks slice使用技巧

第一次看的时候就觉得非常好。

https://go.dev/blog/all

https://go.dev/blog/strings











# 2023

## channel

**Go 中的 channel 是通过通信来共享内存的，而不是通过共享内存来通信的。这是一个微妙但重要的区别。**

在多线程编程中，线程之间通常是通过共享内存来进行通信的。也就是说，不同的线程可以访问同一块内存区域，通过读写该内存区域来交换数据和协调操作。这种方式虽然方便，但也容易引发并发问题，例如竞争条件、死锁、数据不一致等。



而在 Go 语言中，channel 是一种不同的通信机制，它并不直接共享内存，而是通过通信来实现数据的共享和同步。具体来说，**当一个 goroutine 向一个 channel 发送数据时，这个数据会被复制一份并发送给 channel，而不是直接写入共享内存**；当另一个 goroutine 从这个 channel 接收数据时，这个数据会被复制一份并返回给接收方，而不是直接读取共享内存。这种复制数据的方式，避免了多个 goroutine 直接读写同一块内存区域，从而避免了一些并发问题。

**Q: 所以channel更可靠，但是开销应该比传统的共享内存更大吧**     

是的，channel 相比于传统的共享内存方式，可能会增加一些额外的开销。这主要是因为 channel 实现了一些额外的逻辑，例如复制数据、通信协议、阻塞机制等。但是，这种开销通常是非常小的，可以忽略不计。同时，由于 channel 能够有效地避免一些并发问题，因此在复杂的并发程序中，使用 channel 可以使程序更加可靠和易于维护。

需要注意的是，在某些场景下，共享内存方式可能会更加高效。例如，当数据量较大时，复制数据会带来额外的开销，此时使用共享内存方式可能会更加合适。同时，由于 channel 本质上是一种异步的通信方式，因此在一些需要同步处理的场景下，使用共享内存方式也可能更加合适。因此，在实际编程中，需要根据具体的场景和需求，选择合适的并发模型。





# goland使用

IDE使用起来还是不顺手，之前一直是在命令行上面练习的，这个确实不熟悉！
