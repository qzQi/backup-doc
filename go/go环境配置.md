## go环境配置相关

Linux下的配置确实简单，以python为例比如在`/usr/bin`下有多个python版本但是只需要存在一个symbol link文件指向想要的版本（最新的版本），这样的话更新也很方便也不需要先卸载再安装。（但是go不能通过apt安装最新版本）

Linux下直接使用包管理工具即可，go的那些配置呢？GOPATH这些，确实需要配置，C/C++不用配置是因为有固定的路径，比如：`/usr/include  /usr/local/include`。配置这些是为了知道到时候下载的包/库下载后放在哪。C++有专门的构建工具CMake，可以指定项目需要链接的库和头文件的路径。其实只是熟悉C++的而已，C/C++项目配置不算简单，需要配置运行环境（所依赖的库和OS的版本）。



但是window下一些配置倒是有些麻烦，不存在Linux下那样的软链接，以这次更新go的版本为例。确实比较麻烦，和应用软件不一样，这个还需要先进行卸载。但是下载后的环境变量，之前的设置会保存吗？

如果你想让新的版本在同样的文件夹下确实需要进行手动的先卸载，所以再次安装肯定要进行配置。奇怪，在环境变量里面没有发现go却可以使用。因为之前在环境变量的修改还在。



## 环境变量

其实不管哪个平台都要进行环境变量的配置，比如`GOPATH GOROOT`这些，C/C++不用配置是因为有固定的路径，比如：`/usr/include  /usr/local/include`。配置这些是为了知道到时候下载的包/库下载后放在哪。还有就是编译时候知道往哪里搜索模块。

搜一下需要配置哪些环境变量即可。

**相关环境变量**

- GOROOT

  Go 的安装路径

- GOPATH

  GOPATH 目录下主要包含以下三个目录: bin、pkg、src

  * bin：主要存放可执行文件
  * pkg：存放编译好的库文件，这里的库可以重用。
  * src：存放 Go 的代码源文件

- GO111MODULE

  GO111MODULE 是 Go 1.11 引入的新版模块管理方式，它的值决定了当前使用的构建模式是传统的 GOPATH 模式还是新引入的 Go Module 模式，默认值是 `on`。使用mod来管理项目。

### Windows

安装完成之后，系统会自动帮我们添加 GOROOT、GOPATH 和 PATH 环境变量，一般来说，我们可能会修改 GOPATH 为我们自定义的路径，在环境变量里面找到 GOPATH 变量进行修改即可。



这些知识确实不会影响我们写代码，但是确实一些编程的基本功。



之后再补充吧。

### Linux

这个官网给的很清楚了，基本就是把go加入PATH，每个用户都需要设置自己的相关配置。比如gopath。

推荐使用源码安装，这样才会安装最新版本，使用apt不太行。



GOPATH，GOROOT这些确实是需要自己设置的，就像说明我们安装的go包放在哪里？



有一点不清楚就是go是如何保存与使用那些公有的库的，比如C++/python就可以下载到本地使用。好像是保存到了gopath指定的地方。



其实也就是想明白，如何下载使用别人的module，如何使用本地的module。go从GitHub下就无语。

go mod相关的知识，不会了，这个得看项目具体实战。

还有就是go的包下载后还会分版本！ go使用一个包就会下载到本地，这也没什么大不了定期清除即可。



https://www.kandaoni.com/news/40730.html



## 包管理

终于可以系统的搜索了，这一块的知识主要就是了解`go mod` 和`import`的使用。

这里记的和写代码的关系不大，还是要多写代码，但是学习这些包管理还是必须的。
