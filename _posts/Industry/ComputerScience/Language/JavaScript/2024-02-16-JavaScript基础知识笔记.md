---
title: JavaScript基础知识笔记
author: AshGrey
date: 2024-02-16 00:00:00 +0800
categories: [Coumputer Science, Language]
tags: [Computer Science, JavaScript]
---

> 本文章所属领域：
>
> [实践产业知识 - 工业产业 - 计算机科学 - 编程语言 - JavaScript]({% post_url /Computer Science/2024-02-08-计算机科学：索引笔记 %})
{: .prompt-info}

<br>

## 1 JavaScript基础语法

<br>

### 1.1 在HTML中使用JavaScript

<br>

向HTML中插入JavaScript的主要方法是使用`<script>`元素，`<script>`元素有以下6个属性：
- `async`：表示应该立即下载脚本，但不应妨碍页面中的其他操作，比如下载其他资源或等待加载其他脚本，这只对外部脚本文件有效；
- `charset`：表示通过`src`属性指定的代码的字符集，大多数浏览器会忽略这个值；
- `defer`：表示脚本可以延迟到文档完全被解析和显示之后再执行，只对外部脚本文件有效；
- `language`：已废弃，原来用于表示编写代码使用的脚本语言，大多数浏览器会忽略这个属性；
- `src`：表示包含要执行代码的外部文件；
- `type`：可以看成是`language`的替代属性，表示编写代码使用的脚本语言的内容类型，也称为MIME属性，最常用的值是`text/javascript`。

<br>

``` html
<script type="text/javascript">
    function alertHello() {
        alert("Hi!");
    }
</script>
```

<br>

包含在`<script>`元素内部的JavaScipt代码将被从上至下解释，所以包含`<script>`字符串片段的代码将导致HTML解析错误，可以使用`\`转义字符：

<br>

``` html
<script type="text/javascript">
    function alertScript() {
        alert("<\/script>");
    }
</script>
```

<br>

如果要通过`<script>`元素包含外部JavaScript文件，需要使用`src`属性，这个属性的值指向外部JavaScript文件的链接：

<br>

``` html
<script type="text/javascript" src="example.js"></script>
```

<br>

带有`src`属性的`<script>`元素不应该在其中包含额外的JavaScript代码，因为只会下载并执行外部脚本文件，嵌入的代码会被忽略。`src`属性还可以包含来自外部域的JavaScript文件。无论如何包含代码，只要不存在`defer`和`async`属性，浏览器就会按照`<script>`元素在页面中出现的先后书顺序对它们依次进行解析。

<br>

现代Web应用程序一般都把全部JavaScriptJavaScript引用放在`<body>`元素中页面内容的后面：

<br>

``` html
<!DOCTYPE html>
<html>
  <head>
    <title>Example HTML Page</title>
  </head>
  <body>
    <script type="text/javascript" src="example1.js"></script>
    <script type="text/javascript" src="example2.js"></script>
  </body>
</html>
```

<br>

`<script>`标签的`defer`属性的用途是表明脚本在执行不会影响页面的构造，脚本将延迟到整个页面都解析完毕后再运行。在现实的浏览器中，延迟脚本并不一定按照顺序执行，因此最好只添加一个延迟脚本。

<br>

`<script>`标签的`async`属性的用途是不让页面等待多个脚本下载和执行，从而异步加载页面其他内容。标记为`async`属性的脚本并不保证按照指定它们的先后顺序执行，因此需要确保两者之间互不依赖。

<br>

当用户关闭了浏览器的js脚本解析或者浏览器不支持js脚本解析（这在当代几乎是不可能的事）时，需要使用`<noscript>`标签指明该页面需要浏览器支持或开启脚本解析。`<noscript>`标签内的内容只有在检测到浏览器不能解析js脚本时显示。

<br>

``` html
<html>
  <head>
    <title>Example HTML Page</title>
    <script type="text/javascript" defer src="example1.js"></script>
    <script type="text/javascript" defer src="example1.js"></script>
  </head>
  <body>
    <noscript>
      <p>This page needs javascript.</p>
    </noscript>
  </body>
</html>
```

<br>

### 1.2 JavaScript的特点

<br>

- JavaScript中的一切（变量、函数与操作符）都区分大小写；
- JavaScript中的标识符（变量、函数、属性或函数的参数的名字）只能是按照下列格式规则组合起来的一个或多个字符：
  - 第一个字母必须是一个字母、下划线或者一个美元符；
  - 其他字符可以是字母、下划线、美元符或者数字
- JavaScript使用C风格的注释，即使用`//`进行单行注释，使用`/**/`进行多行注释；
- ECMAScript 5引入了严格模式，为JavaScript定义了一种不同的解析和执行模型，在严格模式下，ECMAScript 3的一些不确定行为将得到处理，对某些不安全的操作也会抛出错误。在整个脚本中启用严格模式，需要在顶部添加编译指示（这和C++的`#`编译指令应该类似）：
    
    ``` javascript
    "use strict";
    ```
    在函数内部的上方包含这个编译指令，也可以指定函数在严格模式下执行：

    ``` javascript
    function doSomething() {
        "use strict";
    }
    ```
- JavaScript中的语句以一个分号结尾，如果省略分号，则由解析器确定语句的结尾。虽然语句结尾的分号不是必须的，但最好不要省略它。这样开发人员才可以通过删除多余的空格来压缩JavaScript代码，代码行结尾处没有分号将导致压缩错误。

<br>

### 1.3 变量

<br>

JavaScript的变量是松散类型的，可以用于保存任何类型的数据。定义变量时要使用`var`操作符，后跟变量名：

<br>

``` javascript
var message;
```

<br>

注意该变量可以保存任何值，未经初始化的变量保存一个特殊值`undefined`。JavaScript支持直接初始化变量，这种初始化变量并不会为变量标记变量类型，可以在修改变量的同时修改值的类型（不建议这样做）：

<br>

``` javascript
var message = "hi";
message = 100;
```

<br>

- 使用`var`操作符定义的变量将成为定义该变量的作用域中的局部变量，即在函数体中使用`var`定义一个变量，则该变量在函数退出后就会被销毁；
- 可以省略`var`操作符创建一个全局变量，但不推荐在局部作用域中定义全局变量，这很难维护；
- 可以使用一条语句定义多个变量，只需要用逗号将变量-值的对分开：

    ``` javascript
    var message = "hi",
        found = false,
        age = 24;
    ```

<br>

### 1.4 数据类型

<br>

JavaScript中有五种简单数据类型：
- Undefined
- Null
- Boolean
- Number
- String

一种复杂类型：
- Object

<br>

JavaScript不支持任何创建自定义类型的机制，所有值都是以上六种数据类型之一。JavaScript提供了一种手段用于检测给定变量的数据类型：`typeof`。对一个变量或者字面量使用`typeof`操作符会返回表示相应变量类型的字符串。

<br>

#### 1.4.1 Undefined

<br>

Undefined类型只有一个值，即特殊的`undefined`，使用`var`声明变量但是不初始化时变量的值为`undefined`，也可以显式初始化变量为`undefined`（但没这个必要）。一个注意点是对未定义和未声明的变量使用`typeof`操作符都会返回`"undefined"`：

<br>

``` javascript
var message;
// var age

alert(typeof message);  // "undefined"
alert(typeof age);      // "undefined"
```

<br>

#### 1.4.2 Null

<br>

Null类型只有一个值，即特殊的`null`。`null`表示一个空对象指针，这也是使用`typeof`操作符检测`null`的值时会返回`"object"`的原因。如果定义的变量准备在将来用于保存对象，那么最好将该变量初始化为`null`而不是其他值。一个注意点是标准规定`undefined`派生自`null`，因此它们的相等性测试返回`true`。

<br>

#### 1.4.3 Boolean

<br>

Boolean类型只有两种字面值：`true`和`false`。可以对任何数据类型的值调用`Boolean()`函数，将返回一个Boolean值。以下是不同类型的变量在经过`Boolean()`函数转换后的值：

<br>

|数据类型|转换为`true`的值|转换为`false`的值|
|:---:|:---:|:---:|
|Boolean|`true`|`false`|
|String|任何非空字符串|`""`（空字符串）|
|Number|任何非零数字值（包括无穷大）|`0`和`NaN`|
|Object|任何对象|`null`|
|Undefined|/|`undefined`|

<br>

#### 1.4.4 Number

<br>

Number类型有整数和浮点数。

- 整数：可以使用十进制整数，也可以用八进制整数（字面量以`0`开头，但是八进制字面量在严格模式下会使引擎抛出错误）和十六进制整数（字面量以`0x`开头，后面的字母可以大写也可以小写）。在进行算术计算时所有以八进制和十六进制表示的数值最终都将被转换为十进制数值；
- 浮点数：浮点数的数值必须有一个小数点，并且小数点后必须至少有一位数字。小数点前可以没有数字，但是建议不省略小数点前的数字。由于保存浮点数需要的内存空间是整数的两倍，因此JavaScript总是会试图将可以转化成整数的浮点数转化为整数，如`1.`和`10.0`等小数点后为`0`的浮点数。对于极大极小的浮点数，可以使用e表示法，等于e前面的数值乘以10的后面数值的指数次幂。浮点数的最高精度是17位小数；
- JavaScript能够表示的最小数值保存在`Number.MIN_VALUE`中，在大多数浏览器中这个数值是`5e-324`；能够表示的最大数值保存在`Number.MAX_VALUE`中，在大多数浏览器中这个数值是`1.7976931348623157e+308`。如果计算的结果得到了一个超出JavaScript数值范围的值，这个值会被自动转换为特殊的`Infnity`值，该值不能参与正常的数值计算。确定一个数字是否是`Infinity`值可以用函数`isFinite()`来确定；
- `NaN`是一个特殊的值，用于表示一个本来要返回数值的操作数并未返回数值的情况。任何涉及`NaN`的操作都会返回`NaN`。`NaN`与任何值都不相等：

    ``` javascript
    concole.log(NaN == NaN);
    // >> false
    ```

    JavaScript定义了函数`isNaN()`用于确定数值是否是`NaN`。

<br>

JavaScript中有三个可以将非数值转换为数值的函数：
- `Number()`

    转型函数`Number()`可以用于任何数据类型，其转换规则如下：
    - `Boolean`类型中的`true`转换为`1`，`false`转换为`0`；
    - 如果是`null`，则转换为`0`；
    - 如果是`undefined`，则转换为`NaN`；
    - 如果是字符串，则其转换规则如下：
        - 如果字符串只包含数字，则转换为对应的十进制数值；
        - 如果字符串中包含有效的浮点格式，则转换为对应的浮点数；
        - 如果字符串中包含有效的**十六进制**格式（八进制格式中的前导0被省略），则转换为对应的十进制数；
        - 如果是空字符串，转换为`0`；
        - 不符合以上格式的字符串转换为`NaN`。
    - 如果是对象，则调用对象的`valueOf()`方法，然后依照前面的规则转换返回的值，如果转换的转换的结果是`NaN`，则调用对象的`toString()`方法