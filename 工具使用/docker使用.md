# Docker 使用

[Docker Practice 教程](https://yeasy.gitbook.io/docker_practice/)

## 概述

在 Docker 中有几个核心概念：

- Dockerfile
- 镜像（Image）
- 容器（Container）

可以简单地将镜像理解为可执行程序，而容器则是运行起来的进程。

编写 Docker 镜像就像编写源代码一样，需要使用 Dockerfile，Dockerfile 可以看作是镜像的源代码，而 Docker 就是这个“编译器”。

因此，我们只需在 Dockerfile 中指定所需的程序和配置依赖，然后将 Dockerfile 交给 Docker 进行“编译”，即使用 `docker build` 命令，生成的可执行程序就是镜像（image），然后可以使用 `docker run` 命令来运行这个镜像，运行起来的镜像即成为容器（container）。

具体的使用方法不在这里详述，你可以参考 Docker 的官方文档，其中有详细的讲解。

Docker 的工作原理如何？

Docker 使用常见的客户端-服务器（client-server）架构，其中 Docker 客户端负责处理用户输入的各种命令，如 `docker build`、`docker run`，而真正工作的是 Docker 守护进程（daemon），即 Docker Daemon。值得注意的是，Docker 客户端和 Docker 守护进程可以运行在同一台机器上。

Docker 的基本工作流程如下：

- Docker Build

  当我们将 Dockerfile 提交给 Docker 进行编译时，使用的是 `docker build` 命令。客户端在接收到该命令后会将其转发给 Docker 守护进程，然后 Docker 守护进程根据 Dockerfile 创建出“可执行程序”镜像（image）。

- Docker Run

  有了“可执行程序”镜像后，就可以运行程序了。接下来使用 `docker run` 命令，Docker 守护进程接收到该命令后会找到具体的镜像，然后将其加载到内存并开始执行，镜像在运行时就成为容器（container）。

`docker build` 和 `docker run` 是两个最核心的命令，掌握了这两个命令基本上就可以使用 Docker 了，剩下的只是一些补充知识。

那么 `docker pull` 是什么意思呢？

我们之前提到，Docker 中的镜像概念类似于“可执行程序”。那么我们从哪里下载别人编写好的应用程序呢？答案很简单，就像应用商店（App Store）一样，Docker 也有自己的“应用商店”，即 Docker Hub。你可以在 Docker Hub 上下载别人编写好的镜像，这样就不需要自己编写 Dockerfile 了。

Docker Registry 是用来存放各种镜像的地方，公共的镜像仓库供任何人下载，而 Docker Hub 就是 Docker 官方的“应用商店”。要从 Docker Hub 中下载镜像，就可以使用 `docker pull` 命令。

因此，`docker pull` 命令的实现也很简单，用户通过 Docker 客户端发送命令，Docker 守护进程接收到命令后向 Docker Registry 发送镜像下载请求，下载后存放在本地，这样我们就可以使用这个镜像了。

## 示例 `docker run` 命令

下面的命令运行一个 Ubuntu 容器，以交互方式连接到本地命令行会话，并执行 `/bin/bash`。

```bash
$ docker run -i -t ubuntu /bin/bash
```

运行此命令时，以下操作将会发生（假设你使用的是默认的 Docker Hub 配置）：

1. 如果你本地没有 `ubuntu` 镜像，Docker 将会从配置的镜像仓库中拉取它，就像你手动运行了 `docker pull ubuntu` 一样。
2. Docker 创建一个新的容器，就像你手动运行了 `docker container create` 命令一样。
3. Docker 为容器分配一个读写文件系统作为其最终层。这使得正在运行的容器可以在其本地文件系统中创建或修改文件和目录。
4. Docker 创建一个网络接口，将容器连接到默认网络上，因为你没有指定任何网络选项。这包括为容器分配一个 IP 地址。默认情况下，容器可以使用主机机器的网络连接来连接外部网络。
5. Docker 启动容器并执行 `/bin/bash`。由于容器是以交互方式运行并与终端连接的（由于 `-i` 和 `-t` 标志），你可以使用键盘输入并将输出记录到终端。
6. 当你输入 `exit` 终止 `/bin/bash` 命令时，容器停止但不会被移除。你可以再次启动它或将其删除。

## 入门指南

仔细阅读手册是了解 Docker 最好的方式，尽管阅读手册可能感觉速度较慢，但实际上是最快且系统化的方法。

你可以使用以下命令获取 Docker 的帮助信息：

```bash
docker run --help
```

Docker 容器之间的网络是如何解决的？是否使用 Docker 网卡？

CLI 参考

这部分可以通过命令行的帮助信息得知，而且使用 Docker Desktop 进行管理非常方便。

- [docker version](https://docs.docker.com/engine/reference/commandline/version/)
- [docker run](https://docs.docker.com/engine/reference/commandline/run/)
- [docker image](https://docs.docker.com/engine/reference/commandline/image/)
- [docker container](https://docs.docker.com/engine/reference/commandline/container/)

这些命令的帮助信息就足够了。

## docker run

docker run 命令用于从镜像运行一个容器（类似于从源代码运行程序）；可以从一个镜像上运行多个容器。

运行的多个容器即使在停止后也没有被清除，下次可以重新启动。但是如果不小心创建了许多容器，那会消耗资源（可以使用 `docker ps -aq` 命令显示所有已运行但未清除的容器）。

使用 `docker run -d` 可以在后台运行容器，其他模式下容器停止后会退出（可以重新进入）。后台运行的容器一直处于运行状态，可以使用 `docker attach` 或 `docker exec` 进入，也可以使用 `docker stop` 命令停止运行。推荐使用 `docker exec`。

至于 `docker ps` 命令，默认只显示处于运行状态的容器，使用 `-a` 参数可以显示所有容器（包括已停止的容器），`-aq` 参数只显示容器的 ID。

```bash
# 这里还学到了一些有用的 Bash 命令，实际上就是基本的脚本使用方法
# 组合使用 Docker 相关的命令
docker stop $(docker ps -q)  # 停止所有处于运行状态的容器
docker rm $(docker ps -aq)  # 删除所有容器

docker image rm $(docker images -aq)  # 删除所有镜像
```

Docker 容器有几个状态：Up、Exited。

```bash
docker pull ubuntu  # 默认是最新版本 latest
docker run -i -t ubuntu /bin/bash  # docker run [cmd] image args
# -i：交互式，-t：使用 tty
# 交互式是进入容器，-t 参数是使用终端
```

## docker exec

查看手册：`docker exec --help`：

```bash
Usage: docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

可选的选项是指定的，command 是要在容器中执行的程序（如服务器/操作系统），然后是该命令的参数。

例如：`docker exec -it 71f5d6dfb026 /bin/bash`。

## Docker 容器端口

Docker 如何使用网络，具体来说是如何为容器分配网络？例如，如果我的容器运行了一个 Redis 服务器，那么该容器应该运行在哪个端口上？容器端口与主机端口之间的映射。

容器的端口可以任意使用，例如，如果你使用的是 Redis 服务器，你可以直接使用容器的默认端口 6379，并将主机的某个端口映射到该端口。

端口映射是将主机的某个端口接收的流量转发到容器的过程。

```bash
docker run -d -p 6379:6379 redis
```

上述命令会在后台运行 Redis 容器，并将主机的 6379 端口映射到容器的 6379 端口。这样，你可以通过主机的 6379 端口访问 Redis 服务器。

如果你不指定主机端口，Docker 会自动选择一个可用的端口，并将其映射到容器的指定端口上。

你可以通过 `docker port` 命令查看容器端口和映射的主机端口的信息。

```bash
docker port <container_name or container_id>
```

例如：

```bash
docker port mycontainer
```

这将显示容器的端口映射信息。

对于容器之间的网络通信，Docker 提供了网络功能。你可以创建自定义网络，并将容器连接到该网络，以实现容器之间的通信。

Docker 还提供了一些网络驱动程序，如 `bridge`、`overlay`、`host` 等。每种驱动程序都有不同的行为和适用场景。

有关 Docker 网络的更多信息，请参阅 Docker 官方文档。



# 记录

这次是系统的学习，至少说dockerfile得知道怎么写。

先看看那个短的视频，然后看看官网的即可。

## 容器的状态

要使Docker容器保持在运行状态，您可以使用`docker run`命令的`-d`标志，它会将容器放入后台运行模式。此模式下，容器将在启动后继续运行，而不会自动退出。

```bash
docker run -it ubuntu bash
# 以交互式的方式运行一个Ubuntu容器，exit时候会自动退出容器处于exit状态

docker run -itd ubuntu bash
# -d参数保持在后台运行
docker exec -it container_name/container_id bash
# 运行一个处于running状态的容器
```

通常情况下，Docker容器会以后台模式运行。这使得容器可以在后台持续运行，而不会阻塞当前终端会话。

在使用`docker run`命令时，如果未指定`-d`标志，容器将默认以交互模式运行，并将终端连接到容器的标准输入（stdin）、输出（stdout）和错误（stderr）。这意味着容器的输出将直接显示在当前终端上，而且如果您退出当前终端会话，容器也将随之停止。`docker run -it ubuntu bash`

为了将容器放到后台运行，您可以使用`-d`标志，它表示"detached mode"（后台模式）。这样，容器将在后台启动，并继续运行，即使您退出当前终端会话，它也会持续存在。`docker -itd ubuntu bash`

Docker容器有以下几种状态：

1. **Created**（已创建）：当您使用`docker create`命令创建容器时，容器进入此状态。在这个状态下，容器的文件系统已经创建，但容器并未启动。
2. **Running**（运行中）：当容器处于运行状态时，它被认为是运行中。在这个状态下，容器的进程正在运行，并且可以与之交互。
3. **Paused**（已暂停）：在某些情况下，您可能希望暂停容器的运行，而不是停止它。通过使用`docker pause`命令，您可以将运行中的容器暂停，使其进程停止执行。
4. **Restarting**（重启中）：当容器正在重新启动时，它被认为是处于重启中状态。这可能是由于容器崩溃或主动重启引起的。
5. **Exited**（已退出）：当容器的主要进程终止并退出时，它被认为是已退出状态。在这个状态下，容器不再运行，并且可以通过`docker start`命令重新启动。
6. **Dead**（已停止）：如果容器启动后立即退出，并且无法自动重启，那么容器将被认为是已停止状态。



## 容器数据的存储

每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为 **容器存储层**。

容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。**什么是容器的消亡？**

按照 Docker 最佳实践的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用 [数据卷（Volume）]()、或者 [绑定宿主目录]()，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高。

数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。因此，使用数据卷后，容器删除或者重新运行之后，数据却不会丢失。

> 容器的 "exit" 状态并不代表容器的消亡。当容器的主进程（或指定的主命令）完成运行后，容器将以相应的 "exit" 状态退出。这个状态可以用来表示容器运行的结果，比如成功与否、错误代码等。
>
> 容器的生命周期可以包括创建、运行、停止和删除等阶段。在容器停止运行后，它仍然存在，可以通过命令或 API 来查询容器的状态和相关信息。只有当容器被显式地删除时，才可以说容器被消亡。容器的消亡意味着容器完全从系统中移除，包括容器的存储层和相关资源的释放。
>
> 因此，在容器的消亡过程中，容器的存储层和相关数据也会随之删除，除非使用了持久化的存储方式，如数据卷。
>
> docker kill与stop都是让容器处于exit状态的命令，区别就是stop比较优雅，结束时候会发送SIGTERM信号给容器，而kill就是一个SIGKILL信号



## docker的安装

这一块没啥写的，资料很详细了。

## docker镜像-images

dockerfile==>images==>containers

docker-hub==>images==>containers

### 仓库获取镜像

也就是从仓库拉去镜像，使用docker pull命令。

和git clone一样，你也得说明在哪个服务器下，哪个用户的哪个镜像（及其版本）。

```bash
docker pull ubuntu:20.04 #拉取指定的版本
docker pull ubuntu:latest #拉去最新的版本，也是指定的动作
# 默认扩展到docker.io/library/ubuntu:latest library是官方的
```

运行：

```bash
docker run ubuntu:20.04 -it --rm ubuntu bash
#--rm表示容器exit之后自动进行rm，一般也用不到rm参数
```

### 镜像管理

**docker image**

看看手册就懂了

```bash
Usage:  docker image COMMAND

Manage images

Commands:
  build       Build an image from a Dockerfile
  history     Show the history of an image
  import      Import the contents from a tarball to create a filesystem image
  inspect     Display detailed information on one or more images
  load        Load an image from a tar archive or STDIN
  ls          List images
  prune       Remove unused images
  pull        Download an image from a registry
  push        Upload an image to a registry
  rm          Remove one or more images
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

Run 'docker image COMMAND --help' for more information on a command.
```

docker image list==>docker images

docker rm ==> docker container rm

docker ps ==> docker container ps

像kill、stop、ps、rm很多都是docker container的别名。

**镜像的删除：**

这个之后再看吧，什么容器、镜像是一层一层的。不太明白，用到了再说。



### commit与镜像

使用commit来理解image的分层。

`docker run -d --name webserver -p 8000:80 nginx`

--name将容器命名为webserver，并进行端口映射。

现在这个nginx运行起来了，但是记住nginx也是运行在OS上面的，所有可以进入这个容器进行配置。

```bash
docker exec --it webserver bash
#webserver是容器名字，bash是参数

root@686cc32bfa07:/# echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
root@686cc32bfa07:/# exit
```

我们修改了容器的文件，也就是改动了容器的存储层。我们可以通过 `docker diff` 命令看到具体的改动。

现在我们定制好了变化，我们希望能将其保存下来形成镜像。

要知道，当我们运行一个容器的时候（如果不使用卷的话），我们做的任何文件修改都会被记录于容器存储层里。而 Docker 提供了一个 `docker commit` 命令，可以将容器的存储层保存下来成为镜像。换句话说，就是在原有镜像的基础上，**再叠加上容器的存储层**，并构成新的镜像。以后我们运行这个新镜像的时候，就会拥有原有容器最后的文件变化。**（这个又是叠加一层的原理）**

docker commit就是使用当前的容器，保存为image也就是存档。

具体的语法用到再说，简单的就是

```bash
docker commit webserver nginx:v2
#这就完成了保存，接下来就可以使用nginx：2来运行了
docker run -d --name web2 -p 8000:80 nginx:v2
```

**不推荐使用commit来保持修改**

使用 `docker commit` 命令虽然可以比较直观的帮助理解镜像分层存储的概念，但是实际环境中并不会这样使用。

首先，如果仔细观察之前的 `docker diff webserver` 的结果，你会发现除了真正想要修改的 `/usr/share/nginx/html/index.html` 文件外，由于命令的执行，还有很多文件被改动或添加了。这还仅仅是最简单的操作，如果是安装软件包、编译构建，那会有大量的无关内容被添加进来，将会导致镜像极为臃肿。

此外，使用 `docker commit` 意味着所有对镜像的操作都是黑箱操作，生成的镜像也被称为 **黑箱镜像**，换句话说，就是除了制作镜像的人知道执行过什么命令、怎么生成的镜像，别人根本无从得知。而且，即使是这个制作镜像的人，过一段时间后也无法记清具体的操作。这种黑箱镜像的维护工作是非常痛苦的。

### Dockfile定制镜像

这个就是docker的脚本。

定制镜像也不是凭空定制的，也是在一些基础镜像的上面执行类似于commit的操作，自己叠加几层。

**FROM**

from指定了基础的镜像。

**RUN**

run指令是用来执行命令行命令的。

这个可以是任意的命令行指令？dockerfile应该没啥限制

但是为啥在容器里面下载不行？

容器可以访问外部网络吗？

**COPY**

使用copy将build上下文环境中的相关文件复制进镜像。

copy仅仅是复制文件，可能还需要通过RUN来修改文件的权限

对于URL指向的文件，还需要再使用RUN来进行解压缩，这就引出了ADD（不常用）这个。

**CMD**

这个和RUN有什么区别呢？RUN是在image构建的过程中使用的一些命令，主要是为image加入需要的层次。

之前介绍容器的时候曾经说过，Docker 不是虚拟机，容器就是进程。既然是进程，那么在启动容器的时候，需要指定所运行的程序及参数。`CMD` 指令就是用于指定默认的容器主进程的启动命令的。

容器的主进程！

在运行时可以指定新的命令来替代镜像设置中的这个默认命令，比如，`ubuntu` 镜像默认的 `CMD` 是 `/bin/bash`，如果我们直接 `docker run -it ubuntu` 的话，会直接进入 `bash`。我们也可以在运行时指定运行别的命令，如 `docker run -it ubuntu cat /etc/os-release`。这就是用 `cat /etc/os-release` 命令替换了默认的 `/bin/bash` 命令了，输出了系统版本信息。

在指令格式上，一般推荐使用 `exec` 格式，这类格式在解析时会被解析为 JSON 数组，因此一定要使用双引号 `"`，而不要使用单引号。

exec格式就是：

```bash
exec` 格式：`CMD ["可执行文件", "参数1", "参数2"...]
CMD [ "npm" , "start" ]
CMD ["nginx", "-g", "daemon off;"]
```

**EXPOSE**

用于暴漏端口，这个端口是容器的端口。

要将 `EXPOSE` 和在运行时使用 `-p <宿主端口>:<容器端口>` 区分开来。`-p`，是映射宿主端口和容器端口，换句话说，就是将容器的对应端口服务公开给外界访问，而 `EXPOSE` 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射。

**WORKDIR**

指定工作目录。

使用 `WORKDIR` 指令可以来指定工作目录（或者称为当前目录），以后各层的当前目录就被改为指定的目录，如该目录不存在，`WORKDIR` 会帮你建立目录。

### docker build

写好dockerfile就可以使用build来构建image了。build的结果是创建了一个image。

**镜像构建上下文context：**

https://yeasy.gitbook.io/docker_practice/image/build#jing-xiang-gou-jian-shang-xia-wen-context

当构建的时候，用户会指定构建镜像上下文的路径，`docker build` 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。

当使用 `../`里面的内容的时候就超出了上下文的环境，就会出错。



## 操作容器

`docker container --help`就知道所有的了。

### 启动

启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（`exited`）的容器重新启动。

因为 Docker 的容器实在太轻量级了，很多时候用户都是随时删除和新创建容器。

**从image启动**



**将处于exited启动**



### 守护态运行

**注：** 容器是否会长久运行，是和 `docker run` 指定的命令有关，和 `-d` 参数无关。

如果运行的主程序就无法长时间运行，那么容器还是会进入exited状态。

### 进入容器

在使用 `-d` 参数时，容器启动后会进入后台。

某些时候需要进入容器进行操作，包括使用 `docker attach` 命令或 `docker exec` 命令，推荐大家使用 `docker exec` 命令，原因会在下面说明。

原因就是使用attach进入容器之后再退出容器就会出去Exited状态，而通过exec进入的话就不会出现这种情况。

See Also: `docker exec --help`

### 容器的清除

容器创建后不会自动清除，除非run的时候指定了--rm参数（机会不会使用）

```bash
docker rm $(docker ps -aq) #移除所有的，由于running的无法运行所有只会移除exited
docker container prune #移除所有处于exited状态的
```



## 数据管理

### 数据卷

数据卷volume的管理方式。

如何创建一个数据卷，如何挂载一个数据卷。

比如将html文件挂载到nginx容器下的`/usr/share/nginx/html/`目录的下面，从而访问，而不是直接修改容器的数据层。

### 挂载主机目录



## 网络配置

Docker 允许通过外部访问容器或容器互联的方式来提供网络服务。

容器之间如何交流配合？也就是容器的编排，这就可以说是多机架构了，有分布式-微服务的感觉。

看到这里了，先不看了。

