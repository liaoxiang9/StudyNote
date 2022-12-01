# C++面向对象程序设计——侯捷

## 杂论

### 目标

- 培养正规的、大气的编程习惯
- 以良好的方式编写C++ class
- 学习Classes之间的关系
  - 继承
  - 符合
  - 委托

### C++历史

- B语言（1970年）
- C语言（1972年）
- 1983年，Bjarne Stroustrup在AT&T贝尔实验室开始设计C++，并于1985年完成第一个版本。
- Java语言（1995年）是由Sun公司设计的一种面向对象的编程语言，它的设计目标是取代C++语言。
- C#语言（2000年）是由微软公司设计的一种面向对象的编程语言，它的设计目标是取代Java语言。

### C++ 演化

1. C++ 98
2. C++ 03
3. C++ 11
4. C++ 14
C++ = C++语言 + C++标准库

### C++ 与 C语言的区别

- C++是C语言的超集
- C++是面向对象的
- 将C语言的函数和数据封装成类
- class是C++的核心

## 头文件

### C++程序的代码基本形式

- 头文件 .h
  - 自己的头文件

    ```c++
    #include "complex.h"
    ```

  - 标准库头文件

    ```c++
    #include <iostream>
    ```

- 源文件 .cpp
- 标准库 .h

### C++头文件中的防御式声明

```c++
    #ifndef COMPLEX_H
    #define COMPLEX_H
    // 前置声明
    // 
    #endif
```

## Complex类的设计——不带指针的类

### C++模板

- 模板是泛型编程的基础
- 模板通过 template 关键字来定义
- 在类定义前加 ```template <typename T>```

```c++
template <typename T>
class complex
{
    T re, im;
public:
    complex(T r, T i) : re(r), im(i) {}
    complex(T r) : re(r), im(0) {}
    complex() : re(0), im(0) {}
    T real() const { return re; }
    void real(T r) { re = r; }
    T imag() const { return im; }
    void imag(T i) { im = i; }
};
```

### 内联函数

- 函数在类的内部定义，自动成为内联函数
- 函数在类的外部定义，需要在函数前加 inline 关键字
- inline关键字只是“建议”编译器将函数内联，编译器可以忽略这个建议

### 构造函数

- 构造函数是一种特殊的成员函数，它的名字与类名相同
- 构造函数没有返回值，也不能声明为void

```c++
complex(double r, double i) : re(r), im(i) {}
```

等价于（不完全相同）

```c++
complex(double r, double i)
{
    re = r;
    im = i;
}
```

上述两种写法的区别在于，前者的初始化列表（initializer list）可以避免一些不必要的拷贝操作。后者的写法，会先创建一个临时对象，然后再将临时对象赋值给类的成员变量（效率上会差一些）。

#### 构造函数的重载

- 重载的构造函数可以有不同的**参数列表**
- 构造函数有默认值时，不能够再重载一个无参构造函数

```c++
complex(double r = 0, double i = 0) : re(r), im(i) {}
complex() : re(0), im(0) {}
```

**上述两个构造函数不能同时存在，否则会报错。原因是，编译器无法区分 complex()该调用哪个构造函数。**

```c++
complex(double r) : re(r), im(0) {}
complex() : re(0), im(0) {}
```

上述两个构造函数可以同时存在。

#### 构造函数声明为private

**单例模式**中，将构造函数放在private中，可以防止用户创建对象。

```c++
class Singleton
{
private:
    Singleton() {}
    static Singleton* instance;
public:
    static Singleton* getInstance()
    {
        if (instance == nullptr)
            instance = new Singleton();
        return instance;
    }
};
```

### 常量成员函数

```c++
class complex
{
public:
    complex(double r = 0, double i = 0) : re(r), im(i) {}
    double real() const { return re; }
    double imag() const { return im; }
private:
    double re, im;
};
```

- **常量成员函数不能修改类的成员变量**
- 在常量成员函数中，可以调用其他的常量成员函数
- 应该将所有不改变对象状态的函数都声明为常量成员函数

举例

```c++
complex c1(2, 1);
cout << c1.real() << endl;
cout << c1.imag() << endl;
```

上述代码中，**c1不是一个常量对象，因此可以调用非常量成员函数，也可以调用常量成员函数。**

```c++

```c++
const complex c1(2, 1);
cout << c1.real() << endl;
cout << c1.imag() << endl;
```

上述代码中，**c1是一个常量对象，因此只能调用常量成员函数**。因为编译器认为调用非常量成员函数可能会修改对象的状态，这是不允许的。

### 引用（reference）

- 引用在底层就是指针
- 传引用的效率比传值要高
- **所有的参数传递尽量传引用**

### 参数传递

有两种方式可以传递参数：

1. 传值

```c++
void f(complex z)
{
    cout << z.real() << endl;
    cout << z.imag() << endl;
}
```

2. 传引用

```c++
void f(complex& z)
{
    cout << z.real() << endl;
    cout << z.imag() << endl;
}
```

```c++
void f(const complex& z)
{
    cout << z.real() << endl;
    cout << z.imag() << endl;
}
```

上述代码中，**z是一个复数对象的引用**。在函数f中，**z是一个常量引用**。因此，**z不能被修改**。

为什么参数传递要使用reference？

- reference传递参数的效率比value传递参数的效率高，原因是reference传递参数不需要复制参数，而value传递参数需要复制参数。

---

### 返回值传递

返回值传递有两种方式：

1. 返回值

```c++
class complex
{
public:
    complex(double r = 0, double i = 0) : re(r), im(i) {}
    // return by reference
    complex& operator+=(const complex&);
    double real() const { return re; }
    double imag() const { return im; }
private:
    double re, im;
    friend complex& __doapl(complex*, const complex&);
};
```

2. 返回引用

```c++
class complex
{
public:
    complex(double r = 0, double i = 0) : re(r), im(i) {}
    // return by reference
    complex& operator+=(const complex&);
    double real() const { return re; }
    double imag() const { return im; }
private:
    double re, im;
    friend complex& __doapl(complex*, const complex&);
};
```

- **如果函数不是需要返回内部定义的局部变量（一出函数就消亡），而是返回函数外界定义的对象（如该对象通过参数传递指针的方式进入函数），那么就可以返回对该对象的引用。**
- **传递者无需知道接收者是以reference还是value的方式接收的**。
- **返回值是complex&，是为了能够连续调用函数**。

### 友元

```c++
class complex
{
public:
    complex(double r = 0, double i = 0) : re(r), im(i) {}
    // return by reference
    complex& operator+=(const complex&);
    double real() const { return re; }
    double imag() const { return im; }
private:
    double re, im;
    // 友元函数
    friend complex& __doapl(complex*, const complex&);
};
inline complex&
__doapl(complex* ths, const complex& r)
{
    ths->re += r.re;
    ths->im += r.im;
    return *ths;
}
```

- **友元函数可以访问类的私有成员变量**
- 相同class的各个对象互为友元

```c++
class complex
{
public:
    complex(double r = 0, double i = 0) : re(r), im(i) {}
    int func(const complex& r)
    {
        return r.re + r.im;
    }
private:
    double re, im;
};
```

上述代码中，**func函数可以访问complex类的私有成员变量**。

### 操作符重载

**重载操作符的函数通过参数的个数和类型来区分不同的操作符。**

```c++
class complex
{
public:
    complex(double r = 0, double i = 0) : re(r), im(i) {}
    complex& operator+=(const complex&);
    double real() const { return re; }
    double imag() const { return im; }
private:
    double re, im;
    friend complex& __doapl(complex*, const complex&);
};
inline complex&
__doapl(complex* ths, const complex& r)
{
    ths->re += r.re;
    ths->im += r.im;
    return *ths;
}
inline complex&
complex::operator+=(const complex& r)
{
    return __doapl(this, r);
}
inline complex
operator+(const complex& x, const complex& y)
{
    return complex(real(x) + real(y), imag(x) + imag(y));
}
inline complex
operatoe==(const complex& x, const complex& y)
{
    return real(x) == real(y) && imag(x) == imag(y);
}
```

#### 操作符的分类

- 一元操作符：只有一个操作数，如：+a, -a, !a, ~a, ++a, --a, a++, a--, (type)a, *a, &a, sizeof a
- 二元操作符：有两个操作数，如：a+b, a-b, a*b, a/b, a%b, a==b, a!=b, a>b, a>=b, a<b, a<=b, a&&b, a||b, a&b, a|b, a^b, a<<b, a>>b, a,b
- 三元操作符：有三个操作数，如：a?b:c

#### 成员函数

```c++
class complex
{
public:
    complex(double r = 0, double i = 0) : re(r), im(i) {}
    complex& operator+=(const complex&);
    double real() const { return re; }
    double imag() const { return im; }
private:
    double re, im;
    friend complex& __doapl(complex*, const complex&);
};
inline complex&
complex::operator+=(const complex& r)
{
    re += r.re;
    im += r.im;
    return *this;
}
```

上述代码中，**operator+=是一个成员函数**。因此，**this指针指向调用该成员函数的对象**。

```c++
complex c1(2, 1);
complex c2(4, 2);
c1 += c2;
```

上述代码中，**c1是调用operator+=的对象**。因此，**this指针指向c1**。

任何一个成员函数都有一个this指针，指向调用该函数的对象。因此，**this指针是一个隐含的参数**。

注：**操作符重载成员函数中，隐藏的this作为操作符左边的操作数，而操作符右边的操作数是一个常量引用。**

#### 非成员函数

```c++
class complex
{
public:
    complex(double r = 0, double i = 0) : re(r), im(i) {}
    complex& operator+=(const complex&);
    double real() const { return re; }
    double imag() const { return im; }
private:
    double re, im;
    friend complex& __doapl(complex*, const complex&);
};
inline complex&
__doapl(complex* ths, const complex& r)
{
    ths->re += r.re;
    ths->im += r.im;
    return *ths;
}
inline complex&
complex::operator+=(const complex& r)
{
    return __doapl(this, r);
}

inline complex
operator+(const complex& x, const complex& y)
{
    return complex(real(x) + real(y), imag(x) + imag(y));
}

inline complex
operator+(const complex& x, double y)
{
    return complex(real(x) + y, imag(x));
}

inline complex
operator+(double x, const complex& y)
{
    return complex(x + real(y), imag(y));
}
```

- 上述代码中，**operator+是一个非成员函数**。因此，**该函数没有this指针**。
- 上面三个函数绝不可能return by reference，因为它们的返回值是一个临时对象，一出函数就消亡。在函数外界定义的对象，才可以return by reference。而且对于常量对象，应该return by reference to const。
- 临时对象：typename(); 上面三个函数的返回值都是complex()，因此，它们的返回值都是临时对象。

#### << 操作符重载（output operator）

```c++
#include <iostream>
using namespace std;
ostream& operator<<(ostream& os, const complex& x)
{
    return os << '(' << real(x) << ',' << imag(x) << ')';
}
```

- **output operator必须写成非成员函数，因为<<左边是ostream对象，而不是complex对象**。

## String类的设计——带指针的类

```c++
class String
{
public:
    String(const char* cstr = 0);
    String(const String& str); // 拷贝构造函数
    String& operator=(const String& str); // 拷贝赋值函数
    ~String(); // 析构函数
    char* get_c_str() const { return m_data; }
private:
    char* m_data;//用一个指针而不是一个数组，是因为数组的大小是固定的，而String类的大小是不固定的。
};
```

### 拷贝构造函数

```c++
inline
String::String(const String& cstr=0)
{
    if(cstr)
    {
        m_data = new char[strlen(cstr.m_data) + 1]; // 为指针分配内存,具体大小为cstr.m_data的长度+1，因为要存放'\0'
        strcpy(m_data, cstr.m_data);
    }
    else // 未指定初值
    {
        m_data = new char[1];
        *m_data = '\0';
    }
}
```

### 拷贝赋值函数

```c++
inline
String& String::operator=(const String& str)
{
    if(this == &str) // 判断是否是自我赋值
        return *this;
    delete[] m_data; // 释放原来的内存
    m_data = new char[strlen(str.m_data) + 1]; // 为指针分配内存,具体大小为cstr.m_data的长度+1，因为要存放'\0'
    strcpy(m_data, str.m_data);
    return *this;
}
```

### 析构函数

```c++
inline
String::~String()
{
    delete[] m_data;
}
```

上述代码中，因为使用 new 分配了内存，所以必须使用 delete 释放内存。如果不释放内存，那么就会造成内存泄漏。

### 堆和栈

#### 栈

Stack,是**存在于某作用域内的一块内存区域**，用于**存放局部变量**。Stack的大小是有限的，一般是几百KB。Stack的生命周期是从函数调用开始，到函数调用结束。Stack的生命周期是由编译器自动管理的，不需要程序员手动管理。

#### 堆

Heap，是指由操作系统管理的一块内存区域，用于存放**动态分配的内存**。Heap的大小是没有限制的，一般是几GB。Heap的生命周期是从**new**开始，到**delete**结束。Heap的生命周期是由程序员手动管理的，不需要编译器自动管理。

#### heap objects 的生命周期

```c++
class A {};
A* p = new A; // 在heap上创建一个A对象
delete p; // 在heap上销毁一个A对象
```

上述代码是正确的，在同一个作用域中，new生成的对象，必须在同一个作用域中delete。

```c++
class A {};
A* p = new A;
```

**上述代码会造成内存泄露，因为当作用域结束，p所指的heap object仍然存在，但是指针p的生命周期已经结束，所以p所指的heap object就无法访问了，作用域之外再也看不到p**。

#### Stack和Heap的区别

- Stack是由编译器自动管理的，Heap是由程序员手动管理的。
- Stack的大小是有限的，Heap的大小是没有限制的。

### new 的剖析

- new:**先分配内存，再调用构造函数**

```c++
Complex* p = new Complex(1, 2);
```

上述代码等价于

```c++
void* mem = operator new(sizeof(Complex)); // 分配内存，指向void*类型的指针
p = static_cast<Complex*>(mem); // 将void*类型的指针转换为Complex*类型的指针
p->Complex::Complex(1, 2); // 调用构造函数
```

### delete 的剖析

- delete:**先调用析构函数，再释放内存**

```c++
String* ps = new String("hello");
...
delete ps;
```

上述代码等价于

```c++
String::~String(ps); // 调用析构函数
operator delete(ps); // 释放内存,其内部调用了free(ps)
```

### array new 和 array delete

- array new 要和 array delete 配对使用

```c++
int* p = new int[10];
...
delete[] p; // 唤起10次dtor
delete p; // 错误，只唤起1次dtor，会造成内存泄漏（会导致数组中的对象的析构函数不被调用）
```

## 进一步补充

### static

- static 数据在程序运行期间一直存在，直到程序结束才释放
- **static 数据只有一份，在一个单独的内存区域**
- 分为静态数据和静态函数

```c++
class Account {
public:
    static double m_rate; // 静态数据成员
    static void set_rate(const double& r) { m_rate = r; }
};
double Account::m_rate = 8.0; // 静态数据成员的定义

int main() {
    Account::set_rate(10.0); // 静态函数的调用
    cout << Account::m_rate << endl; // 静态数据成员的访问

    Account a1;
    a1.set_rate(12.0);
    return 0;
}
```

上述代码中，调用静态函数有两种方法，**一种是通过类名调用**，**一种是通过对象调用**。静态数据成员的访问也有两种方法，一种是通过类名访问，一种是通过对象访问。

**单例模式**

```c++
class Singleton {
public:
    static Singleton* getInstance() {
        if (m_instance == nullptr) {
            m_instance = new Singleton();
        }
        return m_instance;
    }
private:
    Singleton() {}
    static Singleton* m_instance;
};

Singleton* Singleton::m_instance = nullptr;// 静态数据成员的定义
```

上述代码中，m_instance是一个静态数据成员，它的作用是保存Singleton类的唯一实例。在getInstance()函数中，如果m_instance为nullptr，说明还没有创建Singleton类的实例，那么就创建一个实例，否则直接返回m_instance。

### cout

cout是一个ostream对象，它的定义如下：

```c++
class ostream {
public:
    ostream& operator<<(int);
    ostream& operator<<(double);
    ostream& operator<<(char);
    ostream& operator<<(const char*);
    ostream& operator<<(const string&);
    ostream& operator<<(ostream& (*pf)(ostream&));
    ...
};
```

上述代码中，operator<<是一个重载函数，它的作用是将一个对象插入到ostream对象中。cout是一个ostream对象，它的作用是将一个对象插入到标准输出流中。

### 函数模板

```c++
template <typename T>
void swap(T& a, T& b) {
    T tmp = a;
    a = b;
    b = tmp;
}

int main() {
    int a = 1, b = 2;
    swap(a, b);
    cout << a << " " << b << endl;
    return 0;
}
```

上述代码中，swap()函数是一个函数模板，它的作用是交换两个对象的值。函数模板的定义和普通函数的定义类似，只是在函数名前面加了一个template关键字。函数模板的调用和普通函数的调用类似，因为编译器会做实参推导。

### namespace

```c++
namespace A {
    ...
}
```

上述代码中，A是一个命名空间，它的作用是将一组相关的函数、类、变量等组织在一起。**命名空间的作用是避免命名冲突**。

如何使用命名空间呢？有两种方法：

1. 使用using声明

```c++
using A::func;
```

2. 使用using编译指令

```c++
using namespace A;
```