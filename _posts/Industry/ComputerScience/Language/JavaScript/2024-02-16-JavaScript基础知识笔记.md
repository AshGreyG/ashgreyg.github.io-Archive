---
title: JavaScript基础知识笔记
author: AshGrey
date: 2024-02-16 00:00:00 +0800
categories: [Computer Science, Language]
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

包含在`<script>`元素内部的JavaScript代码将被从上至下解释，所以包含`<script>`字符串片段的代码将导致HTML解析错误，可以使用`\`转义字符：

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

现代Web应用程序一般都把全部JavaScript引用放在`<body>`元素中页面内容的后面：

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

JavaScript中有六种简单数据类型：
- Undefined
- Null
- Boolean
- Number
- BigInt
- String

两种复杂类型：
- Object
- Symbol

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
- JavaScript能够表示的最小数值保存在`Number.MIN_VALUE`中，在大多数浏览器中这个数值是`5e-324`；能够表示的最大数值保存在`Number.MAX_VALUE`中，在大多数浏览器中这个数值是`1.7976931348623157e+308`。如果计算的结果得到了一个超出JavaScript数值范围的值，这个值会被自动转换为特殊的`Infinity`值，该值不能参与正常的数值计算。确定一个数字是否是`Infinity`值可以用函数`isFinite()`来确定；
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
    - 如果是`null`，则转换String类型为`0`；
    - 如果是`undefined`，则转换为`NaN`；
    - 如果是字符串，则其转换规则如下：
        - 如果字符串只包含数字，则转换为对应的十进制数值；
        - 如果字符串中包含有效的浮点格式，则转换为对应的浮点数；
        - 如果字符串中包含有效的**十六进制**格式（八进制格式中的前导0被省略），则转换为对应的十进制数；
        - 如果是空字符串，转换为`0`；
        - 不符合以上格式的字符串转换为`NaN`。
    - 如果是对象，则调用对象的`valueOf()`方法，然后依照前面的规则转换返回的值，如果转换的转换的结果是`NaN`，则调用对象的`toString()`方法，然后再转换返回的字符串。
- `parseInt()`
  
    `parseInt()`函数在转换字String类型符串时，更多看字符是否符合数值模式，它忽略字符串前面的空格直至找到第一个非空格字符。如果第一个字符不是数字字符或者负号，返回`NaN`；如果第一个字符是数字字符或者负号，则继续处理后续字符直到遇到非数字字符或者解析完所有字符。对于浮点数`parseInt()`将直接在小数点处截断。

    在ECMAScript 5标准中该函数需要使用第二个参数（转换时使用的基数）才能处理十进制与十六进制以外的数字，此时可以不需要前导的符号。
- `parseFloat()`

    `parseFloat()`函数可以解析任意的浮点数格式，并会省略所有前导0，也会省略第二个小数点以及之后的内容。

<br>

#### 1.4.5 BigInt

<br>

在JavaScript中，Number类型不能表示大于`2^53-1`小于`-(2^53-1)`的整数。超出安全整数范围会出现精度问题。如果有时候需要使用这些超出范围的整数，则需要使用BigInt类型。可以在这些整数后面加上`n`显式指定其为BigInt类型。

<br>

对BigInt类型的所有操作的结果都是BigInt类型，且不能将使用`+`一元操作符将BigInt类型的数值转换为Number类型。

<br>

#### 1.4.6 String

<br>

String类型用于表示由零或多个16位Unicode字符的字符序列，字符串可以用双引号、单引号和反引号括起。反引号引起的字符串可以使用`${}`括起变量名将变量或者表达式的值加到字符串中。

<br>

``` javascript
var name = "Huaier";
console.log(`I love ${name}!`);
```

<br>

JavaScript中有两个可以将其余数值转换为字符串的方法：
- 除了`null`以及`undefined`没有转换成字符串的方法，所有数据类型都有转换成字符串的`toString()`方法，除了Number类型的`toString()`方法可以接受一个表示进制基数的参数，其余数据类型的`toString()`方法都没有参数；
- 转型函数`String()`定义如下：
    - 如果值有`toString()`方法，调用该方法；
    - 如果值是`null`，则返回`"null"`;
    - 如果值是`undefined`，则返回`"undefined"`。

<br>

#### 1.4.7 Object

<br>

JavaScript中的对象是一组数据和功能的集合，对象可以通过执行`new`操作符后跟要创建的对象类型的名称来创建。通过创建Object类型的实例并为其添加属性或者方法可以创建自定义的对象。

<br>

``` javascript
var screen = new Object();
```

<br>

在JavaScript中，Object类型是所有它的实例的基础，Object类型所具有的任何属性和方法也同样存在于更具体的对象中。Object的每个实例都具有以下属性和方法：

<br>

|属性或者方法|说明|
|:---:|:---:|
|`constructor`|保存着用于创建当前对象的函数，对于前面的例子，构造函数就是`Object()`|
|`hasOwnProperty(propertyName)`|用于检查给定的属性在当前对象实例中是否存在，其中属性名`propertyName`<br>必须以字符串形式指定|
|`isPrototyprOf(object)`|用于检查传入的对象是否是当前对象的原型|
|`propertyIsEnumerable(propertyName)`|用于检查给定的属性是否能够使用`for-in`语句|
|`toLocaleString()`|返回对象的字符串表示，该字符串与执行环境的地区对应|
|`toString()`|返回对象的字符串表示|
|`valueOf()`|返回对象的字符串、数值或者布尔值表示|

<br>

### 1.5 操作符

<br>

JavaScript的所有操作符均与C类似，需要的注意点如下：
- `Infinity`与`0`的相关操作：
    - 加法：

        ``` javascript
        // +Infinity = Infinity
        (+Infinity) + (-Infinity) = NaN;
        (+Infinity) + (+Infinity) = +Infinity;
        (-Infinity) + (-Infinity) = -Infinity;
        (+0) + (+0) = +0;
        (+0) + (-0) = +0;
        (-0) + (-0) = -0;
        ```
    - 减法

        ``` javascript
        (+Infinity) - (+Infinity) = NaN;
        (-Infinity) - (-Infinity) = NaN;
        (+Infinity) - (-Infinity) = +Infinity;
        (-Infinity) - (+Infinity) = -Infinity;
        (+0) - (+0) = +0;
        (-0) - (+0) = -0;
        (-0) - (-0) = +0;
        ```
    - 乘法

        ``` javascript
        Infinity * 0 = NaN;
        Infinity * Infinity = Infinity;
        Infinity * x = +Infinity;    // x > 0
        Infinity * y = -Infinity;    // y < 0
        ```
    - 除法

        ``` javascript
        Infinity / Infinity = NaN;
        0 / 0 = NaN;
        Infinity / x = +Infinity;    // x > 0
        Infinity / y = -Infinity;    // y < 0
        ```
    - 求模

        ``` javascript
        Infinity % x = NaN;
        X % 0 = NaN;
        Infinity % Infinity = NaN;
        x % Infinity = x;
        0 % x = 0;  // x !== 0
        ```
- ECMAScript在判断相等方面提供了两组操作符：相等（`==`）和不相等（`!=`），全等（`===`）和不全等（`!==`）。相等和不相等操作符都会对转换操作数的类型然后再比较它们的相等性，转换的规则如下：
    - 如果一个操作数为Boolean类型，将Boolean类型先转换为Number类型；
    - 如果一个操作数是String类型，另一个是Number类型，将String类型转换为Number类型；
    - 如果一个操作数是Object类型，另一个操作数不是，则调用该对象的`valueOf()`方法，再按照前面的规则进行比较；
    - `null`和`undefined`相等；
    - 在比较相等性之前，不得将`null`与`undefined`转换成其他值；
    - 有一个操作数为`NaN`时，相等操作符返回`false`，不相等操作符返回`true`；
    - 两个操作数为对象时，将比较它们是否是同一个对象。
  
    全等和不全等操作符不会对数据进行类型转换，在这里`null === undefined`会返回`false`。
- ECMAScript 有求幂运算符`**`；
- 二元运算符`+`按从左到右的顺序工作，遇到为字符串的操作数以后，它会将前面运算结果转换为字符串，并将后面所有操作数都转换为字符串；而其余运算符都是将字符串转换为数字：

    ``` javascript
    console.log(2 + 2 + "1");   // "41"
    console.log("2" + 2 + 1);   // "221"
    console.log("6"/"3");       // 2
    console.log("5" - "2");     // 3
    ```

- 一元运算符`+`会对非数字强制转换为数字：

    ``` javascript
    var apple = "2";
    var banana = "3";
    console.log((+apple) + (+banana));  // 5
    ```

- 空值合并运算符用于确定一个变量是否是未定义的。当一个值既不是`null`也不是`undefined`时，我们称其为已定义的。空值合并运算符的写法、功能如下：`a ?? b`，如果`a`是已定义的，结果为`a`；如果`a`是未定义的，结果为`b`。它相当于以下代码：

    ``` javascript
    result = (a !== null && a !== undefined) ? a : b;
    ```

`??`与`||`运算符有些类似，但是它的适用范围更小，它可以用于判断一系列变量中第一个已定义的值，对它来说假值只有`null`和`undefiend`，不像`||`会将`0`和`""`也视作假值。`??`和`||`的运算级相同，ECMAScript不允许`??`和`||`或`&&`一起使用，除非有括号明确了求值顺序。

<br>

### 1.6 语句

<br>

JavaScript的分支和循环语句与C/C++基本一致（其中`switch`语句用的是全等和不全等进行判断，不会进行类型转换），不同的是`for-in`循环，JavaScript中的是这样的：

<br>

``` javascript
for (var propName in window) {
  document.write(propName);
}
```

<br>

由于ECMAScript 3中 `for-in`循环中的迭代对象为`null`或`undefined`时会抛出错误，而ECMAScript 5则不执行循环体，为了保证兼容性，应当在`for-in`循环前对`null`和`undefined`进行检验。

<br>

`label`语句可以在代码中添加标签，定义的标签可以由`break`或者`continue`语句引用，例如：

<br>

``` javascript
var num = 0;
START:
for (var i = 0; i < 10; i++) {
  for (var j = 0; j < 10; j++) {
    if ( i === 5 && j === 5)
      break START;
    num++;
  }
}
```

<br>

`with`语句的作用是将代码的作用域设置到一个特定的对象上，定义`with`语句的目的主要是简化多次编写同一个对象的工作，例如：

<br>

``` javascript
/*
 * var qs = location.search.substring(1);
 * var hostName = location.hostname;
 * var url = location.href;
 */

with(location) {
  var qs = search.substring(1);
  var hostName = hostname;
  var url = href;
}
```

<br>

严格模式下不能使用`with`语句，在开发项目过程中也不建议使用`with`语句，大量使用`with`语句将导致性能下降。

<br>

### 1.7 函数

<br>

JavaScript中的函数体不需要函数原型，也不需要定义返回类型（这和变量定义也不需要变量数据类型是一致的），函数需要使用`function{}`包裹函数体：

<br>

``` javascript
function sayHi(name, message) {
  alert("Hello " + name + ", " + message);
}
```

<br>

在JavaScript的函数体内部，参数是以一个数组传递的，在函数内部可以通过访问`arguments`对象来访问这个参数数组，从而获取传递给函数的每一个参数，需要注意的是`arguments`对象并不是`Array`的实例，却可以通过方括号访问参数：

<br>

``` javascript
function sayHi(name, message) {
  alert("Hello " + arguments[0] + ", " + arguments[1]);
}
```

<br>

JavaScript中的函数并不会和C++等语言那样创建一个函数签名，在函数调用的时候必须和函数签名一致，JavaScript的解析器不会验证命名参数，所以函数在定义时一个参数都没有也不妨碍函数调用时被传入多个参数：

<br>

``` javascript
function howManyArgs() {
  console.log(arguments.length);
}
howManyArgs("1", 2, null);
howManyArgs(12, undefined);
```

<br>

一个注意点是，JavaScript中函数内部的参数列表的元素和参数名对应的数值永远保持一致。JavaScript所有参数传递的都是值，不像C++那样可以按引用传递。还要注意的是，由于JavaScript中没有函数签名，函数也无法实现重载，如果定义了多个同名的函数，则该名字只属于后定义的函数。但是JavaScript函数内部可以对`arguments`对象进行访问，于是可以模仿函数重载。

<br>

<br>

<br>

## 2 变量、作用域和内存问题

<br>

### 2.1 基本类型和引用类型的变量

<br>

JavaScript不允许直接对变量的内存地址进行访问或者操作，在操作对象时，实际是操作对象的引用而不是实际的对象（为对象添加属性的时候则是操作实际的对象）。对于引用类型的变量，可以为其添加、改变和删除属性和方法：

<br>

``` javascript
var loli = new Object();
loli.name = "Izumi Sagiri";
console.log(loli.name);
```

<br>

- 基本类型的变量的复制过程：从一个变量向另一个变量复制基本类型的值，会在变量对象上创建一个新值，然后把该值复制到为新变量分配的位置上；
- 引用类型的变量的复制过程：从一个变量向另一个变量复制引用类型的值，会创建一个实际为指针的副本，这个指针指向存储在堆中的一个对象。复制操作结束后，两个变量实际上将引用同一个对象，改变其中一个变量，会对另一个变量产生影响。

<br>

若向函数传递一个基本类型，会发生基本类型变量的复制过程；若向函数传递一个引用类型，同样会发生引用类型变量的复制过程，在函数内部，函数内部对象和传入对象引用的是同一个对象：

<br>

``` javascript
function changeObjectName(obj) {
  obj.name = "Izumi Sagiri";
}
var loli = new Object();
changeObjectName(loli);
console.log(loli.name); // -> "Huaier"
```

<br>

为了说明函数传递的参数是按值传递而不是按引用传递，可以给出以下例子：

<br>

``` javascript
function changeObjectName(obj) {
  obj.name = "Huaier";
  obj = new Object();
  obj.name = "AshGrey";
}
var loli = new Object();
changeObjectName(loli);
console.log(loli.name); // -> "Huaier"
```

<br>

如果函数是按引用传递的，则不管函数内部的对象`obj`是否被重新赋值为一个新的对象，它的引用都应该是外部对象`loli`，而这里`loli`的值没有因为`obj`的重新定义而改变，就说明了函数是按值传递的。函数内部的变量、对象都会在函数体结束调用时被销毁。

<br>

### 2.2 执行环境和作用域

<br>

每个执行环境都有一个与之关联的变量对象，环境中定义的所有变量和函数都保存在这个对象中，虽然编写的代码无法访问这个对象，但是JavaScript的解析器在处理数据时会在后台使用它。

<br>

全局执行环境是最外围的执行环境，根据ECMAScript实现所在的宿主环境不同，表示执行环境的对象也不同。在Web浏览器中，全局执行环境默认是`window`对象，因此所有全局变量和函数都是作为`window`对象属性和方法创建的。

<br>

每个函数都有自己的执行环境，当执行流进入一个函数时，函数的环境就会被推入一个环境栈中。而在函数执行之后，栈将其环境弹出，把控制权返回给以前的执行环境。会创建变量对象的一个**作用域链**，作用域链的用途是保证对执行环境有权访问的所有变量和函数的有序访问。作用域的前端始终都是当前执行的代码所在环境的变量对象。

<br>

标识符解析是沿着作用域一级一级地搜索标识符的过程，搜索过程始终从作用域链的前端开始，然后逐级地向后回溯，直至找到标识符为止，如果找不到标识符，通常会导致错误发生。

<br>

``` javascript
var blueColor = "blue";
function ChangeColor() {
  var redColor = "red";
  function SwapColors() {
    var tempColor = redColor;
    redColor = blueColor;
    blueColor = tempColor;
  }
  SwapColors();
}
ChangeColor();
console.log(blueColor);
// Environment: window -> ChangeColor() -> SwapColors()
```

<br>

排在前面的环境可以使用排在后面的环境中的变量，排在后面的环境无法使用排在前面中的变量。

<br>

较老的JavaScript没有块级作用域，这意味着流控制语句中的变量将被添加到当前的执行环境：

<br>

``` javascript
var sum = 0;
for (var i = 1; i <= 10; i++) {
  sum += i;
  console.log(sum);
}
console.log("Final result: " + sum);
```

<br>

使用`var`声明的变量会自动被添加到最接近的环境中，如果初始化变量时没有使用关键字`var`声明，该变量会被自动添加到全局环境中。在较老的脚本中大部分还是使用`var`的，但是在现代JavaScript中，常常使用`let`关键字，`let`与`var`的最大不同是其具有块级作用域：

<br>

``` javascript
let numGroup = [12, 23, 15, 6, 10];
let max = 0;
for (let i = 0; i <= numGroup.length - 1; i++) {
  if (numGroup[i] > max)
    max = numGroup[i];
}
console.log(max);   // 23
console.log(i);     // undefined variable
```

<br>

可以看到`let`声明的变量`i`在离开循环之后就被销毁了，现代JavaScript的`let`具有了块级的作用域。注意`let`不能重复声明同一个名字的变量，否则会发生错误。如果想要保证变量的值不会被修改，可以使用`const`关键字声明变量。

<br>

### 2.3 垃圾收集

<br>

JavaScript具有垃圾收集机制，执行环境会负责管理代码执行过程中使用的内存。垃圾收集器会按照固定的时间间隔周期性地执行收集垃圾的操作。对于函数体内的局部变量，它们只在函数执行的过程中存在，在函数执行时，会为局部变量在栈或者堆上分配相应的空间。垃圾收集器将跟踪这些变量，对于不再有用的变量将会执行变量的销毁。垃圾收集器的实现算法如下：
- **标记清除**：当变量进入环境时，变量被标记为「进入环境」；当变量离开环境时，则被标记为「离开环境」。垃圾收集器在运行的时候会给存储在内存中的所有变量都加上标记，接着去掉环境中的变量以及被环境中的变量引用的变量的标记，剩下的带有标记的变量就是待删除的变量；
- **引用计数**：引用计数是指跟踪记录每个值被引用的次数。当声明了一个变量并将一个引用类型值赋给该变量时，这个引用类型值的引用次数就加一；当引用该值的变量又取得了另外一个值，则这个引用类型值的引用次数减一。当一个变量的引用次数为`0`时，说明没有办法再访问该值，可以将其占用的内存空间回收，引用计数一个重要的问题时可能存在循环引用：

    ``` javascript
    function problem() {
      var objectA = new Object();
      var objectB = new Object();
      objectA.friend = objectB;
      objectB.friend = objectA;
    }
    ```

    此时变量`objectA`和`objectB`的引用次数永远为`2`，如果只采用引用计数的方式回收垃圾将导致即使退出了函数体，这两个变量都无法被回收。如果这个函数被大量调用，内存就会被不断占用直到浏览器崩溃。

<br>

<br>

<br>

## 3 引用类型

<br>

### 3.1 Object类型

<br>

#### 3.1.1 对象的创建

<br>

除了使用`objectA.propertyName = `这样的添加属性或者方法的模式，还可以使用对象字面量表示法（当花括号内留空，则对象只包含默认属性和方法）：

<br>

``` javascript
let wife = {
  name    : "Huaier",
  husband : "AshGrey"
};
```

<br>

访问对象的属性和方法可以使用点表示法，也可以使用中括号表示法，中括号表示法用在对象的属性使用的是关键字或保留字，或者属性名中包含会导致语法错误的字符：

<br>

``` javascript
loli["first name"] = "Izumi";
loli["function"] = 2;
```

<br>

JavaScript的对象即使属性不存在也不会报错，读取不存在的属性只会得到`undefined`。可以使用`in`关键字来检查对象是否具有某个名字的属性：

<br>

``` javascript
let objectA = {
  name : "Huaier",
  age  : 17
};
console.log("name" in objectA);     // true
console.log("height" in objectA);   // false
```

<br>

需要注意的是使用`const`声明的对象中的属性可以被改变，即使在严格模式下也不会报错。

<br>

#### 3.1.2 对象的复制

<br>

之前说过对象的赋值只是将两个对象都指向了同一个地址，如果想做到完全的复制而不是指向同一个地址，可以采用遍历已有对象的属性并在原始类型值的层面复制它们，或者使用`Object.assign`方法：

<br>

``` javascript
let objectC = {
  name : "AshGrey",
  age  : 19
};
let objectD = {};
for (let prop in objectC) {
  objectD[prop] = objectC[prop];
}
```

<br>

`Object.assign`方法的语法是：

<br>

``` plaintext
Object.assign(dest, [src1, src2, src3...])
```

<br>

第一个参数`dest`是目标对象，后面方括号内的参数是源对象。该方法将所有源对象的属性拷贝到目标对象中，调用结果返回`dest`。可以用这个方法将多个对象进行合并，如果被拷贝的属性的属性名已经存在，那么原先的属性会被覆盖：

<br>

``` javascript
let user = { name : "AshGrey" };
let permission1 = { canEdit : true};
let permission2 = { canSave : true};
user = Object.assign(user, permission1, permission2);
console.log(user.canEdit);  // true
console.log(user.canSave);  // true
```

<br>

当对象内部还有用对象初始化的变量时，必须使用深拷贝的方式进行复制，而深拷贝则需要使用递归的方式进行复制：

<br>

``` javascript
let player = {
  name   : "AshGrey",
  age    : 19,
  school : {
    primary    : "A",
    junior     : "X",
    senior     : "Y",
    university : "T"
  }
};

function copyObject(dest, src) {
  for (let prop in src) {
    if (typeof prop === "object") {
      return arguments.callee(dest[prop], src[prop]);
    }
    else {
      dest[prop] = src[prop];
    }
  }
  return dest;
}
let anotherPlayer = copyObject({}, player);
console.log(anotherPlayer.school.junior);
```

<br>

#### 3.1.3 构造函数

<br>

当需要大批量构造同种对象时，可以使用构造函数（与C++不同，这里的构造函数在技术上是正常的函数），构造函数使用`new`关键字调用，相当于进行了隐式创建对象和隐式返回的过程，故构造函数内部并没有`return`关键字：

<br>

``` javascript
function User(name) {
  this.name = name;
  this.isAdmin = false;
}
let user = new User("AshGrey");
```

<br>

#### 3.1.4 可选链

<br>

可选链这一语法糖主要解决访问嵌套对象属性的安全问题，即使中间属性不存在也不会出现错误。假设这样一个场景，网站需要保存用户输入的姓名，但是有用户并没有提供任何姓名并恰巧网站没有检测空输入，则可能出现以下情况：

<br>

``` javascript
let user = {};  // user doesn't have name
console.log(user.name.lastName);    // Error!
```

<br>

在大多数情况下，我们期望得到的是`undefined`而不是一个错误。在可选链出现之前也有一些解决方法，例如在访问该值的属性之前，使用`if`或者条件运算符`?`对值进行检查。可以看到`user.name`出现了两次，对于名称较长的属性，重复两次将加大代码量，不够优雅，于是可选链的语法糖被提出：

<br>

``` javascript
let user = {};
console.log(user.name ? user.name.lastName : undefined);
console.log(user?.name?.lastName);
```

<br>

可选链`?.`前的属性为`undefined`或者`null`时，它将返回`undefined`并结束继续调用。注意，不要过度使用可选链，只将它用在一些属性可以不存在的地方。`?.`并不是一个运算符，而是一种特殊的语法结构，它可以与函数和方括号一起使用：`?.()`用于调用一个可能不存在的函数，`?.[]`用于使用方括号读取可能不存在的对象的属性：

<br>

``` javascript
let userAdmin = {
  admin() {
    console.log("$root>");
  }
}

let userGuest = {};
userGuest.admin?.();
userAdmin.admin?.();
```

<br>

### 3.2 Array类型

<br>

JavaScript中的数组是Array类型，该数组虽然是数据的有序列表，但与其他编程语言不同的地方在于JavaScript的数组的每一项可以保存任何类型的数据。创建数组主要有两种方法：
- 使用`Array`构造函数，其中`new`关键字可用可不用。当向构造函数传递一个Number类型的参数时，数组的大小将被设定为该Number类型的值；当向构造函数传递一个非Number类型的参数或者传递多个参数时，相当于传递给数组了具体的元素；

    ``` javascript
    let names = new Array(3);           // length = 3
    let names = new Array("Izumi");     // names[0] = "Izumi"
    let names = new Array(3, "Izumi");  // names[0] = 3, names[1] = "Izumi"
    ```

- 使用数组字面量表示法：

    ``` javascript
    let names = [3, "Izumi"];
    ```

<br>

可以通过设置数组的`length`属性动态地设置数组的大小。

<br>

确定一个对象是不是数组，在只有一个全局执行环境下时可以使用`instanceof`方法。但如果网页中含有多个框架，可能实际上存在多个全局执行环境，不同的执行环境之间`Array`的构造函数可能不同，使用`instanceof`方法去检测是有问题的。这时候可以使用ECMAScript 5中的`isArray()`方法，这是检测一个对象是否为数组的最终方法。

<br>

所有对象都具有`toLocaleString()`、`toString()`和`valueOf()`方法。
- 调用`valueOf()`方法返回的是数组本身；
- 调用`toString()`方法会返回数组中每个值的字符串形拼接而成的一个以逗号分隔的字符串；
- 调用`toLocaleString()`方法返回的值与`toString()`相同，虽然过程并不一样。

<br>

``` javascript
let loli = ["IzumiSagiri", "Elaina", "Charolotte Soller"];
console.log(loli);                    // Array(3) : ["IzumiSagiri", "Elaina", "Charolotte Soller"]
console.log(loli.valueOf());          // Array(3) : ["IzumiSagiri", "Elaina", "Charolotte Soller"]
console.log(loli.toString());         // "IzumiSagiri,Elaina,Charolotte Soller"
console.log(loli.toLocaleString());   // "IzumiSagiri,Elaina,Charolotte Soller"
```

<br>

可以调用数组的`join()`方法改变`toString()`中的分隔符号，如果不给`join()`函数传递参数或者传递`undefined`，将使用逗号分隔。

<br>

JavaScript为数组提供了一些模仿数据结构的方法：
- 栈方法：数组可以表现地像栈一样，栈是一种LIFO（后进先出）的数据结构，即最新添加的项最早被移除，栈中项的插入和移除都发生在栈的前端，模仿的方法是使用`push()`方法（插入或者压入项）和`pop()`方法（弹出或者移除项）；
- 队列方法：数组可以表现地像队列方法，队列是一种FIFO（先进先出）的数据结构，队列中项的插入发生在队列后端，项的移除发生在队列前端，模仿的方法是结合使用`shift()`方法（移除数组的第一个项）和`push()`方法。数组还有一个`unshift()`方法，它与`shift`方法相反，向数组的前端插入项。

<br>

JavaScript为数组提供了重排序的方法，其中`reverse()`反转数组的顺序，`sort()`将数组中所有元素使用`toString()`方法然后进行比较，默认升序。

<br>

``` javascript
let values = [0, 1, 5, 10, 15];
values.sort();
console.log(values);  // Array(5) : [0, 1, 10, 15, 5]
```

<br>

`sort()`方法可以接受一个比较函数作为参数，以便指定哪个值位于哪个值的前面。比较函数接受两个参数，如果第一个参数应该位于第二个参数之前，应当返回一个负数，如果相等则应返回0，如果第一个参数应该位于第二个参数之后，应当返回一个正数。

<br>

``` javascript
function compare(value1, value2) {
  if (value1 < value2) {
    return -1;
  }
  else if (value1 > value2) {
    return 1;
  }
  else {
    return 0;
  }
}

let values = [0, 10, 5, 4, 12];
console.log(values.sort(compare));  // Array(5) : [0, 4, 5, 10, 12]

console.log(values.sort((a, b) => a - b));
```

<br>

这样就可以覆盖原来的按照字符串排序的方法，实现数字间的比较。

<br>

JavaScript给数组提供了一些操作方法：
- `concat()`方法基于当前数组中的所有项创建一个新的数组，这个方法会先创建当前数组的一个副本，并将接收到的参数添加到副本的末尾，最后返回新构建的数组；

    ``` javascript
    let loli_1 = ["Izumi", "Sagiri"];
    let loli_2 = loli_1.concat("Elaina", ["Charolotte", "Soller"]);
    console.log(loli_1.toString());  // "Izumi,Sagiri"
    console.log(loli_2.toString());  // "Izumi,Sagiri,Elaina,Charolotte,Soller"
    ```

- `slice()`方法基于当前数组的一个或多个项创建一个新数组：
    - 接受1个参数时，`slice()`方法返回从参数指定位置（注意都是索引值）开始到当前数组末尾的所有项；
    - 接受2个参数时，`slice()`方法返回起始和结束位置之间的项，但不包括结束位置的项，该操作方法不会修改原始数组。

    ``` javascript
    let loli_1 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller"];
    let loli_2 = loli_1.slice(1);
    let loli_3 = loli_1.slice(2, 4);
    console.log(loli_2.toString());  // "Sagiri,Elaina,Charolotte,Soller"
    console.log(lolis_3.toString());  // "Elaina,Charolotte"
    ```

- `splice()`方法有多种用法，主要用途是向数组的中部插入项：
    - 删除：可以删除任意数量的项，需要指定2个参数，第一个是要删除的第一项的位置，第二个是要删除的项数；
    - 插入：可以向指定位置插入任意数量的项，第一个参数为起始位置，第二个参数为删除的项数（这里要选择0），后续参数就是要插入的项；
    - 替换：可以向指定位置插入任意数量的项，且同时删除任意数量的项，用这种方法可以做到项的替换。

    ```javascript
    let lolis_1 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller"];
    let lolis_2 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller"];
    let lolis_3 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller"];
    lolis_1.splice(1, 2);
    lolis_2.splice(1, 0, "AshGrey");
    lolis_3.splice(1, 2, "AshGrey", "Helix");
    console.log(lolis_1.toString());  // "Izumi,Charolotte,Soller"
    console.log(lolis_2.toString());  // "Izumi,AshGrey,Sagiri,Elaina,Charolotte,Soller"
    console.log(lolis_3.toString());  // "Izumi,AshGrey,Helix,Charolotte,Soller"
    ```

<br>

JavaScript还为数组提供了位置的方法：`indexOf()`和`lastIndexOf()`方法，两个方法都接受两个参数：要查找的项和（可选的）表示查找起点位置的索引。`indexOf()`方法从数组的开头往后查找，`lastIndexOf()`方法从数组的末尾往前查找。这两个函数都会返回查找参数在数列中的索引值，未查找到时两个函数都会返回-1，注意这两个函数在查找过程中的比较方法都是全等：

<br>

``` javascript
let lolis_1 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller", "Sagiri"];
console.log(lolis_1.indexOf("Sagiri"));       // 1
console.log(lolis_1.lastIndexOf("Sagiri"));   // 5
console.log(lolis_1.indexOf(1));              // -1
```

<br>

JavaScript还为数组提供了5个迭代的方法，每个方法都接受两个参数，要在每一项上运行的函数和（可选的）运行该函数的作用域对象。传入这些方法的函数会接受三个参数：数组的项、该项在数组中的位置和数组对象本身：
- `every()`方法：对数组的每一项都执行给定的函数，如果给定函数对每一项都返回`true`（或者返回值经过强制类型转换得到`true`），则函数本身返回`true`，否则返回`false`；

    ``` javascript
    let loliAge = [12, 14, 9, 13, 15];
    let result = loliAge.every(function(item, index, array) {
      if (item >= 14)
        return true;
      else 
        return false;
    });
    console.log(result);  // false
    ```

- `some()`方法：迭代执行，如果给定函数对某一项返回了`true`（或者返回值经过强制类型转换得到`true`），则函数本身返回`true`，否则返回`false`：

    ``` javascript
    let loliAge = [12, 14, 9, 13, 15];
    let result = loliAge.some(function(item, index, array) {
      if (item >= 14)
        return true;
      else 
        return false;
    });
    console.log(result);  // true
    ```

- `filter()`方法：迭代执行，函数返回的是一个数组，数组内只包含使给定函数返回`true`（或者返回值经过强制类型转换得到`true`）的数组元素：

    ``` javascript
    let loliAge = [12, 14, 9, 13, 15];
    let result = loliAge.filter(function(item, index, array) {
      if (item >= 14)
        return true;
      else 
        return false;
    });
    console.log(result);  // Array(2) : [14, 15]
    ```

- `forEach()`方法：迭代执行，该函数没有返回值：

    ``` javascript
    let loliAge = [12, 14, 9, 13, 15];
    let result = new Array();
    loliAge.forEach(function(item, index, array) {
      function format(n) {
        if (n === 0)
          return 1;
        else
          return n;
      }
      if (item >= 14)
        result.splice(format(result.length), 0, "Hentai!");
      else 
        result.splice(format(result.length), 0, "Law!");
    });
    console.log(result);  // Array(5) : ["Law!", "Hentai!", "Law!", "Law!", "Hentai!"]
    ```

- `map`方法：迭代执行，返回每次函数调用的结果组成的数组：

    ``` javascript
    let loliAge = [12, 14, 9, 13, 15];
    let result = loliAge.map(function(item, index, array) {
      if (item >= 14)
        return "Hentai!";
      else
        return "Law!";
    });
    console.log(result);  // Array(5) : ["Law!", "Hentai!", "Law!", "Law!", "Hentai!"]
    ```

<br>

### 3.3 Date类型

<br>

JavaScript中的Date类型是在早期Java中的`java.util.Date`类基础上构建的，因此Date类型使用自UTC（国际协调时间）1970年1月1日0时开始经过的毫秒数来保存日期，Date类型保存的日期能够精确到1970年1月1日前后的`1e8`年。创建Date类型对象和其他创建对象的方法相似：

<br>

``` javascript
let Today = new Date();
```

<br>

调用Date构造函数而不传递参数的情况下，新创建的对象将自动获取当前的日期和时间。如果想根据特定的日期和时间创建对象，必须传入表示该日期的毫秒数。JavaScript提供两种方法：
- `parse()`方法：接受一个表示日期的字符串参数，然后尝试根据这个字符串返回相应日期的毫秒数，接受的字符串参数因实现的不同而不同（如果字符串参数无法表示日期，函数将返回`NaN`）；

    ``` javascript
    let day_1 = new Date(Date.parse("6/13/2004"));
    let day_2 = new Date(Date.parse("January 12,2006"));
    let day_3 = new Date(Date.parse("Tue May 25 2004 23:21:09 GMT+0800"));
    let day_4 = new Date(Date.parse("2022-02-03T23:09:00"));
    ```

- `UTC()`方法：UTC的参数分别是年份、基于0的月份（一月份是0，二月份是1）、月中的哪一天、小时数、分钟、秒及毫秒。

<br>

### 3.4 RegExp类型

<br>

JavaScript支持通RegExp类型来支持正则表达式。其语法如下：

<br>

``` plaintext
let expression = / pattern / flags
```

<br>

其中`pattern`指的是任何正则表达式，`flags`表示一个或多个标志用以表明正则表达式的行为，正则表达式的匹配模式支持下列三种标志：
- `g`：表示全局模式，即模式将被应用于所有字符串，而非在发现第一个匹配项时立即停止；
- `i`：表示不区分大小写模式，在确定匹配项时忽略模式与字符串的大小写；
- `m`：表示多行模式，即在到达一行文本末尾时还会继续查找下一行中是否存在与模式匹配的项。

<br>

#### 3.4.1 正则表达式的语法

<br>

正则表达式的元字符如下（普通字符通过字面意思理解，元字符是特殊的功能字符）：
- 量词：
  - `*`：匹配前面的模式零次或多次；
  - `+`：匹配前面的模式一次或多次；
  - `?`：匹配前面的模式零次或一次；
  - `{n}`：匹配前面的模式恰好`n`次；
  - `{n,}`：匹配前面的模式至少`n`次；
  - `{n,m}`：匹配前面的模式至少`n`次且不超过`m`次。
- 字符类：
  - `[]`：匹配中括号内的任意一个字符；
  - `[^]`：匹配除了括号内的字符以外的任意一个字符；
  - `[0-9]`：匹配任意一个数字；
  - `[a-z]`与`[A-Z]`：匹配任意一个小写字母，匹配任意一个大写字母；
  - `.`：匹配除了换行符`\n`、`\r`之外的任何单个字符；
  - `\s`：匹配所有空白字符；
  - `\S`：匹配所有非空白字符；
  - `\w`：匹配字母、数字、下划线；
  - `\d`：匹配任意一个阿拉伯数字，等价于`[0-9]`；
  - `()`：用圆括号将所有选择项括起来，相邻的选择项之间用`|`分隔，`()`表示捕获分组，会把每个分组里匹配的值保存起来，多个匹配值可以通过数字`n`来查看，`n`表示第`n`个捕获组的内容。圆括号的这种功能使得相关的匹配被缓存，可以使用`?:`放在第一个选项前消除这种副作用，它是非捕获元之一；
    - `?:`：消除圆括号将匹配的字符缓存的副作用；
    - `?=`：表达式`exp1(?=exp2)`用以查找紧跟在`exp2`前面的`exp1`；
    - `?<=`：表达式`(?<=exp2)exp1`用以查找紧跟在`exp2`后面的`exp1`；
    - `?!`：表达式`exp1(?!exp2)`用以查找后面不是`exp2`的`exp1`；
    - `?<!`：表达式`(?<!exp2)exp1`用以查找前面不是`exp2`的`exp1`。

    圆括号缓存的缓冲区最多能存储99个捕获的子表达式，可以通过`\`加一个一位或两位十进制数字访问缓存的子表达式：

    ``` javascript
    let str = "Is is the cost of gasoline going up up";
    let pattern1 = /\b([a-z]+) \1\b/igm;
    console.log(str.match(pattern1));
    // Array(3) : ["Is is", "of of", "up up"]
    ```

- 边界匹配：
  - `^`：匹配字符串的开头；
  - `$`：匹配字符串的结尾；
  - `\b`：匹配单词的边界；
  - `\B`：匹配非单词的边界

<br>

`*`和`+`量词符都是贪婪的，它们会尽可能多的匹配文字，只要在它们后面加上一个`?`就可以实现最小匹配，例如：

<br>

``` plaintext
Text     : <h1>AshGrey Blog</h1>
pattern1 : /<.*>/   =>  <h1>AshGrey Blog</h1>
pattern2 : /<.*?>/  =>  <h1>
pattern3 : /<\w+?>/ =>  <h1>
```

<br>

#### 3.4.2 JavaScript中的正则表达式

<br>

以上是用字面量形式定义的正则表达式，另一种创建正则表达式的方法是是用RegExp构造函数，它接受两个参数：第一个是匹配的字符串模式，第二个是可选的标志。

<br>

``` javascript
let pattern1 = /[bc]at/i;
let pattern2 = new RegExp("[bc]at", "i");
```

<br>

需要注意的是由于RegExp构造函数的参数是字符串，所以需要对元字符双重转义（因为字符`\`需要被转义，所以会变成`\\`），又因为`\`在正则表达式中需要被转义成`\\`，所以在RegExp构造函数中的字符串又被转义为`\\\\`。如下：

<br>

``` javascript
let pattern1 = /\[bc\]at/;  // RegExp("\\[bc\\]at")
let pattern2 = /\.at/;      // RegExp("\\.at")
let pattern3 = /name\/age/; // RegExp("name\\/age")
let pattern4 = /\d.\d{1,2}/;// RegExp("\\d.\\d{1,2}")
```

<br>

RegExp类型的每个实例都具有下列属性：
- `global`：Boolean类型，表示是否设置了g标志；
- `ignoreCase`：Boolean类型，表示是否设置了i标志；
- `multiline`：Boolean类型，表示是否设置了m标志；
- `lastIndex`：Number中的整数类型，表示开始搜索下一个匹配项的字符位置，从0算起；
- `source`：正则表达式的字符串形式，按照字面量形式而不是传入构造函数中的字符串模式。

<br>

RegExp类型的每个实例都具有下列方法：
- `exec()`：该方法接受一个参数，即要应用模式的字符串，然后返回包含第一个匹配项信息的数组，在没有匹配项的情况下返回`null`，返回的数组虽然是Array的实例，但包含两个额外的属性：`index`和`input`。其中`index`表示匹配项在字符串的位置，`input`表示应用正则表达式的字符串；

    ``` javascript
    let str = "67root@123root12-90";
    let pattern = /(?<=\d+)root/igm;
    let matches1 = pattern.exec(str);
    console.log(matches1.index);
    let matches2 = pattern.exec(str);
    console.log(matches2.index); 
    ```

    即使正则表达式设置了g标志，`exec()`方法每次也只能返回一个匹配项。在不设置g标志时，`exec()`方法每次调用都只能返回第一个匹配项；在设置g标志时，`exec()`方法每次调用都会继续查找新的匹配项；
- `test()`：该方法接受一个字符串参数，当与正则表达式匹配时，返回`true`，不匹配时返回`false`。

<br>

### 3.5 Function类型

<br>

#### 3.5.1 函数表达式

<br>

在JavaScript中，函数实际是一种对象。每个函数都是Function类型的实例，函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定，于是函数的定义也可以这么写：

<br>

``` javascript
let sum = function(num1, num2) {
  return num1 + num2;
};

// let sum = new Function("num1", "num2", "return num1 + num2");
let result1 = sum(1, 2);
let anotherSum = sum;
sum = null;
let result2 = anotherSum(1,2);
```

<br>

函数表达式与函数声明是有区别的，解析器在向执行环境中加载数据时，对函数声明和函数表达式的加载顺序是有先后的。解析器会率先读取函数声明，并使其在执行任何代码之前可用；而函数表达式则必须等到解析器执行到它所在的代码行才会被真正解析：

<br>

``` javascript
haveHoisted();
function haveHoisted() {
  console.log("I love Huaier!");
}

notHoisted();   // TypeError: notHoisted is not a function
let notHoisted = function() {
  console.log("I love Huaier!");
};
```

<br>

在代码开始执行之前，解析器就已经通过一个名为函数声明提升的过程，读取并将函数声明添加到执行环境中。对代码求值时，JavaScript引擎会首先声明函数并将它们放在源代码树的顶端。对于函数表达式，引擎就不会做出这样的函数声明提升。

<br>

#### 3.5.2 箭头函数

<br>

箭头函数有一个`=>`的运算符，所以被命名为箭头函数，它的基本格式如下：

<br>

``` javascript
let func = (arg1, arg2, ..., argN) => expression;
```

<br>

这里创建了一个函数`func`，它接受参数`arg1,...,argN`，然后使用参数对右侧的`expression`求值并返回结果。箭头函数对于简单的单行行为函数来说非常方便。如果需要多行的箭头函数，则需要显式地使用`return`：

<br>

``` javascript
let sum = (a, b) => {
  let result = a + b;
  return result + " = " + a + " + " + b;
}
console.log(sum(2, 3)); // "5 = 2 + 3"
```

<br>

#### 3.5.3 函数对象的属性与方法

<br>

由于函数本身是一种对象，函数也能作为参数传入别的函数中。例如有`object1`和`object2`是一个类的两个对象，现在要根据两个对象的某个属性进行排序：

<br>

``` javascript
function createComparisonFunction(propertyName) {
  return function(object1, object2) {
    let value1 = object1[propertyName];
    let value2 = object2[propertyName];
      if (value1 < value2) {
        return -1;
      }
      else if (value1 > value2) {
        return 1;
      }
      else {
        return 0;
      }
    };
}

let data = [
  { name : "AshGrey", age : 19},
  { name : "Sagiri" , age : 14}
];
data.sort(createComparisonFunction("name"));
console.log(data[0].name);  // AshGrey
data.sort(createComparisonFunction("age"));
console.log(data[0].name);  // Sagiri
```

<br>

函数内部有三个特殊的对象：
- `arguments`对象：是特殊的类数组对象，这个对象还具有一个属性`callee`，该属性是一个指针，指向拥有这个`arguments`对象的函数。例如下面的使用到递归的阶乘函数，如果使用注释里的语句，函数的执行将与该函数的名`factorial`紧密耦合在一起，使用`arguments.callee`则可避免这种问题：

    ``` javascript
    function factorial(num) {
      if (num <= 1) {
        return 1;
      }
      else {
        return num * arguments.callee(num - 1);
        // return num * factorial(num - 1);
      }
    }
    let anotherFactorial = factorial;
    factorial = null;
    console.log(anotherFactorial(5));
    ```

- `this`对象：引用的是函数执行的环境对象，例如在网页中全局作用域调用函数时`this`对象引用的是`window`对象。如果在对象内部定义函数，则函数的`this`指向的就是对象。

    ``` javascript
    window.color  = "red";
    let object = { color : "blue"};
    function colorOutput() {
      console.log(this.color);
    }
    colorOutput();  // "red"
    object.colorOutput = colorOutput;
    object.colorOutput();   // "blue"
    ```

    需要注意的是，箭头函数没有`this`，如果在箭头函数中调用`this`，其值取决于外部的函数：

    ``` javascript
    let hello = {
      name : "AshGrey",
      sayHello() {
        let arrow = () => console.log("Hello, " + this.name);
          arrow();
      }
    };
    hello.sayHello();   // "Hello, AshGrey"
    ```

- `caller`对象：这个对象中保存着调用当前函数的函数的引用，如果在全局作用域调用当前函数，`caller`的值为`null`

    ``` javascript
    function outer() {
      inner();
    }
    function inner() {
      console.log(arguments.callee.caller);
      alert(arguments.callee.caller);
    }
    inner();    // null
    outer();    // function : outer()
                // function outer() {
                //     inner();
                // }
    ```

    在严格模式下，访问`arguments.callee`会导致出错。ECMAScript 5还定义了`arguments.caller`属性，非严格模式下这个属性的值始终是`undefined`。

<br>

函数本身是一个对象，因此也有自己的属性和方法。每个函数都有两个属性：
- `length`：表示函数希望接受的命名参数的个数；
- `prototype`：所有引用类型的`toString()`、`valueOf()`方法都保存在`prototype`名下，只不过是通过各自对象的实例访问。在 ECMAScript 5 中，`prototype`属性是不可枚举的，不能使用`for-in`发现。

<br>

每个函数都有两个非继承得到的方法`apply()`和`call()`，这两个方法都是在特定的作用域中调用参数，实际上等同于设置函数体内`this`对象的值：
- `apply()`方法接受两个参数：第一个是在其中运行函数的作用域，第二个是参数数组。参数数组可以是Array类型的实例，也可以是`arguments`对象。例如

    ``` javascript
    window.flag = "window";
    window.order = 0;
    let callObject = {
      flag  : "Obejct",
      order : 1
    };
    function testFunction(num, str) {
      console.log("Flag : " + this.flag);
      console.log("Order : " + this.order);
      console.log(str + " : " + num);
      return 0;
    }
    function callFunction(age, name) {
      callFunction.flag = "Function";
      callFunction.order = 2;
      if (age >= 18)
        return testFunction.apply(this, arguments);
      else if (age >= 14)
        return testFunction.apply(callFunction, [age, name]);
      else
        return "Hentai!"
    }
    callFunction(19, "loliAge");
    /*
     *  Flag    : window
     *  Order   : 0
     *  loliAge : 19
     */
    callObject.callFunction = callFunction;
    callObject.callFunction(19, "loliAge");
    /*
     *  Flag    : Object
     *  Order   : 1
     *  loliAge : 19
     */
    callObject.callFunction(16, "loliAge");
    /*
     *  Flag    : Function
     *  Order   : 2
     *  loliAge : 16
     */
    ```

- `call()`方法与`apply()`相似，只是传递参数时需要逐个传递：

    ``` javascript
    function callFunction(age, name) {
      callFunction.flag = "Function";
      callFunction.order = 2;
      if (age >= 18)
        return testFunction.call(this, age, name);
      else if (age >= 14)
        return testFunction.call(callFunction, age, name);
      else
        return "Hentai!"
    }
    ```

<br>

ECMAScript 5 还定义了一个方法：`bind()`，这个方法会创建一个函数的实例，其`this`值会绑定到传给`bind()`函数的值：

<br>

``` javascript
window.loli = "Izumi Sagiri";
let second = {
  loli : "Elaina"
}
function outputloli() {
  console.log(this.loli);
}
let objectOutputloli = outputloli.bind(second);
objectOutputloli(); // Elaina
```

<br>

函数继承的`toLocaleString()`、`toString()`和`valueOf()`方法始终都返回函数的代码。返回代码的格式则因浏览器而异：

<br>

``` javascript
function output() {
  console.log(output.toString());
}
output();
```

<br>

### 3.6 Symbol类型

<br>

根据JavaScript规范，只有字符串类型和Symbol类型可以用作对象属性键，如果用其余类型，将自动转换为字符串类型。Symbol类型表示唯一的标识符，在创建Symbol类型变量时，可以给Symbol一个描述：

<br>

``` javascript
let id1 = Symbol("id");
let id2 = Symbol("id");
console.log(id1 == id2);    // false
```

<br>

Symbol类型保证了变量的唯一性，即使两个Symbol类型的变量具有相同的描述，它们也是不同的。还要注意，Symbol类型的变量不具有隐式转换的能力，如果想显示Symbol类型的变量，可以使用`toString`方法，它会返回`Symbol(name)`这样的结果，其中`name`是变量的名称；如果希望得到Symbol类型变量的描述，需要使用`description`属性。

<br>

在对象字面量中使用Symbol类型的变量，需要用方括号将属性名括起来。对象中的Symbol类型属性值会被`for-in`循环跳过，但是使用`Object.assign`方法复制对象时，会同时复制Symbol类型的属性。

<br>

``` javascript
let user = {
  name : "AshGrey",
  age  : 20,
  [id] : 2022012050
};
```

<br>

JavaScript维护了一个全局Symbol注册表，可以在其中创建Symbol类型并在稍后访问它们，它可以确保每次访问相同名字的Symbol变量时返回的都是相同的Symbol。要从注册表中读取（不存在的时候则创建）Symbol使用`Symbol.for(key)`方法：

<br>

``` javascript
var backgroundColor = Symbol.for("red");
var forgeColor = Symbol.for("red");
console.log(forgeColor === backgroundColor);    // true
```

<br>

### 3.7 基本包装类型

<br>

基本包装类型是程序在读取一个简单数据类型时在后台创建的对应的引用类型，从而能够调用一些方法操作这些数据。后台调用这些方法的过程：
- 创建某种简单数据类型对应的基本包装类型的一个实例；
- 在实例上调用指定的方法；
- 销毁这个实例。

<br>

对基本包装类型使用`typeof`会返回`object`，而且所有基本包装类型的对象在转换为Boolean类型（简单数据类型）时都是`true`。创建基本包装类型的方法如下：

<br>

``` javascript
let str = new String("Izumi");
let value = 2;
let num = new Number(value);
console.log(typeof str);    // Object
console.log(typeof num);    // Object
```

<br>

#### 3.7.1 Number基本包装类型

<br>

- `valueOf()`方法返回数值；
- `toString()`和`toLocaleString()`方法都返回字符串形式的数值，其中`toString()`方法还可以传递一个表示基数的参数，控制其返回某进制数值的字符串形式；
- `toFixed()`方法按照指定的小数位返回数值的字符串表示，如果数值本身包含的小数位数比指定的小数位数多，可能会有数据的舍入；
- `toExponential()`方法返回一个以指数表示法表示的数值的字符串形式，它接受一个参数，该参数表示输出结果中的小数位数；
- `toPrecision()`方法接受一个参数，该参数表示有效位数。如果有效位数比原来数字的有效位数还小，有可能会导致数据的舍入。

    ``` javascript
    let num = new Number(1112.345);
    console.log(num.toFixed(4));        // 1112.3450
    console.log(num.toFixed(2));        // 1112.35
    console.log(num.toExponential(4));  // 1.1123e+3
    console.log(num.toExponential(7));  // 1.1123450e+3
    console.log(num.toPrecision(8));    // 1112.3450
    console.log(num.toPrecision(4));    // 1112
    ```

<br>

#### 3.7.2 String基本包装类型

<br>

- `valueOf()`、`toString()`和`toLocaleString()`都是返回字面值；
- `charAt()`方法接受一个参数，该参数是基于0的字符位置，返回值是该字符位置上的字符，部分浏览器还支持使用中括号访问的方法；
- `charCodeAt()`方法与`charAt()`类似，但是返回字符位置上字符对应的编码；

    ``` javascript
    let loveString = "I love ";
    loveString = loveString.concat("Huaier ", "& AshGrey!");
    console.log(loveString.charAt(14));     // "a"
    console.log(loveString.charCodeAt(14)); // 97
    console.log(loveString[14]);            // "a"
    ```

- `concat()`方法用于将一个或者多个字符串拼接在一起并返回拼接得到的新字符串，该方法接受任意多个参数。实践中将字符串拼接起来的方法更多用的是`+`运算；

    ``` javascript
    let loveString = "I love ";
    console.log(loveString.concat("Huaier ", "& AshGrey!"));
    // "I love Huaier & AshGrey!"
    ```

- `slice()`和`substring()`方法的第一个参数指定子字符串的开始位置，第二个参数（可选）指定子字符串最后一个字符后面的位置。`substr()`方法的第二个参数指定的是返回的字符个数。如果给这三个方法传入负数，则它们的行为分别是：`slice()`将传入的负值与字符串长度相加，`substr()`方法将负的第一个参数与字符串长度相加，负的第二个参数设置为0，`substring()`方法将负值全部设置为0；

    ``` javascript
    let stringObject = new String("Izumi Sagiri");

    console.log(stringObject.slice(3, 8));      // "mi Sa"
    console.log(stringObject.substr(3, 4));     // "mi S"
    console.log(stringObject.substring(3, 8));  // "mi Sa"

    console.log(stringObject.slice(-3));        // "iri"
    console.log(stringObject.slice(4, -2));     // "i Sagi"
    console.log(stringObject.substr(-8, 3));    // "i S"
    console.log(stringObject.substr(4, -2));    // "" <empty string>
    console.log(stringObject.substring(3, -4)); // "Izu"
    ```

- `indexOf()`和`lastIndexOf()`，功能与数组的这两个方法类似；
- `trim()`方法返回一个删除了字符串前缀和后缀空格的字符串副本，现代浏览器还支持`trimLeft()`和`trimStart()`用于删除字符串前缀空格，`trimRight()`和`trimEnd()`用于删除字符串后缀空格；
- `toLowerCase()`方法用于将字符串英文字母小写，`toUpperCase()`方法用于将字符串英文字母大写；
- `match()`方法返回查找到的匹配正则表达式的子字符串数组，`search()`方法返回查找到的第一个匹配正则表达式的子字符串的索引；
- `replace()`方法接受两个参数，第一个参数可以是RegExp对象或者字符串（这个字符串并不是RegExp对象的字符串形式，而是子字符串），第二个参数是一个字符串或者函数，是替换后的字符串。若第一个参数提供字符串，只会替换第一个匹配的子字符串，如果要替换所有的子字符串，则需要使用正则表达式并且指定全局标志，也可以使用`replaceAll()`方法：

    ``` javascript
    let text = "loli, life, knife, five";
    let pattern = /ife/igm;
    console.log(text.replace("ife", "ink"));
    // wink, life, knife, five
    console.log(text.replace(pattern, "ink"));
    // wink, link, knink, five
    ```

    `replace()`函数的第二个参数也可以是一个函数，匹配字符串时会向这个函数传递这些参数：第一个参数是匹配项，之后几个参数是按顺序的捕获组的匹配项，最后两个参数分别是模式的匹配项在字符串中的位置和原始的字符串。

    ``` javascript
    function replaceHtmlChar(text) {
      return text.replace(/[<>"&"]/g, function(match, pos, originalText) {
        switch(match) {
          case "<" :
            return "&lt;";
          case ">" :
            return "&gt;";
          case "&" :
            return "&amp;";
          case "\"" :
            return "&quot;";
          } 
        });
    }
    console.log(replaceHtmlChar("<p class =\"greeting\">Hello World!</p>"));
    // &lt;p class =&quot;greeting&quot;&gt;Hello World!&lt;/p&gt;
    ```

- `split()`方法可以基于指定的分隔符将一个字符串分成多个子字符串，并将结果放在一个数组中。指定的分隔符可以是字符串也可以是RegExp对象。`split()`方法可以接受可选的第二个参数，用于指定数组的大小：

    ``` javascript
    let loli = "Izumi Sagiri, Elaina, WanTerrier";
    console.log(loli.split(","));
    // Array (3) : ["Izumi Sagiri", " Elaina", " WanTerrier"]
    ```

- `localeCompare()`方法比较两个字符串：如果字符串的字典顺序排在参数前面，返回`-1`；如果相等，返回`0`；如果排在后面，返回`1`；

<br>

<br>

<br>

## 4 面向对象

<br>

### 4.1 对象的属性标志与属性描述符

<br>

ECMAScript为对象设计了两种属性：数据属性和访问器属性。

<br>

数据属性包含一个数据值的位置，在这个位置可以读取和写入值。数据属性有4个描述其行为的标志：
- `[[Value]]`：包含这个属性的数据值，读取属性值时从这个位置读；写入属性值时把新值保存在这个位置，该标志的默认值为`undefined`；
- `[[Writable]]`：表示能否修改属性的标志，即是否可以直接在对象上创建属性，该标志的默认值为`true`；
- `[[Enumerable]]`：表示能否通过`for-in`循环列出属性，该标志的默认值为`true`；
- `[[Configurable]]`：表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的标志或者将属性修改为访问器属性，该标志的默认值为`true`。

<br>

要修改这些数据属性的标志，需要使用方法`Object.defineProperty()`方法，这个方法接受三个参数：属性所在的对象、属性的名字和一个描述符对象。其中描述符对象的可以用于设置以上四种标志的值（如果需要一次性定义多个属性的标志，则需要使用方法`Object.defineProperties()`）：

<br>

``` javascript
let loli = {};
Object.defineProperty(loli, "name", {
  writable : false,
  configurable : false,
  value : "Izumi Sagiri"
});
console.log(loli.name);
delete loli.name;
console.log(loli.name); // "Izumi Sagiri"
loli.name = "Elaina";
console.log(loli.name); // "Izumi Sagiri"
```

<br>

要获取这些数据属性的标志，需要使用方法`Object.getOwnPropertyDescriptor()`方法，这个方法接受两个参数：需要从中获取的对象和属性的名称：

<br>

``` javascript
let loli = {
  name : "Izumi Sagiri",
  age : 14
};
let descriptor = Object.getOwnPropertyDescriptor(loli, "name");
console.log(JSON.stringify(descriptor, null, 2));
/*
{
  "value": "Izumi Sagiri",
  "writable": true,
  "enumerable": true,
  "configurable": true
}
*/
```

<br>

访问器属性不包含数据值，它们包含一对`getter()`和`setter()`函数，在读取访问器属性时会调用`getter()`函数，这个函数负责返回有效的值；在写入访问器属性时，会调用`setter()`函数，这个函数负责决定如何处理数据。访问器属性有以下4个描述其行为的标志：
- `[[Configurable]]`：与数据属性类似，表示能否通过`delete`删除属性从而定义新的属性，能够修改属性的标志或者将属性修改为数据属性，该标志的默认值为`true`；
- `[[Enumerable]]`：表示能否通过`for-in`循环返回属性，这个标志的默认值是`true`；
- `[[Get]]`：在读取属性时调用的函数；
- `[[Set]]`：在写入属性时调用的函数。

<br>

``` javascript
let loli = {
  _name : "Izumi Sagiri"
};
Object.defineProperties(loli, {
  name : {
    // get name() {}
    get : function() {
      console.log(`Get The Property : ${this._name}`);
      return this._name;
    },
    // set name(value) {}
    set : function(newValue) {
      this._name = newValue;
      console.log(`Set The Property : ${this._name}`);
    }
  }
});
console.log(loli.name);
// "Get The Property : Izumi Sagiri"
// "Izumi Sagiri"
loli.name = "Elaina";
// "Set The Property : Elaina"
console.log(loli.name);
// "Get The Property : Elaina"
// "Elaina"
```

<br>

### 4.2 原型

<br>

在JavaScript中对象有一个特殊的隐藏属性`[[Prototype]]`，它要么为`null`，要么就是对另一个对象的引用，引用的这个对象称为原型。可以使用`__proto__`对对象的原型进行设置。如果访问一个对象没有的属性，JavaScript会尝试在其原型中查找：

<br>

``` javascript
let girlfriend = {
  hug : true,
  kiss : true
};
let loli = {
  sex : true
};
loli.__proto__ = girlfriend;
console.log(loli.hug);  // true
```

<br>

可以构造一个原型链不断继承对象的属性，JavaScript将在这些原型链中寻找没有的属性。注意引用不能形成闭环，如果对`__proto__`进行赋值并且形成了闭环，JavaScript将会抛出错误，并且一个`__proto__`不能从多个对象中获取属性。`__proto__`并不是对象内部的`[[Prototype]]`，而是它的`getter/setter`函数，而且它的存在是出于历史原因，现代JavaScript建议应该使用方法`Object.getPrototypeOf()`和`Object.setPrototypeOf()`。

<br>

如果直接在对象上添加与继承得到的属性名称相同的名称，继承得到的属性将不会被调用，这是因为JavaScript优先搜索对象自己的属性：

<br>

``` javascript
let wife = {
  name : "Huaier",
  age : 17,
  love() {
    console.log(`I love you, ${this.name}`);
  }
};
let wifeForever = {};
wifeForever.__proto__ = wife;
wifeForever.love = function() {
  console.log("I love you, AshGrey");
};
wifeForever.love();
console.log(wifeForever);
```

<br>

#### 4.2.1 原型模式

<br>

创建的每个函数都有其`prototype`属性，默认的`prototype`是一个只有属性`constructor`的对象，该属性是一个指针，指向函数自身。当我们有一个对象，但不知道它使用了哪个构造函数，并且需要创建另一个类似的对象时，可以用以下的方法进行构造：

<br>

``` javascript
function loli(name) {
  loli.prototype.name = name;
  console.log(this.name);
}

let sagiri = new loli("Izumi Sagiri");
let elaina = new sagiri.constructor("Elaina");
```

<br>

如果将函数中默认的`prototype`替换掉，那么其中就不再有`constructor`对象，所以如果要保证仍然有这个对象，就必须选择添加/删除属性到默认的`prototype`中，而不是将整个覆盖。或者也可以手动重写一个`constructor`对象，但这样重写得到的`constructor`对象的`[[Enumerable]]`属性被设置为`true`。要注意的是，构造函数使用`prototype`增加属性，由构造函数创建的对象内部并没有构造函数的属性，它们是通过原型链进行调用的。

<br>

有以下几个可用的方法来研究对象的`prototype`：
- 通过`prototype`属性的`isPrototypeOf()`方法确定对象之间是否有原型链关系，如果参数的`prototype`属性指向调用该方法的对象，那么返回`true`：

    ``` javascript
    function loli(name) {
      loli.prototype.name = name;
      console.log(this.name);
    }

    let sagiri = new loli("Izumi Sagiri");
    let elaina = new sagiri.constructor("Elaina");
    console.log(loli.prototype.isPrototypeOf(sagiri));  // true
    console.log(loli.prototype.isPrototypeOf(elaina));  // true
    ```

- 可以通过`Object.getPrototypeOf()`方法获取对象的原型：

    ``` javascript
    function loli(name) {
      loli.prototype.name = name;
      console.log(this.name);
    }

    let sagiri = new loli("Izumi Sagiri");
    let elaina = new sagiri.constructor("Elaina");
    console.log(Object.getPrototypeOf(sagiri) === loli.prototype);  // true
    console.log(Object.getPrototypeOf(sagiri) === loli.prototype);  // true
    Object.getPrototypeOf(sagiri).age = 12;
    console.log(sagiri.age);    // 12
    ```

- 可以使用`hasOwnProperty()`检验一个属性是在实例中还是在原型中：

    ``` javascript
    function loli(name) {
      this.name = name;
      console.log(this.name);
    }

    let sagiri = new loli("Izumi Sagiri");
    sagiri.age = 12;
    console.log(sagiri.hasOwnProperty("name")); // false
    console.log(sagiri.hasOwnProperty("age"));  // true
    ```

<br>

事实上JavaScript中的引用类型都有自己的构造函数，这些内建对象都在`prototype`上挂载了自己的方法。注意`null`和`undefined`比较特殊，它们没有基本包装类型，也不具有自己的方法和属性，没有相应的原型。例如以下的程序就为引用类型`Function`添加了内建方法：

<br>

``` javascript
Function.prototype.defer = function(ms) {
  setTimeout(this, ms);
};
function f() {
  console.log("hei!");
}
f.defer(10000);
```

<br>

因为原型中所有属性是被很多实例所共享的，如果只是基本数据类型，实例会创建自己的属性，但如果是引用类型，大部分情况下只是使用引用类型的方法对其进行修改而不是实例自定义属性进行覆盖，这种共享模式就出现了问题：

<br>

``` javascript
function Person() {}
Person.prototype = {
  constructor : Person,
  name : "AshGrey",
  age : 19,
  job : "Student",
  loli : ["Izumi Sagiri"],
};


let person1 = new Person();
let person2 = new Person();
person1.loli.push("Elaina");
console.log(person2.loli);  // Array(2) : ["Izumi Sagiri", "Elaina"]
```

<br>

#### 4.2.2 构造函数模式+原型模式

<br>

可以采用以下方法解决共享的问题：构造函数模式用于定义实例属性（所有的引用类型都放在这），而原型模式用于定义方法和可以共享的属性。这样，每个实例都有自己的一份实例属性的副本，但同时又共享着对方法的引用。

<br>

``` javascript
function Person() {
  this.name = "AshGrey";
  this.age = 19;
  this.job = "Student";
  this.loli = ["Izumi Sagiri"];
}
Person.prototype = {
  constructor : Person,
  printInfo : function() {
  let loliStr = "";
  for (let i = 0; i <= this.loli.length - 1; i++) {
    loliStr += `${this.loli[i]}\n`;
  }
  console.log(`name : ${this.name}\nage : ${this.age}\njob : ${this.job}\nloli : ${loliStr}`);
  }
};

let person1 = new Person();
let person2 = new Person();
person1.loli.push("Elaina");
person1.printInfo();
person2.printInfo();
```

<br>

#### 4.2.3 动态原型模式

<br>

可以通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。只有在方法不存在的情况下才会将它添加到原型中，即只在初次调用构造函数时才会执行：

<br>

``` javascript
function Person() {
  this.name = "AshGrey";
  this.age = 19;
  this.job = "Student";
  this.loli = ["Izumi Sagiri"];
  if (typeof this.printInfo !== "function") {
    Person.prototype.printInfo = function() {
      let loliStr = "";
      for (let i = 0; i <= this.loli.length - 1; i++) {
        loliStr += `${this.loli[i]}\n`;
      }
      console.log(`name : ${this.name}\nage : ${this.age}\njob : ${this.job}\nloli : ${loliStr}`);
    }
  }
}
```

<br>

### 4.3 继承

<br>

#### 4.3.1 原型链

<br>

每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针，如果让某个原型对象等于另一个类的实例，此时原型对象将包含一个指向另一个原型的指针，这就形成了原型链。

<br>

``` javascript
function SuperType() {
  this.property = true;
}
SuperType.prototype.getSuperValue = function() {
  return this.property;
};

function SubType() {
  this.subProperty = false;
}

SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function() {
  return this.subProperty
};
let instance = new SubType();
console.log(instance.getSuperValue());  // true

/*
 *      |---------------------------------|
 *  SuperType()  |> SuperType prototype   |
 *               |  + constructor --------|
 *               |  + getSuperValue() function
 *               |
 *               |------------------------|
 *  SubType()       SubType prototype     |
 *                  + [[prototye]] -------|
 *                  + property : true
 *                  + getSubValue() function
*/
```

<br>

与之前实例共享原型时一样，使用原型链继承时派生类的原型属性会从基类得到实例属性，派生类的所有实例都共享了类中的原型属性，这是不妥当的。

<br>

#### 4.3.2 经典继承

<br>

经典继承/借用构造函数（constructor stealing）的核心思想是在派生类的构造函数内部调用基类的构造函数。借用构造函数可以向基类构造函数传递参数，并且基类中的所有实例属性都会成为派生类构造出对象的属性，即进行了一次复制，并且还可以增添属性和覆盖基类方法的行为：

<br>

``` javascript
function SuperType(name) {
  this.name = name;
  this.printInfo = function() {
    console.log(this.name);
  }
}
function SubType() {
  SuperType.call(this, "Izumi Sagiri");
  this.age = 12;
  this.printInfo = function() {
    console.log(this.name + "\n" + this.age);
  }
}
let loli = new SubType();
let girlfriend = new SuperType("Elaina");
girlfriend.printInfo();
loli.printInfo();
```

<br>

#### 4.3.3 伪经典继承

<br>

伪经典继承/组合继承（combination inheritance）使用原型链实现对原型属性和方法的继承，而通过构造函数来实现对实例属性的继承：

<br>

``` javascript
function SuperType(name, age) {
  this.name = name;
  this.age = age;
}
SuperType.prototype.printInfo = function() {
  console.log(this.name + "\n" + this.age);
}
function SubType(name, age) {
  SuperType.call(this, name, age);
}
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
let loli = new SubType("Elaina", 14);
console.log(loli.printInfo());
```

<br>

#### 4.3.4 原型式继承

<br>

原型式继承在对象复制函数内部临时创建一个构造函数，将构造函数的`prototype`设置为传入的对象，并返回根据该构造函数创建的新对象，这样创建的对象的原型是传入的对象，所以实际上对象的实例属性和原型属性都被复制了（这里可以称为继承），而且要注意的是，这些属性被共享了。

<br>

``` javascript
function SuperType(name, age) {
  this.name = name;
  this.age = age;
}
SuperType.prototype.printInfo = function() {
  console.log(this.name + "\n" + this.age);
}
function copy(object) {
  function C() {}
  C.prototype = object;
  return new C();
}
var person1 = new SuperType("AshGrey", 19);
var person2 = copy(person1);
console.log(person2.printInfo());
```

<br>

ECMAScript 5 新增了 `Object.create()` 方法规范化了原型式继承，这个方法接收两个参数，一个是用作新对象原型的对象和为新对象定义额外属性的对象（可选），第二个参数的格式就是对象的字面量。

<br>

### 4.4 现代 JavaScript 的类

<br>

#### 4.4.1 类作为语法糖的使用方法

<br>

现代 JavaScript 支持使用类（class）而不是`new Function`构造面向对象编程，其基本语法是在`class`代码块内编写一个构造函数，其中定义类的属性，再编写自定义的类的方法，然后创建对象时使用 `new ClassName()`来创建对象：

<br>

``` javascript
let time = "2024-08-19";
class wife {
  constructor(name, age, meetTime) {
    this.name = name;
    this.age = age;
    this.meetTime = meetTime;
  }
  printInfo() {
    console.log(this.name + "\n" + this.age);
  }
  celebrate() {
    if (time.slice(5) === this.meetTime.slice(5))
      console.log("You should celebrate the wedding anniversary");
  }
}

let huaier = new wife('Huaier', 17, '2022-06-19');
huaier.celebrate();
```

<br>

`class`常被理解为一种语法糖，这是因为它的实际功能和之前使用的各种创建对象的方法类似。`class ClassName {...}`做了两件事：
- 创建一个名为`ClassName`的函数，该函数成为类声明的结果，且该函数的代码来自`constructor`函数，如果不编写这个函数，则默认为空；
- 存储类中的方法到`ClassName.prototype`下。

<br>

不过`class`确实和普通的构造函数不同：
- 通过`class`创建的函数具有特殊的内部属性标记`[[IsClassConstructor]]: true`，JavaScript会在一些地方检查该属性，例如必须使用`new`来调用它，单独调用JavaScript引擎将抛出错误；
- 类不存在类声明提升，必须在类定义之后才能调用它；
- 类方法不可枚举，类定义将`prototype`中所有方法的`enumerable`标志设置为`false`；
- 类自动使用严格模式，在类中编写的函数或其他代码都在严格模式下运行。

<br>

类中同样包括`getter/setter`，可以在类中的属性被读取或者设置时被调用：

<br>

``` javascript
class User {
  constructor(name) {
    this.name = name;   // set name()
  }
  get name() {
    console.log("The 'name' is getted by somewhere");
    return this.name_;
  }
  set name(value) {
    this.name_ = value;
    console.log("The 'name' is setted by somewhere")
  }
}

let ashgrey = new User("AshGreyG");
console.log(ashgrey.name);
ashgrey.name = "AshGrey-AshGrey";
```

<br>

以上代码中使用`name_`是为了防止无限递归，简洁的写法（即不期望在调用或传入参数时有额外行为时）也可以不需要`getter/setter`。注意，类和函数一样也具有类表达式：

<br>

``` javascript
let User = class MyClass {
  sayHello() {
    console.log(MyClass);   // MyClass is only seen inside
  }
};

new User().sayHello();
console.log(MyClass);   // MyClass can't be seen outside
```

<br>

#### 4.4.2 类中的函数绑定

<br>

在 5.2 节中写到过对象方法被传递到某处，或者在另一个上下文中被调用时，对象的方法内部的`this`不再是对象本身，这样就丢失了`this`。除了函数包装器以及`bind`函数的使用，类还提供了一种优雅的语法：在类中使用箭头函数：

<br>

``` javascript
class User {
  constructor(name) {
    this.name = name;
  }
  printInfo = () => {
    alert(this.name);
  }
  changeInfo = (value) => {
    this.name = value;
  }
}

let user = new User('AshGrey');
setTimeout(user.printInfo, 100)
```

<br>

<br>

<br>

## 5 深入函数

<br>

### 5.1 递归与堆栈

<br>

正在运行的函数的执行过程的相关信息存储在其执行上下文中，执行上下文是一个内部数据结构，它包含有关函数执行时的详细细节：当前控制流所在的位置，当前的变量，`this`的值以及一些内部细节。一个函数调用仅具有一个与其相关联的执行上下文。

<br>

当一个函数进行递归时将发生下列事情：
- 当前函数被暂停；
- 与它关联的执行上下文被一个叫做**执行上下文堆栈**的数据结构保存；
- 执行嵌套调用；
- 嵌套调用结束后，从堆栈中恢复之前的执行上下文，并从停止的位置恢复外部函数。

<br>

``` javascript
function pow(x, n) {
  if (n === 1) {
    return x;
  }
  else {
    return x * pow(x, n - 1);
  }
}
```

上述递归函数`pow()`在计算`pow(2,3)`时它进入了第二个条件分支，并打算执行一个函数调用，此时外部函数的执行上下文`Context: { x: 2, n: 3, at line 5 }`被存入了执行上下文堆栈。

<br>

### 5.2 函数绑定

<br>

当将对象方法作为回调进行传递时，例如传递给`setTimeout`时会发生丢失`this`的问题，一旦方法被传递到与对象分开的某个地方，`this`就会丢失：

<br>

``` javascript
let users = {
  name : "AshGrey",
  age : 19,
  printInfo() {
    console.log(this.name + "\n" + this.age);
  }
};
setTimeout(users.printInfo, 1000); // undefined
```

<br>

解决方法有以下几种：
- 包装器：可以将包装函数传递给`setTimeout`函数，这里的包装函数既可以是完整的`function`函数也可以是更简短的箭头函数：

    ``` javascript
    let users = {
      name : "AshGrey",
      age : 19,
      printInfo() {
        console.log(this.name + "\n" + this.age);
      }
    };
    setTimeout(() => users.printInfo(), 1000);
    ```

    对于该场景我们的方法还是有漏洞，例如如果`users`的信息突然改变，此时对象打印的信息就会出现问题；
- `bind`函数：函数的内建方法`bind`的基本语法如下：

    ``` javascript
    let user = {
      name : 'AshGrey',
      age : 20
    };
    function func() {
      alert(this.name);
    }
    let funcUser = func.bind(user);
    setTimeout(funcUser, 1000)
    ```

    这里将`func`函数的`this`绑定为`user`对象，所以可以调用`user`的属性。如果一个对象中的方法特别多而想要批量绑定的话，需要使用以下`for`循环：

    ``` javascript
    let user = {
      name : 'AshGrey',
      password : 'HuaierLove',
      printInfo() {
        let encryptPassword = '*'.repeat(this.password.length);
        alert(`name : ${this.name}\npassword : ${encryptPassword}`);
      },
      sudoPrintInfo() {
        alert(`name : ${this.name}\npassword : ${this.password}`);
      }
    };

    for (let key in user) {
      if (typeof user[key] === 'function') {
        user[key] = user[key].bind(user);
      }
    }

    setTimeout(user.printInfo, 100);
    setTimeout(user.sudoPrintInfo, 100);
    ```

<br>

### 5.3 Rest 参数和 Spread 语法

<br>

与 Python 中的`*`可变参数一样，JavaScript 中也有可变参数，其使用`...`来声明可变参数（Rest 参数，指剩下所有参数），其接受多个参数并将其收集到数组里，这种参数会自动收集后续所有的元素，所以只能放在参数列表的末尾，否则会出现语法错误：

<br>

``` javascript
function sumAll(name, ...args) {
  let sum = 0;
  for (value of args) {
    sum += value;
  }
  alert(`${name} : ${sum}`);
} 

sumAll('AshGrey', 1, 2, 3, 4, 5);
// AshGrey : 15
```

<br>

与 Python 中的将 tuple/list 传入可变参数需要添加`*`一样，JavaScript 将数组传入 Rest 参数中也需要在数组前加上`...`，这种语法称为 Spread 语法：

<br>

``` javascript
function sumAll(name, ...args) {
  let sum = 0;
  for (value of args) {
    sum += value;
  }
  alert(`${name} : ${sum}`);
} 

let array = [1, 2, 2, 3];
sumAll('Huaier', ...array);
```

<br>

Spread 语法不仅可以用于将数组拆成多个元素传入到函数中，它可以传入到任何需要拆分数组的地方，而且这种语法不是针对数组的，它对所有的可迭代对象都可适用，这是因为 Spread 语法内部使用`for-of`来迭代参数。所以将迭代器对象转换成数组时，`...`等同于`Array.from`；将对象复制给另一个对象时，`...`等同于`Object.assign`：

<br>

``` javascript
let array = [1, 2, 2, 3];
let merge = [0, ...array];
alert(merge);
// 0, 1, 2, 2, 3

let testObject1 = {
  name : 'AshGrey',
  age : 20,
  id : 2022012050
}
let testObject2 = {...testObject1};
alert(JSON.stringify(testObject2));
// {name:'AshGrey',age:20,id:2022012050}
```

<br>

### 5.4 闭包

<br>

JavaScript 是天然具有闭包的语言，要讲清楚闭包就必须从 JavaScript 的词法环境（lexical environment）讲起：
- 在 JavaScript 中，每个运行的函数、代码块`{}`以及整个脚本都有一个被称为**词法环境**的隐藏关联对象，其由两部分组成：
  - **环境记录**：一个存储所有局部变量作为其属性（包括`this`的值）的对象；
  - 对**外部词法环境**的引用，与外部代码关联；

  所以一个变量只是环境记录这个特殊对象的内部属性，「获取或更改变量的值」即意味着「获取或更改环境记录对象的属性」。对于以下的代码，其只有一个词法环境，环境记录是这么变化的：

  ``` javascript
  //                    ┌─────────────────────┐
  // execution start ───│love: <uninitialized>│──> null
  //                    └─────────────────────┘
  //                    ┌───────────────┐
  let love; // ─────────│love: undefined│
  //                    └───────────────┘
  //                    ┌──────────────┐
  love = "Huaier"; // ──│love: "Huaier"│
  //                    └──────────────┘
  //                    ┌───────────────┐
  love = "AshGrey"; // ─│love: "AshGrey"│
  //                    └───────────────┘
  ```

  最开始变量处于未初始化的状态，此时只有引擎知道变量，而编写的脚本的代码是无法获取这个变量的，这是一种特殊的内部状态，在使用`let`声明这个变量前这个变量几乎就等于不存在；
- 我们说过函数就是一种变量，但是和普通变量不同的是，函数声明的初始化会被立即完成，当创建了一个词法环境的时候，函数声明会立即成为**即用型函数**，例如

  ``` javascript
  //                    ┌─────────────────────┐
  // execution start ───│love: <uninitialized>│──> null
  //                    │say: function        │
  //                    └─────────────────────┘
  let love = "Huaier&AshGrey";
  function say(name) {
    alert(`${love} : ${name}`);
  }
  ```

  注意这种词法环境的变化只适用于函数声明而不适用于函数表达式；
- 在函数运行调用刚开始时，会自动创建一个新的词法环境以存储这个调用的局部变量和参数，在这个函数调用期间总共有两个词法环境：内部用于函数调用、外部的全局环境，而内部的词法环境引用了外部的全局环境。**当代码要访问一个变量时，首先会搜索内部的词法环境，然后搜索外部词法环境，以此类推知道全局词法环境**；
- 接下来就是**返回函数**的词法环境分析。所有的函数在创建时都会存储创建他们的词法环境，这是因为所有的函数都有名为`[[Environment]]`的隐藏属性，该属性保存了对创建该函数的词法环境的引用：

  ``` javascript
  function makeCounter() {
    let count = 0;
    return function() {
      return count++;
    };
  }

  let counter = makeCounter();
  // [[Environment]]
  //        │
  //        │
  //        V            ┌─────────────────────┐
  //   ┌────────┐ outer  │makeCounter: function│ outer
  //   │count: 0│───────>│counter: undefined   │──────> null
  //   └────────┘        └─────────────────────┘
  ```

  因此`counter.[[Enumerable]]`有对`{count: 0}`词法环境的引用，由于函数的`[[Environment]]`属性在函数创建时被永久设置，这和函数在哪里调用都无关。当我们调用`count()`时，它就会更新对它而言的外部变量，即函数`makeCounter()`内部的`count`变量。

<br>

### 5.5 调度函数

<br>

有两种计划调度的函数：
- `setTimeout()`允许函数推迟一段时间运行，`clearTimeout()`将定时器清除；
- `setInterval()`允许重复运行一个函数，从一段时间间隔之后开始运行，之后以该时间间隔连续重复运行该函数，`clearInterval()`将循环器清除。

<br>

注意在大多数浏览器中弹出`alert/confirm/prompt`弹窗时，内部的定时器仍旧在运行。周期性调度的`setInterval()`函数不能精确地设置两次执行之间的延时，这是因为执行的函数也需要消耗时间，使用嵌套的`setTimeout()`则要比`setInterval()`函数更好：

<br>

``` javascript
let i = 1;
setTimeout(function run() {
  alert(i++);
  setTimeout(run, 100);
}, 100);
```

<br>

在这里实际就是使用了`setTimeout()`函数的无穷递归调用。