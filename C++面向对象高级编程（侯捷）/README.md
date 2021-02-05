# C++面向对象高级编程（侯捷）

Status: Finished
Tags: Coding

- [C++面向对象高级编程（侯捷）](#c--------------)
  * [对象的两个经典分类](#---------)
  * [Header文件的防卫式声明](#header--------)
  * [Complex类](#complex-)
  * [const-常量成员函数(不改变数据的函数)](#const-----------------)
  * [参数传递](#----)
    + [by value](#by-value)
    + [by reference](#by-reference)
  * [返回值传递](#-----)
  * [友元函数](#----)
  * [操作符重载](#-----)
    + [成员函数形式](#------)
    + [非成员函数形式](#-------)
  * [拷贝构造、拷贝赋值、析构函数](#--------------)
    + [三个重要函数](#------)
    + [拷贝赋值的三个步骤](#---------)
  * [内存管理中的栈、堆](#---------)
    + [static local object的生命期](#static-local-object----)
    + [内存管理](#----)
    + [为什么array new要搭配array delete](#---array-new---array-delete)
    + [new 和 delete 的重载](#new---delete----)
  * [static 静态](#static---)
    + [单例模式](#----)
  * [函数模版与类模版的区别](#-----------)
    + [成员模版](#----)
  * [组合、委托、继承](#--------)
    + [组合（Composition）](#---composition-)
    + [委托（Delegation 或 Composition by reference）](#---delegation---composition-by-reference-)
    + [继承（Inheritance）](#---inheritance-)
  * [虚函数&多态（virtual）](#-------virtual-)
    + [继承跟组合一起存在时的析构情况](#---------------)
    + [委托+继承](#-----)
  * [转换函数](#----)
    + [non-explicit-one-argument-ctor](#non-explicit-one-argument-ctor)
    + [explicit](#explicit)
  * [pointer-like-class](#pointer-like-class)
    + [智能指针](#----)
    + [迭代器](#---)
  * [Reference（内存角度）](#reference------)
  * [关于vptr和vtbl（虚指针、虚表）](#--vptr-vtbl--------)
    + [多态的应用例子](#-------)

---

## 对象的两个经典分类

- 不带有指针的类
    - Complex
- 带有指针的类
    - String

---

## Header文件的防卫式声明

```cpp
#ifndef __COMPLEX__
#define __COMPLEX__

// code

#endif
```

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled.png)

防卫式声明可以防止重复的定义或者重复`include` 该头文件的代码。

---

## Complex类

这里需要注意**模板**的基本使用以及**构造函数**的使用。

```cpp
template<typename T> //模板T
class Complex{
public:
	Complex(T r = 0, T i = 0) 
		: real(r), imagine(i) 
	//构造函数，避免两个数值的重复初始化
	{}
private:
	T real;
	T imagine;
};

//main
{
	Complex<double> com1;
}
```

在`Singleton`设计模式中，构造函数放在`private`区。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%201.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%201.png)

只需要了解有这种需求即可。

---

## const-常量成员函数(不改变数据的函数)

如获取实部的函数:

```cpp
double real() const { return real; }
```

不加的时候，若执行

```cpp
{
	const Complex c1(2, 1); //初始化一个const类
	cout << c1.real(); //此处是无法执行的（函数只有是const时，才能被编译）
}
```

因而该加`const`函数的必须加！！

`const`提供了最简单的**保护机制**。

注意加了const的函数跟没加const的函数是两个不同的函数签名。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%202.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%202.png)

没有保证不改变数据的函数，在 `const` 对象中不能使用。

当两者存在 `const` 和非 `const` 版本时，对象会优先调用对应的函数。这种特性可以用来实现 copy-on-write 的功能。

---

## 参数传递

pass by value   vs    pass by reference

### by value

传递时，对象整包都被传递过去。

无论几个字节都会被传过去。

### by reference

类似于C中直接传递指针。效率最大化。

但不同于指针的点在于，形式更漂亮。最好所有都传递引用。

当传递引用，但想保持只读性质，则使用`const` 。

```cpp
complex & operator += (const complex&);
```

---

## 返回值传递

pass by value   vs    pass by reference

类似于参数的传递，最好返回引用。

但必须在可以的情况下。存在不能以引用返回的状况

例如已经存在实例c1，c2

```cpp
c1 += c2; //它的函数是可以以引用返回的
c1 + c2;  //它的函数时不可以以引用返回的
```

取决于返回值是不是已经存在于内存之中。

注意：引用参数的传递中，传递者不需要知道接收者是否采用引用形式。

这也是为什么引用比指针更漂亮。

---

## 友元函数

友元函数最大的特征在于，其他类可以调用访问内部的数据。

即可以自由取得`friend` 的 `private` 成员。

```cpp
complex& __doapl(complex*, const complex&);
class complex
{
public:
	//...
private:
	//...
	friend complex& __doapl(complex*, const complex&);
}

inline complex& __doapl(complex* ths, const complex& r)
{
	ths->re += r.re;
	ths->im += r.im;
	return *ths;
}
```

注意：相同 class 的各个 objects 互为友元。但不意味着这里的 friend 可以忽略。（这里的 friend 函数不是类的成员函数）

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%203.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%203.png)

相同 class 的 object 互为友元。

---

## 操作符重载

### 成员函数形式

如 Complex 类，可以在类内编写一个操作符重载

```cpp
class Complex{
public: 
	//...
private:
	//...

	//友元函数
	friend Complex& __doapl(Complex* , const Conplex& );
};

inline Complex& __doapl(Complex* ths, const Complex& c){
	ths->real += c.real;
	ths->imgaine += c.imagine;
	return *ths; //reference 发送者不需要知道接收是否引用类型（传值即可）
}

inline Complex& operator += (const Complex& c){
	return __doapl(this, c);
}
```

### 非成员函数形式

需要注意大部分非成员函数的重载，返回值都是 local object，即不可传回引用。

```cpp
inline complex operator + (const complex& x, const complex& y){
	return complex(real(x) + real(y), imag(x) + imag(y)); 
	//temp object 生命在下一行就会结束
}
```

---

~~这里的 real，imag函数写法存在疑问。~~

这里的 real，imag 都是非成员函数。

---

## 拷贝构造、拷贝赋值、析构函数

**class 中带有指针（class with pointer members）**时，编译器提供的拷贝会直接复制指针。显然编译器提供的拷贝是不够用的。

### 三个重要函数

```cpp
class String{
public:
	String(const char* cstr = 0);
	String(const String& str); //对自身的拷贝构造
	String& operator = (const String& str); //拷贝赋值
	~String(); //析构函数
	char* get_c_str() const{ return m_data; }
private:
	char* m_data;
};
```

浅拷贝可能会带来内存泄露。这是显然的。

### 拷贝赋值的三个步骤

- 回收原来的空间
- 申请新的空间
- 复制 data

注意不能自我赋值！自我赋值会破坏所有的数据

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%204.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%204.png)

---

## 内存管理中的栈、堆

所谓的栈，是指在每个函数调用时，内存分配相应的一个栈来存放所有的local object。

所谓的heap，是指操作系统在内存中划分一块专门的全局的区域，用于堆放数据。这里的数据一旦申请，必须自己负责回收。

```cpp
void fun(){
    int a = 2;   //在当前栈中存在 函数结束后自动死亡
    int * p = new int(3);  //存在于gobal的heap中，只有自己调用delete才会清理
}
```

### static local object的生命期

被冠于static的对象，其生命直到整个程序结束才会结束。

```cpp
static complex c;
```

定义于所有函数之外的全局变量生命周期也与上相同。

### 内存管理

申请内存空间时，操作系统会在数据上下附带cookie信息，以记录数据块的大小。同时也会填充数据块使得内存对齐。

存放数组时，vc会额外保存数组的元素个数。

调用new时会发生三件事：

1. malloc内存空间
2. 转换指针类型
3. 调用构造函数

同样的，调用delete时，会发生：

1. 调用析构函数，对带指针的类极其重要
2. 删除内存空间

### 为什么array new要搭配array delete

```cpp
string *p = new String[3];
delete [] p;
// delete p; 内存泄漏
```

这里发生内存泄漏的原因并不是因为内存没清理干净，而是因为析构函数仅调用了一次。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/F951F506-F99A-4233-83E5-8058D2469216.jpeg](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/F951F506-F99A-4233-83E5-8058D2469216.jpeg)

### new 和 delete 的重载

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/6FF942A2-1043-4E2F-A9BE-D60724E9087B.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/6FF942A2-1043-4E2F-A9BE-D60724E9087B.png)

```cpp
inline void * operator new(size_t size){
	return myAlloc(size);
}
inline void * operator new[](size_t size){
  return myAlloc(size);
}
```

new和delete操作符都可以被重载。

以数组new后，内存开头会多出一个四字节的指针，指针内容为开辟的数组长度大小。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/CCC69844-E3CD-42DD-BFD0-0C53A919CAC0.jpeg](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/CCC69844-E3CD-42DD-BFD0-0C53A919CAC0.jpeg)

## static 静态

在类中，非静态的函数调用时都会做以下转换

```cpp
cout << c.real();
//等价于
cout << complex::real(&c);
```

这意味着非静态的函数，仅有一份。不同的实例仅仅是传入了不同的this指针。

`static`的数据，每个类只会拥有一份。`static`的函数同理，且不能访问`this`指针。这意味着`static`的函数仅仅能访问`static`的数据。

`static`的数据需要初始化

可以通过类名或对象名调用`static`的函数，效果 都是一样的。

### 单例模式

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/1DDE452A-5655-4C88-A447-C7860CC91C70.jpeg](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/1DDE452A-5655-4C88-A447-C7860CC91C70.jpeg)

这里的`a`无论外界有没有需要，都会占用资源。

改进方法为：

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/0746EDD8-A6BA-4E7B-8F0C-5713DD793658.jpeg](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/0746EDD8-A6BA-4E7B-8F0C-5713DD793658.jpeg)

---

## 函数模版与类模版的区别

类模版在使用时必须指定模版的类型：

```cpp
vector<int> v;
```

而函数模版不需要，函数模版会自动推断对象的类型。

```cpp
template <typename T>
const T& min(const T& a, const T& b){
    return (a > b) ? b : a;
}
```

这里编译器会自动推断输入参数的类型，对**每一份类型都保存一份 代码**。虽然浪费了空间，但这也是必须的开销。

### 成员模版

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/4EBE6F01-D078-4F10-BB0A-88724D93E872.jpeg](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/4EBE6F01-D078-4F10-BB0A-88724D93E872.jpeg)

这里的构造函数设计允许不同类型之间的复制构造。

---

## 组合、委托、继承

 OOP主要三大关系：组合、委托、继承

### 组合（Composition）

例如

```cpp
template <class T>
class queue{
	...
protected:
	deque<T> c; //container
public:
	//functions from deque
}
```

这里 `queue` 类包含了 `deque` 类，这种形式称为组合。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%205.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%205.png)

这里的 queue 完全利用 deque 的 api 来实现，这种设计模式也被称为 Adapter 模式（适配器模式）。这是组合的一种特例。

**内存角度：**

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%206.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%206.png)

非常自然的，外部的类包含了内部的类。

构造由内到外，即先调用底层的构造函数，再调用高层的构造函数。

析构由外到内，先调用外部的析构函数，再调用内部的析构函数。

### 委托（Delegation 或 Composition by reference）

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%207.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%207.png)

外部类不完全包含内部类，如外部类只存在一个指向内部类的指针：

```cpp
class StringRep;
class String{
public:
	String();
	String(const char* s);
	String(const String& s);
	String &operator=(const String& s);
	~String();
private:
	StringRep* rep; //pointer to StringRep
}
```

任何一个时间点可以委托任务给内部类。

这里被称作 Composition by reference 的原因是通过指针传递 object 的行为也称作 by reference。

与组合的区别：内部类的寿命不一样。

这种实现被称为 pointer to implementation，只保留一个指针接口来指向具体实现的类。

### 继承（Inheritance）

 public 的继承代表的是 is-a 的关系

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%208.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%208.png)

内存角度：

构造由内到外，即先调用父类的构造函数，再调用子类的构造函数。

析构由外到内，先调用子类的析构函数，再调用父类的析构函数。

若一个类可能要被继承，构造函数最好加 virtual。

---

## 虚函数&多态（virtual）

 准确来说，通常子类获得的是父类函数的调用权。

non-virtual 函数：不希望子类重新定义该函数。

virtual 函数：希望子类重新定义该函数，虽然该函数已经有默认定义。

pure-virtual 函数：子类必须重新定义该函数。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%209.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%209.png)

虚函数的用处：延后父类中实现方法的实现：

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2010.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2010.png)

父类函数会自动寻找子类复写的函数代码。这也是大名鼎鼎的 Template Method 设计模式。

### 继承跟组合一起存在时的析构情况

作为课后习题。。。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2011.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2011.png)

结果：

Base→Component→Derived

Component→Base→Derived

### 委托+继承

Composite 设计模式

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2012.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2012.png)

设计模式相关知识还需要补充。

---

## 转换函数

如分数类的实现

```cpp
class Fraction{
public:
	Fraction(int num, int den = 1)
		: m_numerator(num), m_denominator(den){ }
	operator double() **const**{ //该加 const 就要加
		return ((double)m_numerator / m_denominator);	
	}//转换函数不需要返回类型
private:
	int m_numerator;
	int m_denominator;
}
```

当执行 `double d = 4 + f` 时，编译器自动转换 fraction 为 double。

### non-explicit-one-argument-ctor

当设计中包含一个参数的构造函数，在执行语句`Fraction d = f + 4` 时，编译器则直接将 4 转换为 `Fraction` 类。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2013.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2013.png)

当出现多种转换方式时，编译器就会认为这个是 ambiguous 的。

### explicit

添加上 `explicit` 关键字的构造函数，不再成为一个参数的转换函数。即取消了上面 `non-explicit` 的特性。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2014.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2014.png)

此时转换就发生失败。

---

## pointer-like-class

### 智能指针

即高级的指针，比指针多出一些特性。此处是 shared_pointer

```cpp
template<class T>
class shared_ptr{
public:
	T& operator* () const
	{ return *px; }
	T* operator->() const
	{ return px; }
	shared_ptr(T* p) : px(p) { }
private:
	T* px;
	long* pn;
}
```

这里的存在的问题是，调用 `ptr→method()`时，`ptr→`已经变成了 `px`，但是`→`的特性会使得编译器继续调用 `px→method()`。

### 迭代器

迭代器也是一种智能指针，多了很多的操作符重载。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2015.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2015.png)

注意这里的*和→写法

```cpp
T& operator* () const{ return (*node).data; }
T* operator-> () const{ return &(operator* ());}
```

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2016.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2016.png)

这里真的需要.data 吗？

---

## Reference（内存角度）

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/1D65DE76-C838-4BF8-A174-DA7EE0A7F44A.jpeg](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/1D65DE76-C838-4BF8-A174-DA7EE0A7F44A.jpeg)

```cpp
int x = 0;
int* p = &x;    //指针：值为x的地址
int& r = x;     //引用：r代表x  （底部其实有一根指针指向x）
int x2 =5;

r = x2;  //r不能重新代表其他物体。 **现在x2的值赋予了r，也赋予了x。**
int& r2 = r;  //r2也为5
```

此外编译器对于引用还做了一些假象，使引用看起来更像引用：

- `sizeof(r) == sizeof(x)`
- `&x==&r`

另外Java里面所有的变量都是reference。

---

## 关于vptr和vtbl（虚指针、虚表）

只要有一个虚函数，类就会多一个**虚指针**（4字节）。它的子类也会有虚指针。

继承函数的意思是继承函数的调用权。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/FE72C1E6-016D-437D-B45E-53769237BDE1.jpeg](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/FE72C1E6-016D-437D-B45E-53769237BDE1.jpeg)

此处生成了四份非虚函数和四份虚函数代码。虚指针和虚表的责任则是**分发**这些 虚函数。

通过`C`类的指针`p`调用`vfunc1`时，通过`vptr` 找到`vtbl` ，再找到真正的函数代码。

### 多态的应用例子

考虑一个画图工具栏，能画各种各样的图案，这个工具栏里面摆放的是`tool` 类的指针，而`tool`类被很多对象所继承。此时只需要调用各个子类的`draw`方法（一定为虚函数），就可以实现。如果没有虚函数机制，实现起来要用大量的条件判断。

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/55E9C0CC-5B18-4522-932F-2D41FA95FC73.jpeg](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/55E9C0CC-5B18-4522-932F-2D41FA95FC73.jpeg)

这个过程也是**动态绑定**的过程。

多态的条件：

- 指针
- 指针向上兼容
- 调用的是虚函数

多态、虚函数、虚指针、虚表、动态绑定，这些东西都指的是虚机制，都是同一件事情。

另一个多态的例子：

![C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2017.png](C++%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20ae3ac6e96fb745078f6dfa4ed9b34cf0/Untitled%2017.png)

这里调用 `myDoc.OnFileOpen()` 时，编译器翻译为`CDocument::OnFileOpen(&myDoc)` 。因而在 `OnFileOpen()`函数中， `this` 指针代表了`&myDoc` 这一地址。恰好这里满足了多态的三个条件，因而编译器将它按照虚机制来执行。