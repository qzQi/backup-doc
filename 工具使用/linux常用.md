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
