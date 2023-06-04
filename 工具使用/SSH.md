# SSH原理及使用

SSH协议使用的非常频繁，远程登录，云服务器，GitHub这都需要使用。

https://wangdoc.com/ssh/basic.html很好的原理教程，阮一峰老师写的。

​       **之前记录的没什么用，之前写的有一个问题就是不知道自己在写什么。之后在看东西记录的时候注意记录，比如知识性的（这个就不需要进行记录，只需要记下在哪看的）；如果是用来解决特定问题的这个就需要好好记录下。**

## 基本知识

* shh是什么，为什么产生
* ssh的历史，发展演进
* ssh的架构模式，如何使用

我们使用的一般就是开源的OpenSSH项目，可以和OpenSSL做一下对比==》这俩没啥关联吧，一个是security shell网络协议，一个是加密算法。

SSH 的软件架构是服务器-客户端模式（Server - Client）。在这个架构中，SSH 软件分成两个部分：向服务器发出请求的部分，称为客户端（client），OpenSSH 的实现为 ssh；接收客户端发出的请求的部分，称为服务器（server），OpenSSH 的实现为 sshd。

OpenSSH 还提供一些辅助工具软件（比如 ssh-keygen 、ssh-agent）和专门的客户端工具（比如 scp 和 sftp）。

需要了解下如何使用这些工具，man ssh-keygen。

## 客户端

```bash
ssh user@host
ssh -l username host
ssh -p 22 host
```



  ssh 会将本机连接过的所有服务器公钥的指纹，都储存在本机的`~/.ssh/known_hosts`文件中。每次连接服务器时，通过该文件判断是否为陌生主机（陌生公钥）。

  用户个人的配置文件`~/.ssh/config`，可以按照不同服务器，列出各自的连接参数，从而不必每一次登录都输入重复的参数。

```bash
# windows
C://User/DELL/.ssh/config
```



GitHub里面我们上传的公钥信息是干吗？

其实公钥/私钥不只是只有服务器才能拥有，想要完全通过非对称的方式进行双向的通信需要两对公-私密钥。

**客户端几个文件：**      

更多信息在文章的客户端8.4

其实学习这种配置文件才是最快的了解一个服务的方式，比如redis/zookeeper。

SSH 客户端的全局配置文件是`/etc/ssh/ssh_config`，用户个人的配置文件在`~/.ssh/config`，优先级高于全局配置文件。

* `~/.ssh/knows_hosts`

  存放的是本机连接过的所有服务的公钥的指纹，当首次连接一个陌生的服务器的时候会进行显示。

  服务器密钥变更后，会导致在knows_hosts文件里面记录的主机的信息与服务器的信息不一致导致无法登录。比如服务器重装系统、重装了sshd，这会导致服务器重新生成公钥指纹，这时候就需要登录服务器的用户手动进行更新。`ssh-keygen -R hostname`，删除指定服务器的信息。

  通过ssh远程连接sshd肯定要验证sshd的信息，这个就是放在knows_hosts文件。

* `~/.ssh/config`文件

  文件内容，以后通过ssh登陆的时候直接`ssh tencent == ssh ubuntu@101.42.23.45`

  ```bash
  Host tencent
    HostName 101.42.23.45
    User ubuntu
  ```

客户端主要就是这两个文件，一个用来验证服务器的密钥指纹。

## 密钥登录

SSH 默认采用密码登录，这种方法有很多缺点，简单的密码不安全，复杂的密码不容易记忆，每次手动输入也很麻烦。密钥登录是比密码登录更好的解决方案。

客户端也可以生成自己的公钥和私钥，就像我们在使用GitHub那样，将客户端（与sshd对应）的公钥上传到服务器指定的位置，这样就可以免密登陆了。

**密钥登录的过程：**           

* 客户端使用ssh-keygen生成自己的公钥与私钥
* 手动的把客户端公钥放入server端的指定位置
* client发起ssh登录的请求
* 服务器收到用户 SSH 登录的请求，发送一些随机数据给用户，要求用户证明自己的身份。
* 客户端收到服务器发来的数据，使用私钥对数据进行签名，然后再发还给服务器。
* 服务器收到客户端发来的加密签名后，使用对应的公钥解密，然后跟原始数据比较。如果一致，就允许用户登录。

**使用ssh-keygen生成密钥：**          

使用ssh-keygen生成一对公钥/私钥，还可以用来修改knows_hosts文件。

```bash
# -t是指定加密算法的类型；-C是注释，格式就是邮箱，产生一个rsa算法的公钥/私钥。
ssh-keygen -t rsa -C "youremail@example.com"
```

输入命令后`~./ssh/`下会生成两个文件，分别是公私钥。

一些常用命令·：

* `-F`参数检查某个主机名是否在`known_hosts`文件里面。

  `ssh-keygen -F example.com`

* `-R`参数将指定的主机公钥指纹移出`known_hosts`文件。

  `ssh-keygen -R examle.com`

这里设计的文件就是通过ssh-keygen后产生的两个文件，一个公钥一个私钥。

**上传公钥到服务器：**            

生成密钥以后，公钥必须上传到服务器，才能使用公钥登录。          

OpenSSH 规定，用户公钥保存在服务器的`~/.ssh/authorized_keys`文件。

你要以哪个用户的身份登录到服务器，密钥就必须保存在该用户在服务器的主目录的`~/.ssh/authorized_keys`文件。==》比如我的服务器有一个用户Ubuntu，现在我想通过密钥登录服务器的Ubuntu用户，这种情况下我就需要将本地生成的公钥上传到Ubuntu用户的`~/.ssh/authorized_keys`文件。

公钥上传后就可以直接免密登录了，用户的`~/.ssh/authorized_keys`文件的每一行表示一个公钥信息。

这里涉及的文件就是服务器上·用户的`~/.ssh/authorized_keys`文件，存放的允许远程登陆的用户的公钥信息，可以不使用密码登录。



一般为了安全性我们都会关闭使用密码登录，对于 OpenSSH，具体方法就是打开服务器 sshd 的配置文件`/etc/ssh/sshd_config`，将`PasswordAuthentication`这一项设为`no`。这样就只能通过公钥登录。

```bash
PasswordAuthentication no
# 更新配置后重启服务器sshd生效。
systemctl service restart sshd.service
# 目前Linux下的服务管理已经变为了systemctl 使用service的概念
# 具体的看一下以前的文章吧。用到的时候在Google吧
systemctl start sshd.service
systemctl stop sshd.service
systemctl restart sshd.service
# restart，并且去读配置文件demeon CTRL+D
systemctl enable sshd.service
#下次开机自动运行
```

```bash
PermitRootLogin yes
PasswordAuthentication yes
# 可能有的Server是不允许我们使用root作为remote登陆的，比如mysql的server就不允许使用root登录
```



嗯，到目前为止已经解决了自己的问题，接着看完这个文章。以后解决问题要考虑一些原理性的东西，这些原理自己是知道的，比如上次登录MySQL，只要我们主机的MySQL Server服务是开放的（打开监听socket），以及我们的port打开了，**还有iptables防火墙是允许的**，那么就不应该在这些上面找原因了。



## SSH服务器

SSH 的架构是服务器/客户端模式，两端运行的软件是不一样的。OpenSSH 的客户端软件是 ssh，服务器软件是 sshd。正如我们的MySQL Client（多种多样）与mysqld



sshd的知识以后再说吧。