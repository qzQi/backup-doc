rpc网络通信框架。

## 为什么需要rpc

rpc的场景主要就是微服务之间的通信，当客户端调用系统的服务时候直接使用rest调用即可。rpc就是将本地进程的调用和远程服务器方法的调用透明化。

自己之前一直不了解为什么需要rpc。现在懂了，又不知道以前迷在哪里。

关于这个问题，想想自己之前写的客户端程序，是如何请求服务器的。

* 不就是远程过程调用吗，不使用rpc不也可以完成？

  确实可以完成，但你想想需要干多少事情：1、建立socket连接  2、定义应用层交互协议  3、对每次交互的消息进行序列化/反序列化。       同时处理粘包等网络编程常用的细节处理。

  尽管在实现项目时候可以1、使用网络库 2、使用一些现成的序列化工具。  但是这些和调用本地进出的一个方法比起来还是差太多。

  *看那个有了http为什么需要rpc*

  ```C++
  //普通的C/S结构下 client请求server的过程
  int socketfd=socket();
  	connect();
  	send();//发送请求的消息
  	receive();//接收并反序列化
  ```

* rpc

  rpc就是远程过程调用，目的就是通过rpc框架让我们可以像调用本地方法一样，调用远程服务器方法。

  而要做到这一切需要rpc框架。

* rpc框架

  我们使用rpc关键是在一个rpc框架的帮助下，不用去管连接的建立，数据的序列化/反序列化。

  一句话来说就是：*像使用本地方法一样调用远程的方法* 。要这么方便的使用rpc就得有一个框架。 

  

  使用框架直接发布为rpc服务，简化了网络编程+数据序列化。
  
  
  
  

**我的理解rpc框架就是集成了一套网络库和序列化的工具。==>也就是对传统C/S架构的一层抽象。**

当然调用的约定还是需要之前双方约定好，比如基于protobuf的rpc，他们的约定就是proto文件。

比如下面这个，约定好调用接口，以及一些序列化结构体。

```C++
// 返回的好友信息是一个列表，repeated修饰表示多个/列表
message GetFriendListsResponse
{
    ResultCode result=1;
    repeated User friend_list=2;
}

// 在protobuf里面定义描述rpc方法的类型 - service
// 用于序列化rpc方法
service UserServiceRpc
{
    rpc Login(LoginRequest) returns(LoginResponse);
    // rpc GetFriendLists(GetFriendListsRequest) returns(GetFriendListsResponse);
}
```

感觉rpc和普通的c/s通信挺像的。

但是可以这样理解,rpc使得调用远程的服务更加简单；

但是感觉对于我们普通的客户端没有使用rpc的必要，就比如我那个简单的聊天服务器业务的客户端。



> **RPC 也是一种 C/S 架构，但它提供了一种更高级别的抽象，使得客户端和服务器之间的通信更加简单。**在 RPC 中，客户端调用远程过程（Remote Procedure）而不是发送低层次的请求，这些过程可以在另一个计算机上运行，而客户端程序看起来就像在本地调用一样。RPC 通常使用一些特定的协议，如 gRPC、Thrift、XML-RPC、JSON-RPC 等。



学习rpc的最大障碍：

目前根本遇不到需要rpc的场景，就我那组多几千行的代码哪里需要改为rpc。

**Q：rpc的使用场景**

> RPC (Remote Procedure Call) 是一种计算机通信协议，它允许一个进程调用另一个进程（通常在不同的计算机上）的子程序，就像本地调用一样。
>
> 以下是一些常见的使用场景：
>
> 1. 分布式系统：RPC可以在不同的机器之间传递数据，使得在分布式系统中的不同节点之间的通信更加简单、高效。
> 2. 微服务：微服务架构中的不同服务之间需要进行通信，RPC可以提供一个高效、可靠的通信机制。
> 3. 浏览器与服务器交互：在Web开发中，RPC可以用于浏览器与服务器之间的通信，例如使用JSON-RPC或XML-RPC进行远程调用。
> 4. 数据库访问：RPC可以用于数据库访问，例如使用Java RMI来调用远程数据库的存储过程或触发器。
> 5. 移动应用开发：移动应用通常需要与服务器进行通信，RPC可以提供一种高效的通信机制，例如使用gRPC进行移动应用与服务器之间的通信。
>
> 总之，RPC可以在许多不同的场景中使用，其中包括分布式系统、微服务、浏览器与服务器交互、数据库访问和移动应用开发等。

**Q:移动应用开发里面使用rpc和直接c/s通信比起来有什么好处**

> 移动应用开发中使用 RPC 和直接 C/S (Client/Server) 通信相比有以下优势：
>
> 1. 简化开发：RPC 提供了一种高层次的抽象，使开发人员能够更轻松地编写代码。相比之下，直接的 C/S 通信需要更多的低层次细节和处理。
> 2. 网络传输效率高：RPC 基于二进制协议，通常比使用文本格式（例如 HTTP）的 C/S 通信更快。此外，RPC 可以通过一些优化来减少网络传输的数据量，如协议缓存、数据压缩、流控等。
> 3. 可扩展性高：由于 RPC 提供了一个抽象的接口，可以很容易地添加或移除服务，因此具有良好的可扩展性。相比之下，C/S 通信的接口和协议通常是硬编码到应用程序中的，如果要更改或添加服务，则需要重新编写代码。
> 4. 可维护性高：RPC 提供了一些工具来检测和解决通信故障，这有助于降低维护成本。此外，由于 RPC 的抽象层次较高，因此更容易维护。
> 5. 支持多语言：RPC 通常支持多种编程语言，这使得开发人员可以使用不同的编程语言编写客户端和服务器端。而使用 C/S 通信，则要求客户端和服务器端都使用相同的编程语言。

**Q:有哪些弊端吗**

> RPC 也有一些弊端，主要包括以下几点：
>
> 1. 不适合高延迟和不稳定的网络环境：RPC 要求客户端和服务器之间的网络通信要快速、稳定，如果网络延迟高或不稳定，可能会导致通信失败或延迟过高。
> 2. 可靠性问题：RPC 通常基于 TCP 协议，虽然 TCP 可以保证数据的可靠性，但不能保证 RPC 请求的可靠性，如果 RPC 请求失败，则需要使用重试机制来解决。
> 3. 版本兼容性问题：如果客户端和服务器端使用不同的 RPC 版本，可能会导致不兼容，需要使用适当的兼容性策略来解决。
> 4. 性能问题：虽然 RPC 通常比 HTTP 通信更快，但由于需要进行序列化和反序列化，还有额外的网络传输协议开销，因此可能会对性能造成一定的影响。
> 5. 学习成本高：相比直接的 C/S 通信，RPC 通信需要使用特定的协议和库，需要一定的学习成本和技术储备。
>
> 总之，RPC 通信也有一些弊端，例如不适合高延迟和不稳定的网络环境、可靠性问题、版本兼容性问题、性能问题和学习成本高等问题。开发人员需要根据具体情况选择适合的通信方式。





## rpc配合协程

rpc应该只能配合协程吧，rpc框架里面还怎么获取IO事件？（感觉没办法，因为它不需要我们自己处理IO)

应该需要配合异步的协程库！ 异步rpc框架。

**Q:感觉rpc应该和协程异步配合起来，我想的对吗**

> 您的想法是正确的。RPC（远程过程调用）是一种在分布式系统中进行通信的技术。而协程则是一种并发编程模型，允许在单个线程内执行多个独立的代码段。在某些情况下，将RPC与协程异步配合使用可以提高系统的性能和可扩展性。
>
> 在使用RPC时，通常需要等待服务器返回结果，这可能需要花费一些时间。如果在等待结果时，线程被阻塞，那么系统的吞吐量将受到限制。异步编程允许在等待结果时执行其他任务，这可以提高系统的响应速度和并发能力。
>
> 协程可以在等待结果时释放线程，让线程去处理其他任务，而不是一直等待结果。当结果准备好时，协程可以恢复并继续处理结果。这种方式可以减少线程的数量，提高系统的并发性能和可扩展性。
>
> 因此，将RPC与协程异步配合使用是一种优秀的方式，可以提高系统的性能和可扩展性。
>
> 例子：
>
> 好的，假设我们有一个分布式系统，其中一个客户端需要从另一个客户端获取一些数据。我们可以使用RPC进行通信。在传统的同步RPC中，客户端将发送请求并等待响应，直到接收到响应后才能继续执行后续的操作。这样的做法会使得客户端的线程在等待结果时一直被阻塞。
>
> 如果我们使用异步编程的方式，可以使用协程在等待响应时释放线程，让线程去处理其他任务，而不是一直等待结果。当响应准备好时，协程可以恢复并继续处理响应结果。









## 框架-服务的发布方

> 最终在实现上仅仅写了rpcprovider，这个就足够完成服务的发布与相应rpc请求。

我们的框架肯定要处理粘包的，采用的方法就是定义payload，采用proto来说明发送的包格式。    

调用者需要按照这个来发送，被调用的仅需要发送函数返回值即可。调用者需要说明要调用`service` 及其 `method` 。

```protobuf
message RpcHeader{
	bytes service_name=1;
	bytes method_name=2;
	uint32 args_size=3;
}
```

获取参数的size之后，直接使用protobuf进行反序列化这个size大小的数据。



我们的框架要设计怎么样的接口来使用？

服务的提供方传入IP+port，来说明这个rpc服务运行的地址信息。

服务运行后将这个服务的配置信息发送到zookeeper，刚刚开始可以采用直连的方式请求服务，最后再用zookeeper

```C++
//使用我们框架的服务提供方： ./main 127.0.0.1 8000
//至于服务注册中心zookeeper的相关配置信息我们通过json文件保存也不需要解析了
//==》框架的使用者需要提供一个json配置文件，来配置zookeeper相关信息
//之后在服务运行后还需要通过zookeeper来注册这个服务的相关信息IP+Port，这就是后话了
int main(int argc,char** argv){
    InetAddr listendAddr(argv[1],atoi(argv[2]));
    EventLoop loop;
    //非得和之前的类比的话这个就是一个TcpServer
    RpcProvider provider(&loop,listenAddr,"loginServer");
    //将rpc节点进行发布，其实就是把这个rpc服务相关信息进行序列化
    provider.NotifyService(new UserService{});
    //底层使用的muduo网络库，开始进行eventloop，处理客户请求。
    //非得类比的话就是之前使用muduo里面的loop.loop();里面开始eventloop
    provider.Run();
}
```



**服务的发布者调用**

```C++
// step1：完成NotifyService，完成rpc服务的存储
// proto里的所有service都是继承自google::protobuf::Service
// notifyservice写好了，修改后的客户端调用方法
// notifyservice要做的不过是存储一下相关的信息
int main() {
    // cout<<"begin:"<<endl;
    // UserService *us = new UserService();
    // showServiceInfo(us);
    InetAddress listenAdde("127.0.0.1",12345);
    EventLoop loop;

    // woc，上面的和框架代码一样，可就是无法运行，这个就可以。
    RpcProvider provider(&loop,listenAdde,"test");
    //发布服务
    provider.NotifyService(new UserService());
    // 开启网络服务
    provider.Run();
}
```



**RPC的网络服务**

现在notifyservice写好了，服务的发布者存储了相关的服务信息；接下来就是处理网络请求了。

实现网络功能，也就是完成muduo的那几个回调操作。服务的注册先不写。

```C++
// 调用Server的start并开始eventloop，注册相应的事件回调
void RpcProvider::Run() {
    LOG_INFO<<"rpc node begin running!";
    
    // 设置相应的回调操作
    server_.setConnectionCallback(std::bind(&RpcProvider::OnConnection,this,_1));
    server_.setMessageCallback(std::bind(&RpcProvider::OnMessage,this,_1,_2,_3));

    // 向服务注册中心注册自己的服务信息，ip+port，这个稍后再写
    // 服务注册中心的相关配置保存在文件里面，使用json解析

    // 启动网络服务
    server_.setThreadNum(3);
    server_.start();
    loop_->loop();
}
```





**收到调用者的请求信息后进行rpc服务的分发**

接下来就是`OnMessage()`的书写，rpc服务收到调用者发来的数据，如何分发服务，如何找到对象请求的方法。

之前说过我们调用者与被调者之间的约定就是`rpcHeader.proto`，从里面解析出调用的服务+方法然后使用即可。 rpcHeader说明了要调用的服务及方法。

```C++
message RpcHeader{
    string service_name=1;
    string method_name=2;
    uint32 args_size=3;
}

//从serviceMap里面找出相关的service及method信息
//调用相关的method，实现NewCallback生产的closure，发送rpc调用的结构
//获取rpc请求后调用相关的方法，然后就是rpc的响应回调
```



相关阅读：protobuf的`service.h`  `callback.h` 写一个service的rpc方法然后生成后看看相关信息。



到此服务的发布代码基本完成rpcprovider.cpp也就是150行作用，可以完成编译运行，正确性需要完成调用方代码再进行确定。



实现一个服务的调用方，然后写一个业务代码进行测试。

## 框架-服务的调用方

> 最终的实现就是rpcchannel和rpccontroller，可以完成调用方的rpc调用，并返回调用信息。
>
> 客户端没有高并发的请求直接使用socket进行阻塞IO即可，序列化以及网络的发生都由channel处理。

比如我们的proto文件，经过protoc编译后就是会生成两个类：`UserServiceRpc  UserServiceRpc_Stub` 

```protobuf
service UserServiceRpc{
    rpc Login(LoginRequest) returns(LoginResponse);
}
```

阅读一下这个stub类，发现关键就是这个channel参数。调用Login也不过底层的channel在调用，阅读下channel的代码。服务的调用方最主要就是实现这个channel，stub在调用相应的方法eg：login时候底层就是在调用channel。         

channel又是一个基类的指针，那么我们就需要继承+override这个channel的callMethod。

之前说过我们框架的调用方和提供方之间的通信协议是rpcHeader的proto文件，如果我们可以在overridechannel的callMethod的时候能够在里面获取这次调用的service_name和method_name那就可以通信了。

服务的发布方就是实现mprpcchannel，继承自channel，覆盖channel的callMethod方法在里面进行调用rpc节点的服务。

```C++
class UserServiceRpc_Stub : public UserServiceRpc {
 public:
  UserServiceRpc_Stub(::google::protobuf::RpcChannel* channel);
  UserServiceRpc_Stub(::google::protobuf::RpcChannel* channel,
                   ::google::protobuf::Service::ChannelOwnership ownership);
  ~UserServiceRpc_Stub();

  inline ::google::protobuf::RpcChannel* channel() { return channel_; }

  // implements UserServiceRpc ------------------------------------------

  void Login(::google::protobuf::RpcController* controller,
                       const ::example::LoginRequest* request,
                       ::example::LoginResponse* response,
                       ::google::protobuf::Closure* done);
 private:
  ::google::protobuf::RpcChannel* channel_;
  bool owns_channel_;
  GOOGLE_DISALLOW_EVIL_CONSTRUCTORS(UserServiceRpc_Stub);
};
//可以看出stub对象在调用Login的时候就是调用的channel的callMethod，
//而channel是指针，并且CallMethod是虚函数，override它来做自己想要的操作
void UserServiceRpc_Stub::Login(::google::protobuf::RpcController* controller,
                              const ::example::LoginRequest* request,
                              ::example::LoginResponse* response,
                              ::google::protobuf::Closure* done) {
  channel_->CallMethod(descriptor()->method(0),
                       controller, request, response, done);
}
```



**MpRpcChannel**   

这是给rpc服务调用方使用的最主要的类了。

```C++
// 所有通过stub代理对象调用的rpc方法，最终都走到这里了，
// 统一做rpc方法调用的数据数据序列化和网络发送 
```

还有就是服务的调用方在使用我们框架的时候，我们的框架不应该输出错误信息，而是通过controller把调用的情况返回。所以channel与controller是调用者所要关注的两个类。参见`MpRpcController`



**使用**      

直接通过stub类传入一个channel来实现rpc调用。channel里统一做rpc方法调用的数据数据序列化和网络发送 。

```C++
int main(){
    // 必须先实现这个channel
    // UserServiceRpc_Stub stub(new protobuf::RpcChannel());
    UserServiceRpc_Stub stub(new MpRpcChannel());
    LoginRequest request;
    request.set_name("qzy");
    request.set_pwd("1234");
    LoginResponse response;
    // 本次rpc调用的相关控制信息，应该通过controller返回，而不是给用户输出一个框架错误信息，
    // controlled也是纯基类
    // 因为我们的框架会调用controller的一些方法，所以不能为nullptr
    stub.Login(nullptr,&request,&response,nullptr);
}
```





## 本地服务发布为rpc服务

参见example部分代码。

目前完成了rpc框架的服务发布与调用，可以先写一些服务进行验证了。也就是在框架的帮助下把本地服务发布为rpc服务：

* 定义服务的proto文件
* 服务发布方override一下相应的service类的方法，也就是本地业务实现

rpc框架就是一个强大的网络通信框架。看零声那一篇文章。

**发布方**   

定义的proto文件里面既有message也有service类型的数据，服务的发布方使用框架将`LoginServiceRpc`类及其方法进行存储（在protobuf下这个rpc类继承自service类，通过一些描述符，比如服务描述符和方法描述符来区分服务）。 

发布这个方法后通过服务注册中心将这个rpcServer的ip+port写入注册中心，这样服务的调用方就不用使用直连。   

```C++
// 通过protobuf定义rpc方法之后，进行override一些虚函数即可
class UserService:public UserServiceRpc{
    //override一些在proto里面定义的rpc方法，方法的参数自己看protobuf生产的文件
};

int main(int argc, char **argv) {
    string ip = argv[1];
    uint16_t port = atoi(argv[2]);
    InetAddress listenAddr(ip, port);
    EventLoop loop;

    RpcProvider provider(&loop, listenAddr, "UserService");
    provider.NotifyService(new UserService{});

    provider.Run();
}
```





**调用方**

rpc服务的调用者，仅仅需要使用`LoginServiceRpc_Stub`类进行rpc方法的调用，这个stub需要一个channel参数。调用stub类的方法就会转而调用channel的方法。  

这个channel是我们框架的提供者写的，重新protobuf提供的纯基类。channel里面主要就是负责从服务注册中心获取配置信息，然后进行序列化+发送，然后获取调用结果并返回。

```C++
    // 演示调用远程发布的rpc方法Login,
    UserServiceRpc_Stub stub(new MprpcChannel());
    // rpc方法的请求参数,message类型
    LoginRequest request;
    request.set_name("zhang san");
    request.set_pwd("123456");
    // rpc方法的响应
    fixbug::LoginResponse response;
    // 发起rpc方法的调用  同步的rpc调用过程  MprpcChannel::callmethod
    stub.Login(nullptr, &request, &response, nullptr); 
	// RpcChannel->RpcChannel::callMethod 集中来做所有rpc方法调用的参数序列化和网络发送
```



目前服务发布与调用功能已经完善，就差服务的注册与发现。



## 服务的注册与发现-DNS

> 采用zookeeper作为服务的配置中心，这里使用的比较简单就是一个类似于文件服务器。没有考虑分布式这种情况下的 republican，partition tolerance。 分布式系统需要接着进行学习，用来入门分布式不错。

**rpc服务的调用方调用方法的时候，如何知道rpc服务节点的配置信息（ip+port)来进行socket连接？**      

服务的发现问题，调用方的网络传输以及序列化都是在channel里面，所以调用方的每次rpc调用都要与zk进行一次连接建立。

具体体现就是在MpRpcChannel里面如何获取rpc节点配置信息。

**rpc服务的发布方，rpc节点运行后将自己的信息保证在哪可以让调用方看见**       

服务的注册问题，provider发布完所有的服务之后，在run里面将信息注册进配置中心。主要看的run方法。



服务的注册与发现写好了接下来就是写入代码。

**存在的问题**

目前有一个问题就是zk客户端的使用，目前服务的调用方每次进行rpc调用都需要在channel里面进行zk的连接，这个是比较耗时的，想想办法改进。服务的发布方倒是没什么问题，只有在Run的时候才会进行连接zookeeper创建相关的znode。



**下面是一些思考：**

*rpc框架本身所具有一些分布式的难题，这里暂时不考虑仅仅使用一个中间件作为服务的注册-发现中心。*

有关分布式一些经典问题，还在学习中！



项目需求需要一个文件服务器，也是使用一个服务器中间件，主要想完成就是找一个**中间人**来保存一下rpc节点的服务信息，比如某个rpc节点上发布了哪些服务。

如果不使用的话就得预先告诉rpc调用方所有的服务信息，不方便也不现实。

感觉想要的就是一个文件服务器（其实使用redis就足够，但是得自己开发）。

我们这是分布式的网络通信框架，哪里体现分布式了？项目名字是分布式rpc通信框架。==>这只是一个rpc通信框架，只不过是有了服务注册与发现功能。

> RPC通信框架本身并不一定是分布式的，它可以在单机上实现本地过程调用，也可以通过网络在分布式系统中进行远程过程调用。
>
> 在分布式系统中，RPC通常是用于在不同的计算机节点之间进行通信，以便节点之间能够互相调用对方的函数或方法。通过RPC通信框架，远程节点可以像本地函数一样调用远程函数，而无需了解底层的网络通信细节。
>
> 因此，RPC通信框架在分布式系统中非常常见，它可以帮助开发者轻松地实现跨节点的函数调用，提高了分布式系统的可扩展性和灵活性。

**我有一个rpc框架，那么是不是加上服务的注册与发现就可以称之为分布式的rpc框架**

可见这个项目不能称之为分布式，顶多就是一个rpc框架，但是自己可以加上服务注册与发现相关的代码。因为分布式的东西太多了，你说分布式肯定会问很多，可用性，一致性，容灾。

> 服务注册与发现是构建分布式系统中重要的组件之一。但是，只有实现了服务注册与发现并不能使一个RPC框架成为分布式RPC框架。因此，你需要考虑以下问题：
>
> 1. 网络通信: 在一个分布式系统中，不同的服务需要通过网络进行通信，因此需要实现网络通信协议和机制。
> 2. 可靠性：分布式系统中有许多故障的可能性，因此需要实现可靠性机制，例如重试、故障转移、容错等。
> 3. 分布式事务：如果你的RPC框架需要支持跨多个服务的事务，那么需要实现分布式事务管理机制。
>
> 如果你的RPC框架满足上述要求，包括服务注册与发现，网络通信、可靠性、分布式事务等，则可以称之为分布式的RPC框架。

**一个rpc网络通信框架，就算是有了服务的注册与发现，依然不能称之为分布式的rpc框架，因为他没有解决分布式场景下遇到的一些问题，我的理解对吗？**       

> 您的理解是正确的。即使有了服务的注册与发现，一个RPC网络通信框架仍然不能被称为分布式的RPC框架，因为在分布式场景下，仍然需要解决许多问题，例如负载均衡、故障转移、跨数据中心的通信、一致性等等。只有在解决了这些问题之后，RPC框架才能真正称为分布式的RPC框架。

**所以我要是有这样一个项目写进简历，最好不要说是分布式的**

> 如果您的项目确实涉及到分布式的场景，那么您可以在简历中写明这一点。但是，需要注意的是，面试官可能会进一步询问您在项目中是如何解决分布式场景下的问题的。因此，您需要确保您在项目中真正理解并解决了这些问题，并能够清晰地表达出来。如果您没有解决这些问题，或者解决方式比较简单，那么最好不要将其作为亮点在简历中突出。
>
>   
>
> self：其实rpc框架本身就具有一些分布式的感觉。







## 中间件

**zookeeper服务注册与发现：**       

使用Zookeeper作为服务的注册与发现是RPC框架中的一种常见做法，它可以实现服务的动态发现和自动化的负载均衡。下面是一个简单的步骤来实现这一过程：

1. 安装Zookeeper：首先需要安装Zookeeper，并在本地或远程服务器上运行Zookeeper。
2. 注册服务：当一个服务启动时，它应该向Zookeeper注册自己的地址。这可以通过创建一个Znode来实现，Znode是Zookeeper中的一种数据结构，用于存储数据和元数据。每个服务都应该创建一个Znode，其中包含服务的名称、IP地址和端口号等信息。
3. 发现服务：客户端需要从Zookeeper获取服务的地址，这可以通过查询Znode来实现。客户端可以订阅一个特定的Znode路径，以获取该路径下所有子节点的变更通知。当服务启动或停止时，它会创建或删除相应的Znode，客户端会收到相应的通知，从而实现服务的动态发现。（订阅这个功能目前没有使用，还是每次查询）
4. **负载均衡**：当多个服务提供相同的服务时（同一个服务进行水平集群拓展），客户端需要选择其中一个服务来处理请求。这可以通过实现负载均衡算法来实现，例如轮询、随机选择或基于权重的负载均衡算法等。（这个怎么实现？配置zookeeper？）==》ans客户端做负载均衡，而不是想集群服务器那里使用nginx

需要注意的是，使用Zookeeper作为服务的注册与发现需要考虑Zookeeper的可用性和性能问题。如果Zookeeper出现故障或网络延迟，可能会导致服务注册和发现出现问题。因此，建议在生产环境中使用多个Zookeeper节点进行集群部署，以提高可用性和性能。（这多个zookeeper不也是分布式场景吗?）**防止单点故障**

集群与分布式本就界限不清楚。

*这个负载均衡是zookeeper帮我们做的吗？*

> **通常有两种负载均衡的实现方式：客户端和服务端**
>
> 在使用ZooKeeper作为服务注册和发现的工具时，ZooKeeper本身并不提供负载均衡的功能。但是，通过在客户端实现特定的负载均衡算法，可以将客户端请求分配到不同的服务节点上，实现负载均衡。
>
> 一般情况下，客户端会从ZooKeeper节点上获取到所有可用的服务节点的列表，并根据特定的负载均衡算法选择一个服务节点来处理请求。当服务节点出现故障或不可用时，客户端会从ZooKeeper节点上获取最新的服务节点列表，并根据负载均衡算法选择新的服务节点。
>
> 具体来说，在使用ZooKeeper实现负载均衡时，一般需要在客户端实现以下几个步骤：
>
> 1. 在客户端启动时，从ZooKeeper节点上获取所有可用的服务节点列表。
> 2. 根据特定的负载均衡算法选择一个服务节点来处理请求，例如轮询、随机等算法。
> 3. 监听ZooKeeper节点的变化，当服务节点列表发生变化时，及时更新客户端的服务节点列表。
> 4. 当服务节点出现故障或不可用时，根据负载均衡算法选择新的服务节点来处理请求。
>
> 通过以上步骤，客户端就可以实现基本的负载均衡功能。当然，在实际应用中，还需要考虑更多的因素，例如节点的负载情况、网络延迟等因素，来实现更加高效和可靠的负载均衡算法。

**我感觉这里的时候和采用一个文件服务器差不多，感觉使用redis同样可以**   

是的，使用Redis作为服务的注册与发现也是RPC框架中的另一种常见做法。与Zookeeper类似，Redis可以实现服务的动态发现和自动化的负载均衡。下面是一个简单的步骤来实现这一过程：

1. 安装Redis：首先需要安装Redis，并在本地或远程服务器上运行Redis。
2. 注册服务：当一个服务启动时，它应该向Redis注册自己的地址。这可以通过将服务的地址存储为Redis中的一个键值对来实现，其中键表示服务的名称，值表示服务的地址。例如，可以将键为“service-A”、值为“127.0.0.1:8000”的键值对存储在Redis中。
3. 发现服务：客户端需要从Redis获取服务的地址，这可以通过查询Redis中存储的键值对来实现。客户端可以使用Redis的GET命令获取特定键的值，以获取服务的地址。客户端也可以使用Redis的KEYS命令获取所有的键，然后根据键值对获取服务的地址。
4. 负载均衡：当多个服务提供相同的服务时，客户端需要选择其中一个服务来处理请求。这可以通过实现负载均衡算法来实现，例如轮询、随机选择或基于权重的负载均衡算法等。

需要注意的是，使用Redis作为服务的注册与发现也需要考虑Redis的可用性和性能问题。如果Redis出现故障或网络延迟，可能会导致服务注册和发现出现问题。因此，建议在生产环境中使用多个Redis节点进行集群部署，以提高可用性和性能。

**既然redis也可以那么更推荐哪个**        

对于选择Zookeeper还是Redis作为RPC框架的服务注册与发现组件，其实并没有绝对的优劣之分，选择哪种技术主要取决于实际的业务需求和技术架构。

Zookeeper是一个专门用于分布式协调和服务注册的工具，它具有强大的一致性和可靠性，并且支持复杂的分布式场景。Zookeeper提供的Watcher机制可以让客户端实时感知服务的变化，能够更精准地实现服务的动态发现和负载均衡。但是，使用Zookeeper需要额外部署一个Zookeeper集群，管理和维护Zookeeper也需要一定的成本和学习成本。

Redis是一种高性能的缓存和数据存储工具，它可以很方便地存储和查询服务的地址信息，并且支持各种复杂的数据结构和操作。Redis还可以使用主从复制和哨兵机制提高可用性和可靠性。但是，相比于Zookeeper，Redis可能不如Zookeeper在分布式场景下的复杂度和灵活性。

综上所述，选择使用哪种技术主要取决于具体的业务需求和技术架构。如果需要支持复杂的分布式场景，并且有足够的资源和技术团队来管理和维护Zookeeper集群，那么Zookeeper可能更适合。如果只需要简单地实现服务的注册与发现，并且需要一个高性能的数据存储工具来支持其他功能，那么Redis可能更适合。

**可能我的场景使用redis就足够了，比如就采用一个节点作为配置中心**   

是的，如果你的场景比较简单，只需要一个轻量级的服务注册与发现组件，并且已经在项目中使用了Redis作为数据存储和缓存工具，那么使用Redis作为服务注册与发现的解决方案可能更为合适。你可以将Redis作为单点配置中心，存储服务的地址信息，并通过查询Redis的方式来实现服务的发现和负载均衡。

需要注意的是，使用单点Redis作为服务注册与发现组件，需要考虑Redis的可用性和故障恢复机制。当Redis节点出现故障或网络中断时，服务注册与发现会受到影响，导致服务不可用。因此，建议在生产环境中使用Redis集群部署，以提高可用性和性能。此外，你也可以考虑使用Redis Sentinel或Redis Cluster等Redis的高可用性解决方案来提高Redis的可用性和稳定性。

**这些中间件都很类似，只不过各有自己的优势，可以作为成熟的解决方案**      

在选择服务器中间件时，需要结合具体的业务场景和技术架构来进行选择。如果业务场景比较简单，可以选择较为轻量级的服务器中间件，如Redis或Etcd；如果业务场景比较复杂，需要支持复杂的分布式场景和服务治理需求，可以选择像Zookeeper、Nacos或Consul这样的服务器中间件。

Redis和Zookeeper都可以用于服务的注册与发现，而且它们的实现方式很相似，可以通过增加代码实现它们之间的相似性。在实际应用中，我们可以根据具体业务场景和技术需求，选择使用Redis或者Zookeeper来实现服务的注册与发现。

如果你已经在使用Redis，你可以通过在Redis中存储服务的地址信息，并在服务启动和停止时更新Redis中的信息，实现服务的注册与发现。通过使用Redis的发布订阅机制，可以实现服务节点的动态更新和服务的负载均衡。类似地，如果你选择使用Zookeeper，你可以使用Zookeeper提供的API来实现服务的注册与发现，并通过Zookeeper提供的Watcher机制来监测服务的状态变化，实现服务节点的动态更新和服务的负载均衡。

总之，无论选择Redis还是Zookeeper作为服务注册与发现的组件，都需要根据具体的业务需求和技术架构进行选择和实现。







## protobuf与反射

为什么感觉protobuf可以用来作为rpc的序列化主要是可以进行反射？也就是获取服务及方法的名字。



**Q1：如果语言本身支持反射的化，那么不借助框架也能很容易写rpc服务吧**

>如果编程语言本身支持反射（reflection），那么在不借助框架的情况下编写 RPC 服务是可行的，但这并不意味着这是一件容易的事情。
>
>反射是一种在运行时检查和修改程序结构和行为的能力。使用反射，可以在运行时获取类型信息、操作对象的属性和方法，以及动态创建新的对象实例。这使得编写灵活且可扩展的代码变得更加容易。
>
>要编写 RPC 服务，您需要定义一组接口和方法，这些方法可以被客户端调用。使用反射，您可以动态地扫描代码中的函数和方法，并根据其名称、参数类型和返回类型创建对应的 RPC 方法。一旦您完成了这些步骤，就可以将这些方法发布到网络上，以供远程客户端调用。
>
>尽管使用反射可以使编写 RPC 服务变得更加容易，但这并不是一项轻松的任务。您需要考虑安全性、错误处理、网络传输和序列化等问题，以确保您的 RPC 服务能够正常工作。因此，使用 RPC 框架仍然是一个更好的选择，因为这些框架通常已经解决了这些问题，并提供了可靠的、经过测试的解决方案。

**Q2: C++使用protobuf实现的rpc，主要是由于protobuf实现了类似反射的机制吗**

> 虽然 Protobuf 与反射（reflection）有一些相似之处，但是 C++ 使用 Protobuf 实现 RPC 的主要原因并不是因为 Protobuf 实现了类似反射的机制。
>
> 在 C++ 中使用 Protobuf 实现 RPC 的主要原因是 Protobuf 提供了一种轻量级、高效的序列化和反序列化机制，使得 C++ 程序员可以方便地将结构化的数据编码为二进制格式，以进行网络传输或数据存储。而 RPC 则是一种远程过程调用的协议，允许程序员通过网络调用远程服务器上的函数或方法，以实现分布式系统中的各种功能。
>
> 因此，C++ 使用 Protobuf 实现 RPC 的过程主要包括以下步骤：
>
> 1. 定义服务接口：使用 .proto 文件定义服务接口，包括服务的名称、方法和消息等信息。
> 2. 编写服务实现代码：在服务器端实现服务接口定义中的方法。
> 3. 编译 .proto 文件：通过 Protobuf 编译器生成客户端和服务器端的代码。
> 4. 启动 RPC 服务器：使用 RPC 框架启动 RPC 服务器，将服务注册到框架中。
> 5. 调用远程方法：在客户端中使用生成的代码调用远程方法，向服务器发送请求并接收响应。
>
> 在这个过程中，Protobuf 提供了序列化和反序列化的能力，以帮助程序员将消息编码为二进制格式，并在网络上传输。而 RPC 框架则提供了通信协议、服务注册、负载均衡、错误处理等功能，使得程序员可以方便地实现 RPC 服务。因此，C++ 使用 Protobuf 实现 RPC 的主要原因是它提供了一个快速、简单和可扩展的方法来实现分布式系统中的远程过程调用。



## 分布式相关

**如何系统学习分布式系统**     

Learning distributed systems is a complex and multifaceted task that requires a good understanding of various topics in computer science, including computer networks, operating systems, algorithms, and database systems. Here are some general steps you can follow to systematically learn distributed systems:

1. Get a good foundation in computer science fundamentals: Before diving into distributed systems, you should have a solid understanding of computer science basics such as data structures, algorithms, computer networks, and operating systems. You can start by taking courses or reading books on these topics.计算机基础的学习。
2. Learn the basics of distributed systems: Once you have a good foundation in computer science, you can start learning about distributed systems. This includes understanding the challenges and benefits of distributed systems, the characteristics of distributed systems, and the various architectural models used in distributed systems.入门分布式
3. Study distributed algorithms: Distributed algorithms are the building blocks of distributed systems. It's essential to learn about distributed algorithms, including consensus algorithms, distributed locking algorithms, and distributed computing algorithms.研究算法
4. Learn about distributed systems infrastructure: Distributed systems rely on various infrastructure components, including distributed file systems, distributed databases, messaging systems, and load balancers. It's crucial to have a good understanding of these infrastructure components and how they work.学习一些分布式基架
5. Get hands-on experience: Finally, it's essential to get hands-on experience with distributed systems. You can start by building simple distributed systems and gradually working your way up to more complex systems. You can also participate in open-source projects related to distributed systems or attend hackathons or other events focused on distributed systems.亲自体验

**the way to learn**       

1. Start with the basics: Begin by understanding the fundamental concepts of distributed systems, including their benefits and challenges, the architectural models used, and the characteristics that make them different from centralized systems. You can use online resources such as blogs, videos, and online courses to learn these basics. 
2. Learn distributed algorithms: Distributed algorithms are the building blocks of distributed systems, and it's essential to understand how they work. Focus on algorithms such as consensus, replication, consistency, and failure handling. You can use textbooks or research papers to learn more about these algorithms.
3. Study distributed systems infrastructure: Distributed systems rely on various infrastructure components such as databases, messaging systems, and file systems. Learn about these components and their role in distributed systems. Try to gain practical experience with popular systems like Apache Kafka, Redis, or Cassandra.
4. Build and experiment with distributed systems: After gaining a solid understanding of distributed systems' concepts and infrastructure, start building small distributed systems. You can try building a distributed key-value store, a simple message queue, or a distributed web crawler. This will give you hands-on experience with designing, developing, and testing distributed systems.
5. Participate in online communities and events: Join online communities like Reddit, StackExchange, and Twitter to stay up-to-date with the latest developments in the distributed systems community. Attend conferences, meetups, and hackathons focused on distributed systems to network with experts and gain new insights.
6. Practice, practice, practice: The best way to learn distributed systems is by building, experimenting, and practicing. Keep experimenting with different designs and architectures, try out new tools, and explore new algorithms to gain deeper insights into distributed systems.



## zookeeper使用

目前一些中间件的使用确实不熟悉，比如redis/nginx/zookeeper

zookeeper也是一个CS结构的先安装server，然后下载相应语言的SDK进行客户端的开发。

~~安装了jdk，zookeeper。 不如直接源码编译。没法通过apt安装？~~

查看已安装的包和软件。  `apt list --installed`     

删除一个包/软件`apt remove <package-name> `

这种还是自己从源码安装比较好，一来熟悉源码安装，而且自己也知道安装在了`/usr/local/`，其次这些软件需要进行配置。==》自己慢慢配环境！zookeeper是Java写的，需要jdk环境。这个不是安装在/usr/local，Java包这个不是C/C++那样编译运行的。

```bash
tar -zxvf xxx.tar.gz #解压 
# zk的两个文件夹 conf和bin
# 先进入conf进行配置， mv simple.conf zoo。conf，进入zoo.conf进行配置
vim zoo.conf
# 主要修改dataDir和clientPort两项，dataDir就是存放znode节点的地方
dataDir=/home/ubuntu/package/。。。/data

# 配置完成后进入bin通过zkServer.sh脚本进行启动即可，zookeeper是Java开发的，下载的就是Java包
# 不需要进行编译，安装；直接使用脚本启动。
```

zookeeper的访问每一次都必须从根部znode开始，`ls /`显示根部的节点。

每个znode节点都可以存放数据，并且可以作为子节点的父节点，可以说与普通文件系统的区别就是znode可以存放文件夹，也可以存放文件。

将每一个/RpcService/Method作为一个节点来存放配置信息,也可以一个Service节点的所有方法放在一个znode。

每一种server的SDK客户端开发都一样，就是建立连接将要执行的命令发送出去即可。

采用的也就是`get set create delete ls`



**SDK的安装**     

可以从源码编译安装。

```bash
./config
make
make install
```

也可以先看看可不可以通过apt安装。`apt show libzookeeper*`一般这个链接库就是lib开头命名的。

```bash
apt show libzookeeper*
apt-get install libzookeeper-mt-dev #
```

## 注释与可维护

使用@和\ 都可以，brief是对这个函数的简要概括，param是对参数的注释。return是对返回值的描述

```C++
/**
 * \brief 使用快排对数组进行排序。
 * 
 * be,ed分别代表区间的左右端点，这两处下标是可取的。
 * \param nums 待排序的数组
 * 
 * \param be nums的左端点，可取到
 * 
 * @param ed nums的右端点，可取到，使用/或艾特 都行
 * 
 * \return void no values
*/
```

## json

目前采用的使用head only那个，忘了咋用了。

## 项目简历输出

写好了，如何输出到简历？



除了服务注册中心，基本写好了输出到简历。

我用C++写了一个rpc通信框架，采用的是muduo网络库作为网络模块，protobuf作为序列化。刚我写一个项目介绍，有哪里不清楚的问我别瞎写。 不是很好写



RPC通信框架myRpc，
