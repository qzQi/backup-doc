# docker使用

https://yeasy.gitbook.io/docker_practice/   教程

## overview

[docker overview](https://docs.docker.com/get-started/overview/)           

docker中有这样几个概念：

- dockerfile
- image
- container

实际上你可以简单的把image理解为可执行程序，container就是运行起来的进程。

那么写程序需要源代码，那么“写”image就需要dockerfile，dockerfile就是image的源代码，docker就是"编译器"。

因此我们只需要在dockerfile中指定需要哪些程序、依赖什么样的配置，之后把dockerfile交给“编译器”docker进行“编译”，也就是docker build命令，生成的可执行程序就是image，之后就可以运行这个image了，这就是docker run命令，image运行起来后就是docker container。

具体的使用方法就不再这里赘述了，大家可以参考docker的官方文档，那里有详细的讲解。

Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

**Docker如何工作的**       

docker使用了常见的CS架构，也就是client-server模式，docker client负责处理用户输入的各种命令，比如docker build、docker run，真正工作的其实是server，也就是docker demon，值得注意的是，docker client和docker demon可以运行在同一台机器上（不是废话吗//）。

docker的基本工作流程。

* docker build

  当我们写完dockerfile交给docker“编译”时使用这个命令，那么client在接收到请求后转发给docker daemon，接着docker daemon根据dockerfile创建出“可执行程序”image。

* docker run

  有了“可执行程序”image后就可以运行程序了，接下来使用命令docker run，docker daemon接收到该命令后找到具体的image，然后加载到内存开始执行，image执行起来就是所谓的container。

docker build和docker run是两个最核心的命令，会用这两个命令基本上docker就可以用起来了，剩下的就是一些补充。

那么docker pull是什么意思呢？

我们之前说过，docker中image的概念就类似于“可执行程序”，我们可以从哪里下载到别人写好的应用程序呢？很简单，那就是APP Store，即应用商店。与之类似，既然image也是一种“可执行程序”，那么有没有"Docker Image Store"呢？答案是肯定的，这就是Docker Hub，docker官方的“应用商店”，你可以在这里下载到别人编写好的image，这样你就不用自己编写dockerfile了。

docker registry 可以用来存放各种image，公共的可以供任何人下载image的仓库就是docker Hub。那么该怎么从Docker Hub中下载image呢，就是这里的docker pull命令了。

因此，这个命令的实现也很简单，那就是用户通过docker client发送命令，docker daemon接收到命令后向docker registry发送image下载请求，下载后存放在本地，这样我们就可以使用image了。

**Example `docker run` command**        

The following command runs an `ubuntu` container, attaches interactively to your local command-line session, and runs `/bin/bash`.

```
$ docker run -i -t ubuntu /bin/bash
```

When you run this command, the following happens (assuming you are using the default registry[docker hub] configuration):

1. If you do not have the `ubuntu` image locally, Docker pulls it from your configured registry, as though you had run `docker pull ubuntu` manually.
2. Docker creates a new container, as though you had run a `docker container create` command manually.
3. Docker allocates a read-write filesystem to the container, as its final layer. This allows a running container to create or modify files and directories in its local filesystem.
4. Docker creates a network interface to connect the container to the default network, since you did not specify any networking options. This includes assigning an IP address to the container. By default, containers can connect to external networks using the host machine’s network connection.
5. Docker starts the container and executes `/bin/bash`. Because the container is running interactively and attached to your terminal (due to the `-i` and `-t` flags), you can provide input using your keyboard while the output is logged to your terminal.
6. When you type `exit` to terminate the `/bin/bash` command, the container stops but is not removed. You can start it again or remove it.

## Get Start

慢慢读一下手册就什么都清楚了，读手册你感觉是最慢的，但是其实最快，最系统。

`docker -- help  ; docker run --help`

docker的各个容器之间的网络怎么解决？docker网卡？

**CLI reference**          

这一部分通过命令行的help可以很方面得知，而且通过的desktop管理非常方便。

- [docker version](https://docs.docker.com/engine/reference/commandline/version/)
- [docker run](https://docs.docker.com/engine/reference/commandline/run/)
- [docker image](https://docs.docker.com/engine/reference/commandline/image/)
- [docker container](https://docs.docker.com/engine/reference/commandline/container/)

help这几个就够了。



## docker run

**这里介绍的比较多，都是关于docker容器的使用。**

docker run是从一个image运行一个容器（理解为从源文件运行程序）；可以从一个image上面运行多个容器。

这样运行的多个容器哪怕是停止了依然没有被清除，下次可以进行restart。但是可能会不小心创建很多容器，这个就会消耗资源。（docker ps -aq显示所有曾经运行过但是没有清除的容器）

docker run -d是后台运行，其他模式都是退出后容器就停止了（可以重新进入）。后台运行的容器一直都是live状态，可以使用attach或者exec进入，也可以使用stop来停止运行。（参见docker attach/exec）推荐使用exec。

至于docker ps：默认只会显示处于Up状态的容器，使用-a参数显示所有的容器（包括Exited状态的），-aq只会显示id。

```bash
#这里也学了几个好用的bash命令，其实也就是脚本的基本使用
#组合docker pa相关的命令
docker stop $(docker ps -q)# 停止所有处于运行状态的容器
docker rm $(docker ps -aq)# 删除所有的容器

docker image rm $(docker images -aq)# 删除所有的image
```

docker容器的几个状态：Up，exited

```bash
docker pull ubuntu # default latest
docker run -i -t ubuntu /bin/bash #docker run [cmd] iamge args
#i：interactive -t：tty
#交互式是进入容器 -t参数是使用终端
```



## docker exec

看手册：docker exec help：

`Usage:  docker exec [OPTIONS] CONTAINER COMMAND [ARG...]`

可选的options，command就是对容器运行的程序（服务器/OS）执行一个命令，然后是这个命令的参数。

eg：`docker exec -it 71f5d6dfb026 /bin/bash `



## docker容器端口

docker如何使用网络，具体而言是如何为容器分配网络。比如我的容器运行了一个redis-server，那么这个容器应该运行在哪个端口？容器的端口与自己主机端口之间的映射。
