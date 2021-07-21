# 右值引用

### 左值&右值

定义1:

* 左值：可以放在等号左边的东西叫做左值。
* 右值：不可以放在等号左边的东西叫做右值。

定义2:

* 左值：可以提取地址并且有名字的东西叫做左值。
* 右值：不能提取地址且没有名字的东西叫做右值。

例如:

```cpp
int a = b+c;
//a是左值，有变量名，可以提取其地址，也可以放到等号左边
//表达式(b+c)的返回值是右值, 没有名字，&(b+c)不能提取地址，并且不能放到等号左边
```

> 左值一般有：
>
> * 函数名或者变量名
> * 返回左值引用的函数调用
> * 前置自增和自减表达式\(++i、--i\)
> * 由赋值表达式或者赋值运算符连接的表达式\(a = b, a+=b\)等
> * 解引用表达式\(\*ptr\)
> * 字面常量符\("hello world"\)等

### 纯右值&将亡值

纯右值和将亡值都属于右值。

#### 纯右值

运算表达式产生的临时变量、不和对象关联的原始字面量、非引用返回的临时变量、lambda表达式等都是纯右值。

例如：

* 除字符串字面值外的字面值
* 返回非引用类型的函数调用
* 后置自增自减表达式\(i++、i--）
* 算术表达式\(a+b, a\*b, a&&b, a==b等\)
* 取地址表达式等\(&a\)

#### 将亡值

将亡值是指C++11新增的和右值引用相关的表达式，通常指将要被移动的对象、T&&函数的返回值、std::move函数的返回值、转换为T&&类型转换函数的返回值，将亡值可以理解为即将要销毁的值，通过“盗取”其它变量内存空间方式获取的值，在确保其它变量不再被使用或者即将被销毁时，可以避免内存空间的释放和分配，延长变量值的生命周期，常用来完成移动构造或者移动赋值的特殊任务。

例如:

```cpp
class A {
    xxx;
};
A a;
auto c = std::move(a); // c是将亡值
auto d = static_cast<A&&>(a); // d是将亡值
```

### 左值引用&右值引用

* 左值引用就是对左值进行引用的类型
* 右值引用就是对右值进行引用的类型

他们都是引用，都是对象的一个别名，并不拥有所绑定对象的堆存，所以都必须立即初始化。

```cpp
type &name = exp; // 左值引用
type &&name = exp; // 右值引用
```

#### 左值引用

```cpp
int a = 5;
int &b = a; // b是左值引用
b = 4;
int &c = 10; // error，10无法取地址，无法进行引用
const int &d = 10; // ok，因为是常引用，引用常量数字，这个常量数字会存储在内存中，可以取地址
```

可以得出结论：对于左值引用，等号右边的值必须可以取地址，如果不能取地址，则会编译失败，或者可以使用const引用形式，但这样就只能通过引用来读取输出，不能修改数组，因为是常量引用。

#### 右值引用

如果使用右值引用，那表达式等号右边的值需要时右值，可以使用std::move函数强制把左值转换为右值。

```cpp
int a = 4;
int &&b = a; // error, a是左值
int &&c = std::move(a); // ok 使用std::move()强制转换为右值
```

### 移动语义

移动语义按照侯捷老师的说法就是**偷所有权**，例如深拷贝的本质是重新开辟一个与原来资源（数据）相同的内存，而对于移动语义，类似于转让或者资源窃取的意思，对于那块资源，转为自己所拥有，别人不再拥有也不会再使用，通过C++11新增的移动语义可以省去很多拷贝负担，怎么利用移动语义呢，是通过移动构造函数。

```cpp
class A{
public:
    A(int size): size_(size){
        data_ = new int[size];
    }

    //拷贝构造函数
    A(const A& ref){
        this->size_ = ref.size_;
        this->data_ = new int(ref.size_);
        std::cout << "copy" << std::endl;
    }
    
    //移动构造函数
    A(A&& ref){ //这里需要改变ref指针的指向所以不能是const
        this->size_ = ref.size_;
        this->data_ = ref.data_;
        ref.data_ = nullptr;
        std::cout << "move" << std::endl;
    }

    ~A(){
        if(data_ != nullptr){
            delete data_;
        }
    }

private:
    int size_;
    int data_;
};

int main(int argc, char *argv[])
{
    A a(10);
    A b = a; //copy
    A c = std::move(a); //move
    return 0;
}
```

> **注意：**移动语义仅针对于那些实现了移动构造函数的类的对象，对于那种基本类型int、float等没有任何优化作用，还是会拷贝，因为它们实现没有对应的移动构造函数。

### 完美转发

完美转发指可以写一个接受任意实参的函数模板，并转发到其它函数，目标函数会收到与转发函数完全相同的实参，转发函数实参是左值那目标函数实参也是左值，转发函数实参是右值那目标函数实参也是右值。那如何实现完美转发呢，答案是使用std::forward\(\)。

```cpp
void PrintV(int &v){
    std::cout << "lvalue\n";
}

void PrintV(int&& v){
    std::cout << "rvalue\n";
}


template<typename T>
void Test(T&& t){
    PrintV(t); //永远左值
    PrintV(std::forward<T>(t)); //完美转发
    PrintV(std::move(t)); //永远右值 
}

int main(int argc, char *argv[])
{
    Test(1); //lvalue rvalue rvalue
    
    int a = 1;
    Test(a); //lvalue lvalue rvalue
    Test(std::forward<int>(a)); //lvalue rvalue rvalue
    Test(std::forward<int&>(a)); //lvalue lvalue rvalue
    Test(std::forward<int&&>(a)); //lvalue rvalue rvalue
    
    return 0;
}

```

### Reference

* \[左值引用、右值引用、移动语义、完美转发，你知道的不知道的都在这里\]\([https://mp.weixin.qq.com/s/aCv7vIyrGyqu06QpNjZFTA](https://mp.weixin.qq.com/s/aCv7vIyrGyqu06QpNjZFTA)\)

