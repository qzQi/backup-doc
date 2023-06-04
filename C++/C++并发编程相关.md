## C++并发编程相关

### 多线程编程的入门

使用thread，不再使用封装的pthread，C下的pthread经常使用的也就是，mutex，condition_variety来完成线程间的同步（当然封装C能更好理解、、、）           

经过对C线程的





**目的**：初步掌握C++线程的常用基本方法，对于没见过的日后补充。

* std::thread
* std::mutex
* std::lock
* std::atomic
* std::call_once
* std::condition_variable
* std::futex(日后补充)
* async （日后补充）

#### 线程的创建

创建线程的方法，线程参数的传递，线程的返回值？

配合lambda的使用。

需要注意的就是创建的线程必须使用join或者deatch，当然我们可以使用RAII来避免（意义不是太大）

```C++
class scoped_thread
{
private:
    thread thread_;

public:
// 使用万能转发的构造函数
    template<typename...Args>
    scoped_thread(Args&&...args)
        :thread_(std::forward<Args>(args)...)
    {}
    // 右值引用还是 ‘变量’
    scoped_thread(scoped_thread&& other)
        :thread_(std::move(other))
    {}
    ~scoped_thread()
    {
        if(thread_.joinable()){
            thread_.join();
        }
    }
    // 赋值运算符的话看一下thread的重载=吧
};
```



```C++
class ThreadGuard{
public:
    enum class Thread_action{join,detach};

// 这里应该使用一个万能转发的，但是move更简单，，th是一个左值
// 一个thread对象就可以构造
    ThreadGuard(std::thread&& th,Thread_action a=Thread_action::join)
        :th_(std::move(th)),action_(a){

        }
    ~ThreadGuard(){
        if(th_.joinable()){
            if(action_==Thread_action::join)th_.join();
            else th_.detach();
        }
    }

    ThreadGuard(ThreadGuard&&)=default;
    ThreadGuard& operator=(ThreadGuard&&)=default;
    //这两个函数太粗糙了吧,=不能为默认，至少得把原来的取消
    // 根据规则，拷贝构造默认直接为delete
    /*模拟thread
    thread& operator=(const thread&) = delete;
    thread& operator=(thread&& __t) noexcept
    {
      if (joinable())
        std::terminate();
      swap(__t);
      return *this;
    }
    */

    
private:
    std::thread th_;
    Thread_action action_;
};
int main()
{
    ThreadGuard=std::thread([](){cout<<"thread"<<endl;});
    thread thd_1=std::thread([](){cout<<"thread"<<endl;});;
    ThreadGuard=std::move(thd_1);
}
```



```C++
//线程休眠chrono，this_thread命名空间下的一些东西。
std::this_thread::sleep_for(std::chrono::seconds(1));
//提供返回原生句柄的操作，目前不知道有啥用
auto handle = t.native_handle();// handle可用于pthread相关操作
//本线程相关的
thread::id this_thread::get_id();
```

#### this_thread相关    

管理当前的线程的函数，位于`this_thread`命名空间。

这个就类似于我们封装的`CurrentThread`         

```C++
	using namespace this_thread;//not recommand
    cout<<this_thread::get_id()<<endl;
    this_thread::sleep_for(chrono::seconds(3));
    // 时间处理也很好

    this_thread::yield();
    // 主动让出处理器
```





#### mutex的使用

定义于mutex

* std::mutex
* lock_guard
* unique_lock

用的最多的就是mutex互斥锁，lock_guard就是简单的RAII封装，unique_lock则有更多的功能（一个完全RAII的mutex吧）

* lock_guard完全RAII，利用栈来的生命期来 保护临界区
* unique_lock经常配合condition一块使用

```C++
mutex output_guard;

void print()
{
    lock_guard<mutex> lock(output_guard);
    //unique_lock<mutex> lock(output_lock);同样是RAII封装
    cout<<"out not thread safety"<<endl;
}
```







#### atomic

这个主要就是使用的原子int。



#### 可调用对象的统一

**这部分得再写点，专门开一个文件。**           

* closure
* lambda的实现

> **在计算机科学中，闭包（英语：Closure），又称词法闭包（Lexical Closure）或函数闭包（function closures），是引用了自由变量的函数。 这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。**
>
> 保持的应该也是形成闭包时的变量，今后这个变量的改变不会影响闭包。 之前真是瞎写，看看moderncpp

*通过使用function与bind来使可调用世界大一统。*effective里面说这个function不如lambda

可调用类型：

* call_once相关(这个多线程分类处的)
* function与bind，bind也叫做函数适配器
* lambda相关
* 仿函数，重载operator()的类
* 普通类的成员函数（指针）,通过function+bind转化下
* 类的静态成员函数

目前对类的普通成员函数如何确定它的函数类型，然后使用lambda来使用它？（bind+function）

直接可给function包装的：函数（指针），仿函数（函数对象），lambda。

需要bind的有：改变参数，类的普通函数成员（静态成员没有this）。







**lambda**

编译器会将lambda表达式自动转换为函数对象，捕获值就是类的data mem。

* 值捕获，引用捕获
* 函数体可为哪些东西

编译器与lambda。

`auto func=[capture](params)opt->ret{func_body;};`

其中func是可以当作lambda表达式的名字，作为一个函数使用，capture是捕获列表，params是参数表，opt是函数选项(mutable之类)， ret是返回值类型，func_body是函数体。

- []不捕获任何变量
- [&]引用捕获，捕获外部作用域所有变量，在函数体内当作引用使用
- [=]值捕获，捕获外部作用域所有变量，在函数内内有个副本使用
- [=, &a]值捕获外部作用域所有变量，按引用捕获a变量
- [a]只值捕获a变量，不捕获其它变量
- [this]捕获当前类中的this指针

lambda捕获的值默认是不可改变的，但是opt可以为mutable。

```C++
//lambda的可能实现，以及opt对应的mutable
struct test{
    mutable int num_;
    test(int n=0):num_(n){}
    void operator()(int i)const{
        cout<<(num_+=i)<<endl;
    }
};
//equal
auto func=[num_](int i)mutable->void{num+=i;cout<<num_;};
```







**function**

[link1_](https://www.codenong.com/cs110704565/)

[link2_](https://mp.weixin.qq.com/s?__biz=MzkyODE5NjU2Mw==&mid=2247484783&idx=1&sn=ddc57fd2753aa386ed1bcf0b7c755699&chksm=c21d37d3f56abec5a9b333d55afd541017fc8c41f74a2bb8009fa341d6a26685c6f08ff5d50c&scene=21#wechat_redirect)







介绍看一下primer。

>std::function就是可调用对象的封装器，可以把std::function看做一个函数对象，用于表示函数这个抽象概念。std::function的实例可以存储、复制和调用任何可调用对象，存储的可调用对象称为std::function的目标，若std::function不含目标，则称它为空，调用空的std::function的目标会抛出std::bad_function_call异常。

存储普通的函数对象，使用他们的调用形式作为函数模板参数就可以。

对于仿函数以及普通类的成员函数，有一个要明确，类的第一个实参其实是*this，这个在重载operator应该明确了吧。在类的内部使用我们默认了，但是拿出来使用要记得。

但是我们也可以把一个类的对象，绑定到也得成员函数（指针）

*函数与函数指针，这两个我就混用了啊，其实就是函数*

```C++

struct Foo{
    int num_;
    Foo(int num=0):num_(num){}
    void print_add(int i){
        cout<<num_+i<<"\n";
    }
};//测试成员函数

struct Print_num{
    void operator()(int i){
        cout<<i<<endl;
    }
};//测试函数对象

void print_num(int i){
    cout<<i<<endl;
}//plain func


int main()
{

    // void (int)
    // plain func
    function<void(int)>f_display{print_num};
    f_display(-1);

    // store lambda
    function<void()>f_display_42=[](){
        print_num(42);
    };
    f_display_42();

    // store bind ans
    function<void()>f_display_521=bind(print_num,521);
    f_display_521();

    // 访问成员函数
    // 为何不能使用，使用bind把对象绑定后就可以
    const Foo foo(520);
    /*这不知道为啥但是这个方法也没有bind好用。
    function<void(const Foo&,int)>f_add_disp=&Foo::print_add;
    f_add_disp(foo,2);
    f_add_disp(521,-1);//期望生成一个临时对象
    */
//    不使用上面的那个，使用bind的结果
   using std::placeholders::_1;
   function<void(int)>f_add_disp_2=bind(&Foo::print_add,foo,_1);
   f_add_disp_2(1);

    // dont know the use 
    std::function<int(Foo const&)> f_num = &Foo::num_;
    std::cout << "num_: " << f_num(foo) << '\n';

    // 仿函数,不需要bind
    function<void(int)>f_display_obj=Print_num();
    f_display_obj(22222);
    //静态成员测试，这个很好写，不需要bind等这些
```





**bind**

使用std::bind可以将**可调用对象和参数**一起绑定，绑定后的结果使用std::function进行保存，并延迟调用到任何我们需要的时候。

可调用对象可以是我们上面说的任何形式，对于类（lambda不算），要记得使用一个对象作为参数。

bind的常用技法：

- 将可调用对象与参数一起绑定为另一个std::function供调用
- 将n元可调用对象转成m(m < n)元可调用对象，绑定一部分参数，这里需要使用std::placeholders

`using namespace std::placeholders;`使用所有的占位符。

这里一个要注意的就是bind的参数默认是值，想一些不能拷贝的比如io流我们必须使用ref或者cref来使用他们的引用形式。

`auto newcallable=bind(callable,arg_list)`

占位符的使用，_1就表示newcallable的第一个参数,  _2就表示newcallable的第二个参数。

```C++
auto g=bind(f,a,_1,b,_2,c);
//那么我们调用g的过程就是g(_1,_2);传给g的参数，
//对f来说，它的第1、3、5这三个参数被绑定到了a，b，c这三个数，
//g(1,2)---> f(a,1,b,2,c);
```

*还有一个使用bind重排参数顺序，我不知道用这个干吗。*









#### 条件变量







~~cpp时代在Linux编译还需要 -lpthread选项？~~

编译选项`-pthread`.





为什么需要条件变量？mutex仅仅可以保证我们的互斥访问，我们需要一种机制来完成线程间的同步（通知）。条件变量最基本的功能：`signal_one()\all()`&&`wait()`.





在C语言里面mutex就足够了，c++里面需要使用RAII的是unique_lock。





最基本的形式：

```C
// thread1:
pthread_mutex_lock(&lock);
while(ready)
    pthread_cond_wait(&cond,&lock);
pthread_mutex_unlock(&lock);

// thread 2
pthread_mutex_lock(&lock);
ready=1;
pthread_cond_signal(&cond);
pthread_mutex_unlock(&lock);
```

可能迷惑的地方：

* wait为什么需要一个mutex锁作为参数？为什么signal仅仅需要一个？
* 为什么使用while作为，检查条件？

*reason*：

* wait除了会是进程进入睡眠状态外，还需在睡眠的时候释放锁（unlock）来让其他进程来加锁（比如signal进程），在被signal唤醒后，wait还需要对线程进行加锁（来对应后面的unlock）。
* 使用while，是为了防止睡眠线程被意外的唤醒。

so，在我们的c++里面我们应该使用unique_lock。

```C++
//c++的wait操作，有两种，一种是在wait指定谓词
void wait(std::unique<std::mutex>&lock_);
//这种方式我们一般直接指定while的等待条件

template<class Predicate>
void wait(unique_lock<mutex>&lock_,Predicate pred);
//equal
while(!pred())
{
    wait(lock_);
}
```







#### call_once

保证仅仅被调用一次。

```C++
once_flag flag1;
mutex guard_out;

void thread_func()
{
    cout<<"only call once"<<endl;
    cout<<this_thread::get_id()<<endl;
    this_thread::sleep_for(chrono::seconds(1));
    // 时间处理也很好

    this_thread::yield();
    // 主动让出处理器
}

// 如果不使用指针或者引用是失效的对吧。
void do_once(once_flag *flag)
{
    call_once(*flag,thread_func);
    lock_guard<mutex> lok(guard_out);
    cout<<this_thread::get_id()<<"every thread executate here"<<endl;
}

void join_all(vector<unique_ptr<thread>>&n){
    for(auto &i:n)i->join();
}

// once_flag:一个只能被执行一次
void do_once_tets(once_flag *flag)
{
    call_once(*flag,do_once,flag);//傻逼才会这么写吧
}

int main()
{
    cout<<thread::hardware_concurrency()<<endl;
    vector<unique_ptr<thread>>threads;
    threads.resize(5);
    for(auto& i:threads)
    {
        i=make_unique<thread>(do_once,&flag1);
        // 不应该在这里join，不join也会执行
    }

    join_all(threads);

    thread test_th(do_once_tets,&flag1);
    test_th.join();
}
```



#### < future >

**async与thread：**          

[stackoverflow](https://stackoverflow.com/questions/34680985/what-is-the-difference-between-asynchronous-programming-and-multithreading)            

[a batter doc](https://www.baeldung.com/cs/async-vs-multi-threading)              



待完成、、、

区别不到，用的时候才有体验。



* 异步async

  异步：关于现在与将来的时间间隔。           

  



* 并行：

  同时发生的事情。



* 多线程

  为了发挥我们多核计算机的计算功能。对于计算量任务我们使用一个线程（processor）来处理。但是对于一个计算量少（不占用cpu而是需要读写）的任务，我们使用async比较合适。



| API           | 说明                                   |
| ------------- | -------------------------------------- |
| async         | 异步执行一个函数，并保留其结果的future |
| future        | 等待被异步设置的值                     |
| packaged_task | 打包一个函数，存储其结果以进行异步获取 |
| promise       | 存储一个值进行异步获取                 |
| shared_future | 等待被异步设置的值                     |



```C++
#include<future>
```











#### C++的时间函数

这里写下C++的时间吧？              

> https://paul.pub/cpp-date-time/



```C++
#include<sys/time.h>
struct timeval now;
gettimeofday(&now,NULL);

```



### 多线程练习

* 常用的*倒计时*门栓类封装（C++20连这个都提供？）
* 阻塞队列实现
* 线程池的实现
* 读写锁
* 生产者/消费者





*emmm，还没有实现。*      



### 内存模型

