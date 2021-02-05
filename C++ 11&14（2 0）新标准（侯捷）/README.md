# C++ 11&14（2.0）新标准（侯捷）

Date: Jan 23, 2021
Status: Finished
Tags: Coding

- [C++ 11&14（2.0）新标准（侯捷）](#c---11-14-20--------)
  * [Variadic Templates 多参数模板](#variadic-templates------)
    + [hash function 的实现](#hash-function----)
    + [tuple 的实现](#tuple----)
  * [模板后>>之间的空格](#----------)
  * [nullptr 与 std::nullptr_t](#nullptr---std--nullptr-t)
  * [auto](#auto)
  * [Uniform Initialization 初始化](#uniform-initialization----)
  * [Initializer Lists](#initializer-lists)
  * [explicit for ctors taking more than on argument](#explicit-for-ctors-taking-more-than-on-argument)
  * [Range-base for statement 增强 for](#range-base-for-statement----for)
  * [=default 与 =delete](#-default----delete)
  * [Alias Template 化名模板](#alias-template-----)
    + [template template parameter (模板模板参数)](#template-template-parameter---------)
  * [Type Alias 类型别名(typedef  与 using)](#type-alias------typedef----using-)
  * [noexcept 忽略异常](#noexcept-----)
  * [override 改写](#override---)
  * [final  不能被继承](#final-------)
  * [decltype 类似于 typeof](#decltype-----typeof)
  * [lambdas](#lambdas)
  * [Rvalue reference (右值引用)  与 move](#rvalue-reference-----------move)
    + [Perfect Forwarding 完美转移](#perfect-forwarding-----)
    + [Move-aware class](#move-aware-class)



## Variadic Templates 多参数模板

---

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled.png)

...表示一组东西，可以用于数量不定的模板参数。注意需要写一个处理空参数的函数。

```cpp
void print(){
    cout << endl;
}

template<typename T, typename... Types>
void print(const T& firstArg, const Types&... args){
    cout << firstArg << " ";
    print(args...);
}
```

### hash function 的实现

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%201.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%201.png)

提供了很方便地进行切分和递归的实现方法。总是会调用更加特化的函数。

### tuple 的实现

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%202.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%202.png)

递归地去继承，产生类（类外面嵌套着类）。

---

## 模板后>>之间的空格

```cpp
vector<vector<int>> dp;
```

---

## nullptr 与 std::nullptr_t

可以用 `nullptr` 代替 `NULL` 或者 `0`。在此前 `NULL` 定义为 `0` 。现在的 `nullptr` 定义为 `std::nullptr_t`。

---

## auto

自动推断类型，用于名字很长的 type 类型。

```cpp
auto pos = v.begin();

auto l = [](int x)->bool{...}; //lambda
```

 `auto`作为返回类型时，函数的返回类型由编译器根据返回值以及decltype推导规则进行自动推导。

---

## Uniform Initialization 初始化

以前的做法（多种）：

```cpp
Rect r1(1,2);
Rect r1 = {1,2};
```

现在可以用`{}`作为一切的初始化。

```cpp
Complex c{1,2};
vector<int> v{1,2,3,4};
```

事实：每个大括号都会创造为一个 `Initializer_list<T>`，当`Initializer_list<T>` 本身被传到容器的构造函数时，直接整包传递。当容器无构造函数接收`Initializer_list<T>` 时，参数会一个一个逐步分解传递给 `ctor` 。（效率不同）

---

## Initializer Lists

`{}`的一些性质：

```cpp
		int i; //undefined
    int j{}; // 0
    int* p; //undefined
    int* q{}; //nullptr

    // int z{5.0}; // narrowing
```

`initializer_list<>` 支撑了一致初始化。每一个`{}`可以转换成一个`initializer_list<>` 。当存在两种情况时：

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%203.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%203.png)

可以看到都是有优先调用 InitializerList 的函数。  InitializerList 的底层是 array，形成时采用浅拷贝。同时 InitializerList 影响了 stl 的实现。

```cpp
vector<int> v{1, 2, 3, 4, 5, 6};
v.insert(v.begin() + 1, {0,0,0});

cout << max({l, h, k, z});
```

---

## explicit for ctors taking more than on argument

`explicit` 针对构造函数可以针对一个以上的实参了。以前的用法：

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%204.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%204.png)

多个的情况：

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%205.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%205.png)

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%206.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%206.png)

---

## Range-base for statement 增强 for

```cpp
for(auto i: v)cout << v << endl;
for(auto &i: v)i++;

for(auto i: {1,2,3,4,5,6}}cout << i << endl;
```

其实是等价于

```cpp
for(auto iter = v.begin(); iter != v.end(); iter++){
		i = *iter;
}
//or
for(auto iter = begin(v); iter != end(v); iter++){
		i = *iter;
}
```

**这里可能会发生隐式转换，按情况判断需不需要`explicit`：**

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%207.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%207.png)

---

## =default 与 =delete

已知如果自定义了 ctor，编译器不会再提供默认的 ctor。

在构造函数后面加上`=default` 后，可以重新获得默认的 ctor。对三大函数来说，编译器会提供默认的版本。

类似的，加上`=delete`意味着不需要默认的构造函数。

```cpp
class Zoo{
public:
    Zoo(int i1, int i2): d1(i1), d2(i2){ }//构造函数
    Zoo(const Zoo&)=delete;//拷贝构造
    Zoo(Zoo&&)=default;//右值引用
    Zoo& operator=(const Zoo&)=default;//拷贝赋值
    Zoo& operator=(const Zoo&&)=delete; //右值引用
    virtual ~Zoo(){ } //virtual：避免不完全的析构（多态）
private:
    int d1, d2;
};
```

例子：

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%208.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%208.png)

如单例模式类只能有一个实例，所以要禁止默认的构造函数。

- 类中成员变量含有指针，则一定需要使用者自己写出 big five；反之如果该类不含 pointer member，则大多数情况不必重写 big five
- 例如 string 类中有指 针，需要重写全部 big five

    应用：

    - 禁止copy函数中使用 `=delete`，相当于 禁用 NoCopy 类中的拷贝构造、拷贝赋值函数

        ```cpp
        NoCopy(const NoCopy&) = delete;  //no copy
        NoCopy &operator = (const NoCopy&) = delete;  //no assignment
        ```

    - 析构函数中使用`=delete`，后果自负

        ```cpp
        ~NoDtor() = delete;  // we can't destroy objects of type NoDtor
        ```

    - 要**禁用拷贝构造、拷贝赋值函数**的一般操作：在private中重写，只能 友元/内部成员函数 调用。

---

## Alias Template 化名模板

带参数的别名化： 

```cpp
template <typename T>
**using** Vec = std::vector<T, MyAlloc<T>>;

Vec<int> coll;
//is equivalent to
std::vector<int, MyAlloc<int>> coll;
```

但是不可能对化名模板进行特化。

假设想要测试一个容器 `Container<T>`，写出函数，只获取对象的class作为参数。

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%209.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%209.png)

这是无法编译的，因为容器参数是一个 object，在初始化的时候，T 已经固定了。

实际上是想要以class 作为函数的参数

利用 iter 得到 value 类型：

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2010.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2010.png)

传一个临时对象进去，然后通过typedef 得到 value 类型，解决。但是有方法可以更好地解决这个问题，可以做到直接传类型名称作为函数：

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2011.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2011.png)

### template template parameter (模板模板参数)

完全解决上面的问题：

```cpp
template<typename T>
**using Vec = vector<T, allocator<T>>**

**template<typename T, template<class T> class Container>**
class XCIs
{
private:
	**Container<T> c;**
public:
	****XCIs(){ /* test code*/}
};

XCIs<MyString, Vec> c1;
```

---

## Type Alias 类型别名(typedef  与 using)

```cpp
using func = void(*)(int, int);
//equal to
typedef void(*func)(int, int)

void example(int, int){}
func fn = example;
------
typedef T value_type;
//equal to
using value_type = T;
```

两者是一样的。

---

## noexcept 忽略异常

```cpp
void foo() noexcept {
	//...
};
// or
void foo() noexcept(true){
	//...
}
```

若异常一直没有被处理，一直往上层调用，最后调用 `std::terminate()` 。

**注意在使用右值引用参数的函数中，需要添加 `noexecpt`。在需要成长的容器中（vector），需要调用 `move` 动作，此时 `move` 版本的构造函数应该要优先于`copy` 的。我们需要添加 noexpect 关键字来通知 c++这是有 move 版本的函数。**

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2012.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2012.png)

---

## override 改写

应用于虚函数的改写，可加可不加。加是为了告诉编译器，这是改写的函数，防止改写函数的声明与父类的虚函数声明不一样而没有发现。

---

## final  不能被继承

被修饰的类不能被继承，修饰的虚函数不能被重写。

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2013.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2013.png)

---

## decltype 类似于 typeof

以前的 typeof 只能输出字符串，而 decltype 可以直接当做类名称来声明变量。

```cpp
string a = "123";
decltype(a) an_other_a = "456";
```

应用：

- 推断的返回参数类型（T1 + T2 的 type 不可预测）

    ```cpp
    template<typename T1, typename T2>
    auto add(T1 x, T2 y) -> decltype(x + y){
    		return x + y;
    }
    ```

- 应用于元编程（模板之间）

    ![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2014.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2014.png)

- pass a type of lambda

    ```cpp
    auto cmp = [](const Person& p1, const Person& p2){
    	return p1.name() < p2.name();
    }
    //... lambda 的 type 非常复杂
    vector<decltype(cmp)> cmplist(cmp);
    ```

---

## lambdas

lambda 可以作为参数或者是对象。

```cpp
[]{
	cout << "hello";
}(); //直接调用

auto I = []{
	cout << "hello";
};
I(); //调用lambda
```

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2015.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2015.png)

 []内表示需要使用的外部变量。可以 by value 和 by reference。

即 lambda 实际上是一个 functionlike object，也有内部数据。

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2016.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2016.png)

注意这里需要 `mutable`，id 才能被++。

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2017.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2017.png)

注意 lambda 并没有默认的构造函数和赋值函数，因而要避免。

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2018.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2018.png)

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2019.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2019.png)

---

## Rvalue reference (右值引用)  与 move

右值引用是为了避免不必要的 copy。当赋值右手边来源边是一个右值，左手边的接收端可以去 steal 右手边的资源，而不需要执行 allocation。也可以称为 move。

左值是变量可以在左右边，而右值不能放在左边。

```cpp
int a = 9;
int b = 10;
 //a, b 都是左值
a + b = 42 //非法，所以 a+b 为右值
```

但也有例外，当 int 为 string 或 complex 时，

```cpp
string s1 = "he", s2 = "llo";
s1 + s2 = s2; //通过编译
string() = ”world“ // 通过编译
//非常奇怪
```

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2020.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2020.png)

当传入参数为右值（临时对象），调用的是右值引用参数的函数。

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2021.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2021.png)

move 的 ctor 避免了拷贝构造，把指针切换过去即好（浅拷贝）。

若想要把左值作为参数 move ，则使用 `std::move()`：

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2022.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2022.png)

### Perfect Forwarding 完美转移

右值在传递过程中可能不再是右值。需要借助特殊的设计。使用 `std::forward()`可以这个问题。

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2023.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2023.png)

### Move-aware class

关键在于右值引用的复制构造、复制赋值、析构函数。

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2024.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2024.png)

![C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2025.png](C++%2011&14%EF%BC%882%200%EF%BC%89%E6%96%B0%E6%A0%87%E5%87%86%EF%BC%88%E4%BE%AF%E6%8D%B7%EF%BC%89%20683d70f65a4744159732f6c939738888/Untitled%2025.png)