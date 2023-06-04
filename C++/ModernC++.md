## 第一章

### 条款1 理解模板推导

第一章里面这一个比较难，仔细看。

```C++
const char* const ptr="hello world";
// *号右边的const是说明ptr的常量性，即ptr不能指向别的地方（无法赋值）
// *号左边的const是说明ptr所指向的对象的常量性（这里是字符串），也就是无法通过ptr修改所指向的对象
ptr=str2;//error ，违反*号右侧的常量
ptr[2]='a';//error，违反左侧的常量
```

阅读条款一，明白推导出来的参数T的类型，和ParamType的类型。主要是分为三类

* ParamType是指针/引用
* ParamType是一个万能引用
* ParamType不是指针也不是引用，传值

再一次调用`fun(expr);`里面编译器利用expr来推导T和ParamType，关键是理解这三种情况下推导出来的T类型和，param的类型。

```C++
template<typename T>
void fun(T& param);/ void fun(T* param);

template<typename T>
void fun(T&& param);

template<typename T>
void fun(T param);
```

### 条款2 理解auto

除了一个例外。auto推导出来的变量类型就是上一个/条款1中的模板参数T；所以auto一般也是三种用法

```C++
Foo foo;
auto a1=foo;
auto & a2=foo;
auto && a3=f00;
//一般也就是再使用const修饰一下

//C++14里面auto可以用于lambda的形参列表，作用和模板一样
auto func=
    [](auto && nums1,auto && nums2){return nums1.size()<nums2.size();};
```

例外就是列表初始化。

### 条款3 理解decltype

decltype几乎就是编译器的化身，推导出来的就是类型本身ParamType；当然有例外的情况，但几乎遇不到。

```C++
//返回一个容器在index的值，eg: nums[index]，
//不同的容器返回类型不一样，有的是引用eg，vector<int>；有的返回值eg:vector<bool>
// version1
template<typename Container,typename Index>
auto autoAndAccess(Container& c,Index i)->decltype(c[i]){
    return c[i];
}

//version2
template<typename Container,typename Index>
auto autoAndAccess(Container& c,Index i){
    return c[i];
}

//version3，不了解出这些语法糖干啥？第一版已经可以工作了
template<typename Container,typename Index>
decltype(auto) autoAndAccess(Container& c,Index i){
    return c[i];
}
//也许decltype(auto)可以完全不写返回值，当不知道返回值是什么东西的时候
decltype(auto) fun(int a){
    return a;
}
```



## 第二章auto

### 条款5 尽量使用auto



### 条款6 auto不适用的情况

条款2&6是一些auto不适用的情况。





## 第三章 现代cpp

### 条款17 理解三五法则

三五法则也就是之前的big three 和C++11之后的big five。理解何时会自动生成。//未看     

```C++
class Foo{
 public:
    Foo()=default;
    Foo(const Foo&);
    Foo(Foo&&);
    Foo& operator=(const Foo&);
    Foo& operator=(Foo&&);
    ~Foo();
};//big five

//使用copy-swap哲学可以使得operator=统一，变为big four
//仅仅需要写一个copy拷贝构造，swap操作，析构，而operator=和移动构造由swap完成
class Foo{
 public:
    Foo()=default;
    Foo(const Foo&);

    void swap(Foo &){
        //do swap
    }
    
    Foo(Foo&& f):Foo(){
        this->swap(f);
    }
    Foo& operator=(Foo foo){
        this->swap(foo);
    }
    //Foo& operator=(const Foo&);
    //Foo& operator=(Foo&&);
    ~Foo();
};//big four
```



## 智能指针

面试的时候问到这种modern cpp无疑是最好的答案。这就得多看几遍，多写了。      

读一遍不行，无法记录。  这里是一些细节，使用陈硕写得好。

### 条款18 unique

自定义的析构器会改变unique_ptr对象的大小，具有不同析构动作的可能无法互相移动（unique本就无法赋值）。

```C++
    unique_ptr<int> upint(new int(2),[](int *a){
        delete a;
    });//equal: unqiue_ptr<int> upint(new int(2),del);
    //默认动作位delete，具有不同析构动作的uptr无法转化
    unique_ptr<int> upint2(std::move(upint));

// unique-->shared ok
shared_ptr<int> spint(std::move(upint));
```



### 条款19 shared

shared_ptr对象的大小固定位两个指针，同类型的具有不同析构动作的也可以相互赋值/转化。

*make shared/unique*是无法传入一个析构动作的，只有使用reset/使用指针初始化时才会。      

```C++
class Foo{};
auto spfoo=make_shared<Foo>();//无法转入析构动作
shared_ptr<int> spfoo2(new Foo(),del);//ok
// 初始化/构造的时候尽量避免使用裸指针，当然使用new创建可以
```



### 条款20 使用weak_ptr 

* 不增加引用计数来查看对象是否存在
* 弱回调
* 避免循环引用



### 条款21 使用make而不是new

这个是为什么？ make固然适用，但是无法转入自定义析构。make使用时候会创建一个控制块，用于对一块资源的初次创建。        

```C++
template<typename T,typename... Ts>//万能引用与转发
std::unique<T> make_unique(Ts&&...params){
    return std::unique<T>(new T(std::forward<Ts>(params)...);
}//make实现原理，底层使用的是对象的（）构造，
auto upvecInt=make_unique<vector<int>>(10,20);//vector<int>(10,20);而不是初始化列表
```

make不适用的情况；       

1、自定义删除器      2、大括号初始化物，这个从上面原理可得，转发使用的（）       

自己觉得还是随便使用吧。     



### 条款22 Pimpl:Point to imple

将成员函数的定义放到实现文件。     

把某类的数据成员用一个指涉到某实现类的指针代替。然后把主类的数据放到实现类，之后通过指针间接访问这些数据成员。        这么简单的想法，但是把指针换成智能指针的话引出的一点问题。遇到了再说吧

但是不使用unique_ptr还需要自己管理资源big five。   

其实也就是减少头文件的依赖，不要在头文件里包含头文件特别是自定义的或者非常容易修改的；**而是去使用前置声明采用指针这样就不用包含头文件可以使用不完整类型**，将实现放到cpp文件里面。        









## 第五章 右值/移动/转发

右值引用，移动语义，完美转发。       

这一块挺多，有时间慢慢看吧，目前看的不多；看的反而有点迷糊。

* 理解move与forword

  move就是强制类型转换，forward往往要配合万能引用

* 区分右值引用与万能引用

  ```C++
  vector<int> a{1,2,3,4};
  vector<int> vb(std::move(a));//使用的是右值引用
  
  template<typename T>
  void func(T&& params){
      std::forward<T>(params);//传入的是什么类型的T就会转发什么
      //关键是理解模板实参推导，
      //对于万能引用推导出的T参数 1、传入引用/左值推导出引用  2、传入右值推导出右值引用
      //推导出的T经过forward转发，就变为了传入的实参类型也就是传入什么，转发什么
  }
  ```

  

* 右值引用搭配move，万能引用搭配forward

  右值引用与move；万能引用与完美转发。这是配套使用。



在看吧



## 第六章 lambda

当在类的成员函数内部构造lambda时候记的，成员函数的第一个参数是this，并且在成员函数内部lambda无法直接使用类的成员变量而是通过使用this指针，这也就暴漏了this。      lambda需要捕获的是局部的变量全局的当然可见。
