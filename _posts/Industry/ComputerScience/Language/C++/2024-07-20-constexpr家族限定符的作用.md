---
title: constexpr家族限定符的作用
author: AshGrey
date: 2024-07-20 00:00:00 +0800
categories: [Computer Science, Language]
tags: [Computer Science, C++]
excerpt: 本文章所属领域：实践产业知识 - 工业产业 - 计算机科学 - 编程语言 - C++
---


<br>

> 本文章所属领域：
>
> [实践产业知识 - 工业产业 - 计算机科学 - 编程语言 - C++]({% post_url /Computer Science/2024-02-08-计算机科学：索引笔记 %})
>
> 本文章所需前置知识：
>
> - [C++基础知识]({% post_url /Computer Science/Language/2024-02-08-C++基础知识笔记 %})
{: .prompt-info}

<br>


## 1 constexpr

<br>

### 1.1 constexpr 变量

<br>

`const`变量的初始化可以推迟到运行时进行，但是`constexpr`变量的初始化必须在编译时就进行。对于一些需要常量表达式来初始化的数组，`constexpr`可以确保变量是常量表达式（`constexpr`变量蕴含`const`）：

<br>

``` cpp
#include <iostream>
#include <array>

/*

int main() {
    int len = 3;
    const int size = len + 1;
    std::array<int, size> int_arr = {1, 2, 3, 4};
                    ^^^^
                    Initializer of 'size' is not a constant expression
    int int_pod[size] = {1, 2, 3, 4};
                ^^^^
                Variable-sized object may not be initialized
    return 0;
}

*/

/*

int main() {
    int len = 3;
    constexpr int size = len + 1;
                  ^^^^
                  Constexpr variable 'size' must be initialized by a constant expression
    std::array<int, size> int_arr = {1, 2, 3, 4};
    int int_pod[size] = {1, 2, 3, 4};
    return 0;
}

*/

int main() {
    constexpr int size = 1 + 1 + 2;
    std::array<int, size> int_arr = {1, 2, 3, 4};
    int int_pod[size] = {1, 2, 3, 4};
    return 0;
}
```

<br>

可以看到第一段注释的代码中的`size`虽然是`const`变量，但是它是到运行期才初始化的，由于编译期就需要确定数组边界的容器和 C 风格数组所需要的边界大小必须是在编译期就确定的常量表达式，所以此处会报错。第二段注释的代码中，使用`constexpr`声明的变量是在编译期就初始化的，因此其必须使用常量表达式来初始化。注意，所有用`constexpr`声明的变量在运行期也是不能修改的，即其同时是`const`的。

<br>

``` cpp
#include <iostream>

struct test_class {
    int test_num;
    constexpr test_class(int a) : test_num(a) {}
    /*
        test_class(int a) : test_num(a) {}
        error : Constexpr variable cannot have non-literal type 'const test_class'
    */
};

int main() {
    constexpr test_class t(2);
    std::cout << t.test_num << std::endl;
    return 0;
}
```

<br>

这一段代码展现的是使用`constexpr`声明自定义的类的对象时，必须保证类的构造函数也是`constexpr`的，至于`constexpr`函数，这将在下一节提到。

<br>

### 1.2 constexpr 函数

<br>

`constexpr`修饰的函数在编译器就能进行计算，当需要在编译期获取`constexpr`函数的返回值时，函数在编译期就会计算并返回值；当需要在运行期获取`constexpr`函数的返回值时，函数在运行期会像正常函数一样进行计算。这不需要程序员编写一个函数的非`constexpr`版本和`constexpr`版本。

<br>

C++11 引入`constexpr`限定符之后，`constexpr`修饰的函数必须只有一个`return`语句，且内部不能使用局部变量、循环、分支，但是可以调用递归：

<br>

``` cpp
// !!! C++11
constexpr int fibonacci(const int n) {
    return n == 1 || n == 2 ? 1
        : fibonacci(n - 2) + fibonacci(n - 1);
}
```

<br>

C++14 之后，`constexpr`函数也可以有简单的循环和分支语句，内部可以有局部变量，可以有多个`return`语句：

<br>

``` cpp
constexpr int fibonacci(const int n) {
    if (n == 1) return 1;
    if (n == 2) return 1;
    return fibonacci(n - 2) + fibonacci(n - 1);
}
```

<br>

现在`constexpr`函数支持以下规则：
- 可以包含`if`或者`switch`语句，以及所有的循环语句，包括`for`、基于范围的`for`、`while`和`do-while`语句；
- 可以包含局部变量声明，但是对于 C++20 之前的版本，必须初始化变量（C++20 开始就没有这个要求）：

    ``` cpp
    // !!! < C++20
    constexpr void fibonacci(const int n) {
        int a;
    /*      ^
            Uninitialized variable in a constexpr function is a C++20 extension
    */
    }
    ```

- 不可以声明非字面类型的变量，以及静态或者线程存储期变量（就是不能使用`static`和`thread_local`声明变量）；

    <!--TODO:这里的字面类型（LiteralType）不是很懂是什么意思，但大概知道一般情况下不会遇到-->

- 不可以包含`goto`控制流语句，不可以包含除了`case`以及`default`外的`:`标号语句；
- 在 C++20 之前`constexpr`函数不能是虚函数；
- 在 C++20 之前`constexpr`函数不能是函数`try`块；
- 自 C++20 引入协程之后，`constexpr`函数不能是协程。

<br>

`constexpr`函数是隐式内联的：

<br>

> 注：这里再提下内联函数`inline`是什么，关键字`inline`建议编译器使用函数定义中的代码替换对该函数的每次调用。理论上使用内联函数可以加速程序运行，因为消除了函数调用关联的开销。调用函数需要将返回地址推送到堆栈、将参数推送到堆栈、跳转到函数体，然后在函数完成时执行返回指令。通过内联函数可以消除这一个过程。注意内联函数的替换操作由编译器自行决定，如果某个函数的地址已被占用或者编译器判定函数过大，就有可能不予内联该函数。
>
> 类声明的主体中定义的函数是隐式内联函数，隐式的则意味着在类中声明的函数是否加`inline`关键字都是同样的：
>
> ``` cpp
> class test_class {
> private:
>     std::string_view test_str;
> public:
>     test_class(std::string_view s) : test_str(s) {}
>     void test_function();
> };
> 
> inline void test_class::test_function() {
>     std::cout << "This is called by the test_function.\n" 
>               << "This is the test_str : " 
>               << test_str
>               << "\n"
>               << std::endl;
> }
> 
> int main() {
>     test_class obj("123");
>     obj.test_function();
>     return 0;
> }
> ```

<br>

`constexpr`函数内部必须产生的都是常量表达式（编译期就能确定的表达式），能计算且能

<br>

### 1.3 if constexpr 控制流语句

<br>

`if constexpr`的分支语句可以在编译期就确定判断的常量表达式的值，注意如果下面的`constexpr`省略的话，返回值就有两种情况了，`auto`并不支持推导出不同的类型：

<br>

``` cpp
#include <iostream>

template<typename T>
auto print_type_info(const T& t) {
    if constexpr (std::is_integral<T>::value) {
        std::cout << "Integer\n";
        return t + 1;
    }
    else {
        std::cout << "Double\n";
        return t + 0.001;
    }
}

int main() {
    std::cout << print_type_info(5)   << "\n"
              << print_type_info(0.2) << std::endl;
    return 0;
}
```

<br>

再比如下面的代码用于检测是否是指针类型：

<br>

``` cpp
#include <iostream>
#include <string_view>

template<typename T>
auto show_value(T t) {
    if constexpr (std::is_pointer_v<T>) {
        std::cout << "Is a pointer.\n";
        return *t;
    }
    else {
        std::cout << "Not a pointer.\n";
        return t;
    }
}

int main() {
    std::string_view test_str = "Test";
    std::string_view* test_str_ptr = &test_str;
    std::cout << show_value(test_str)     << "\n"
              << show_value(test_str_ptr) << "\n"
              << std::endl;
    return 0;
}
```

<br>

`if constexpr`语句在编译期计算，编译器只生成与发送到函数模板的参数类型匹配的`if`分支的代码。该语句判断的、可按语境转换到`bool`类型的常量表达式将转换为`bool`类型的常量表达式，如果其值为`true`，则舍弃`false`分支，否则舍弃`true`分支，其中被舍弃语句中的`return`语句不会参与到函数返回类型推导。

<br>

<br>

<br>

## 2 consteval

<br>

### 2.1 consteval 函数

<br>

`consteval`实际是就是`constexpr`的补丁，其只能对函数进行声明。我们知道`constexpr`函数可以在编译期计算，也可以在运行期计算，这是`constexpr`的自主行为。如果我们不希望一个函数在运行期被调用，而是在编译期就被调用进行计算，那么我们可以使用`consteval`关键字：

<br>

``` cpp
consteval int test_squre(int x) {
    return x * x;
}

int test_function() {
    int x = 2;
    return test_squre(x);
/*         ^^^^^^^^^^
            error: the value of 'x' is not usable in a constant expression
*/
}
```

<br>

上述代码的函数使用到了`consteval`关键字，于是该函数只能在编译期进行计算，当传入的参数不是常量表达式时，`consteval`函数就无法在编译期进行计算。**每次调用`consteval`函数，都必须立即返回编译时常量**。

<br>

### 2.2 if consteval 控制流语句

<br>

`if consteval`本身就是一个条件，它指示`constexpr`函数在编译期计算时运行该分支，如果是运行期计算则运行另一分支（如果存在的话）。

<br>

``` cpp
#include <iostream>

template<typename T>
constexpr int check_eval(T value) {
    if consteval {
        return 1;
    }
    else {
        return 0;
    }
}

int main() {
    constexpr int compile_num = check_eval(3);
    int runtime_num = check_eval(3.2);
    std::cout << compile_num << "\n"
              << runtime_num << "\n";
    return 0;
}
```

<br>

以下三种代码是一致的：

<br>

``` cpp
// 1
if consteval {
    do_something()
}
else {}

// 2
if not consteval {}
else {
    do_somthing()
}

if !consteval {}
else {
    do_something()
}
```

<br>

事实上，我们还可以使用`type_traits`头文件中的`std::is_constant_evaluated()`函数（这在 C++20 中就有）来代替在 C++23 中才有的`if consteval`：

<br>

``` cpp
#include <iostream>
#include <type_traits>

template<typename T>
constexpr int check_eval(T value) {
    if (std::is_constant_evaluated()) {
        return 1;
    }
    else {
        return 0;
    }
}
```

<br>

<br>

<br>

## 3 constinit

<br>

静态存储期的变量和普通的变量一样都会遇到这个问题：
- 变量在编译期被初始化（常量初始化）；
- 变量在运行期被初始化。

<br>

使用`constinit`确保静态存储期和线程存储期的变量在程序编译时就确定其值，注意它不蕴含`const`以及`constexpr`，但是`constexpr`蕴含着`constinit`，但是注意`const constinit`与`constexpr`不是等价的。

<br>

``` cpp
#include <iostream>
#include <type_traits>

template<typename T>
constexpr int check_eval(T value) {
    if (std::is_constant_evaluated()) {
        return 1;
    }
    else {
        return 0;
    }
}

int main() {
    constinit static int test_var = check_eval(0);
    std::cout << test_var << std::endl;     // 1
    return 0;
}
```