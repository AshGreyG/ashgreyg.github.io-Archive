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

#### 1.4.5 String类型

<br>

String类型用于表示由零或多个16位Unicode字符的字符序列，字符串可以用双引号也可以用单引号括起。JavaScript中的字符串是不可变的，字符串一旦创建，它们的值就不能变化，要改变某个字符串的值，则必须先销毁原来的值再用一个包含新值的字符串填充该字符串。

<br>

JavaScript中有两个可以将其余数值转换为字符串的方法：
- 除了`null`以及`undefined`没有转换成字符串的方法，所有数据类型都有转换成字符串的`toString()`方法，除了Number类型的`toString()`方法可以接受一个表示进制基数的参数，其余数据类型的`toString()`方法都没有参数；
- 转型函数`String()`定义如下：
    - 如果值有`toString()`方法，调用该方法；
    - 如果值是`null`，则返回`"null"`;
    - 如果值是`undefined`，则返回`"undefined"`。

<br>

#### 1.4.6 Object类型

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

#### 1.7 函数

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
var wife = new Object();
wife.name = "Izumi Sagiri";
console.log(wife.name);
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
var wife = new Object();
changeObjectName(wife);
console.log(wife.name); // -> "Izumi Sagiri"
```

<br>

为了说明函数传递的参数是按值传递而不是按引用传递，可以给出以下例子：

<br>

``` javascript
function changeObjectName(obj) {
    obj.name = "Izumi Sagiri";
    obj = new Object();
    obj.name = "AshGrey";
}
var wife = new Object();
changeObjectName(wife);
console.log(wife.name); // -> "Izumi Sagiri"
```

<br>

如果函数是按引用传递的，则不管函数内部的对象`obj`是否被重新赋值为一个新的对象，它的引用都应该是外部对象`wife`，而这里`wife`的值没有因为`obj`的重新定义而改变，就说明了函数是按值传递的。函数内部的变量、对象都会在函数体结束调用时被销毁。

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

JavaScript没有块级作用域，这意味着流控制语句中的变量将被添加到当前的执行环境：

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

使用`var`声明的变量会自动被添加到最接近的环境中，如果初始化变量时没有使用关键字`var`声明，该变量会被自动添加到全局环境中。建议只使用`var`声明变量，在严格模式下，初始化未经声明的变量将导致错误。

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

除了使用`objectA.propertyName = `这样的添加属性或者方法的模式，还可以使用对象字面量表示法（当花括号内留空，则对象只包含默认属性和方法）：

<br>

``` javascript
var wife = {
    name : "Izumi Sagiri",
    age  : 14
};
```

<br>

访问对象的属性和方法可以使用点表示法，也可以使用中括号表示法，中括号表示法用在对象的属性使用的是关键字或保留字，或者属性名中包含会导致语法错误的字符：

<br>

``` javascript
wife["first name"] = "Izumi";
wife["function"] = 2;
```

<br>

### 3.2 Array类型

<br>

JavaScript中的数组是Array类型，该数组虽然是数据的有序列表，但与其他编程语言不同的地方在于JavaScript的数组的每一项可以保存任何类型的数据。创建数组主要有两种方法：
- 使用`Array`构造函数，其中`new`关键字可用可不用。当向构造函数传递一个Number类型的参数时，数组的大小将被设定为该Number类型的值；当向构造函数传递一个非Number类型的参数或者传递多个参数时，相当于传递给数组了具体的元素；

    ``` javascript
    var names = new Array(3);           // length = 3
    var names = new Array("Izumi");     // names[0] = "Izumi"
    var names = new Array(3, "Izumi");  // names[0] = 3, names[1] = "Izumi"
    ```

- 使用数组字面量表示法：

    ``` javascript
    var names = [3, "Izumi"];
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
var wife = ["IzumiSagiri", "Elaina", "Charolotte Soller"];
console.log(wife);                    // Array(3) : ["IzumiSagiri", "Elaina", "Charolotte Soller"]
console.log(wife.valueOf());          // Array(3) : ["IzumiSagiri", "Elaina", "Charolotte Soller"]
console.log(wife.toString());         // "IzumiSagiri,Elaina,Charolotte Soller"
console.log(wife.toLocaleString());   // "IzumiSagiri,Elaina,Charolotte Soller"
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
var values = [0, 1, 5, 10, 15];
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

var values = [0, 10, 5, 4, 12];
console.log(values.sort(compare));  // Array(5) : [0, 4, 5, 10, 12]
```

<br>

这样就可以覆盖原来的按照字符串排序的方法，实现数字间的比较。

<br>

JavaScript给数组提供了一些操作方法：
- `concat()`方法基于当前数组中的所有项创建一个新的数组，这个方法会先创建当前数组的一个副本，并将接收到的参数添加到副本的末尾，最后返回新构建的函数；

    ``` javascript
    var wifes_1 = ["Izumi", "Sagiri"];
    var wifes_2 = wifes_1.concat("Elaina", ["Charolotte", "Soller"]);
    console.log(wifes_1.toString());  // "Izumi,Sagiri"
    console.log(wifes_2.toString());  // "Izumi,Sagiri,Elaina,Charolotte,Soller"
    ```

- `slice()`方法基于当前数组的一个或多个项创建一个新数组：
    - 接受1个参数时，`slice()`方法返回从参数指定位置（注意都是索引值）开始到当前数组末尾的所有项；
    - 接受2个参数时，`slice()`方法返回起始和结束位置之间的项，但不包括结束位置的项，该操作方法不会修改原始数组。

    ``` javascript
    var wifes_1 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller"];
    var wifes_2 = wifes_1.slice(1);
    var wifes_3 = wifes_1.slice(2, 4);
    console.log(wifes_2.toString());  // "Sagiri,Elaina,Charolotte,Soller"
    console.log(wifes_3.toString());  // "Elaina,Charolotte"
    ```

- `splice()`方法有多种用法，主要用途是向数组的中部插入项：
    - 删除：可以删除任意数量的项，需要指定2个参数，第一个是要删除的第一项的位置，第二个是要删除的项数；
    - 插入：可以向指定位置插入任意数量的项，第一个参数为起始位置，第二个参数为删除的项数（这里要选择0），后续参数就是要插入的项；
    - 替换：可以向指定位置插入任意数量的项，且同时删除任意数量的项，用这种方法可以做到项的替换。

    ```javascript
    var wifes_1 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller"];
    var wifes_2 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller"];
    var wifes_3 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller"];
    wifes_1.splice(1, 2);
    wifes_2.splice(1, 0, "AshGrey");
    wifes_3.splice(1, 2, "AshGrey", "Helix");
    console.log(wifes_1.toString());  // "Izumi,Charolotte,Soller"
    console.log(wifes_2.toString());  // "Izumi,AshGrey,Sagiri,Elaina,Charolotte,Soller"
    console.log(wifes_3.toString());  // "Izumi,AshGrey,Helix,Charolotte,Soller"
    ```

<br>

JavaScript还为数组提供了位置的方法：`indexOf()`和`lastIndexOf()`方法，两个方法都接受两个参数：要查找的项和（可选的）表示查找起点位置的索引。`indexOf()`方法从数组的开头往后查找，`lastIndexOf()`方法从数组的末尾往前查找。这两个函数都会返回查找参数在数列中的索引值，未查找到时两个函数都会返回-1，注意这两个函数在查找过程中的比较方法都是全等：

<br>

``` javascript
var wifes_1 = ["Izumi", "Sagiri", "Elaina", "Charolotte", "Soller", "Sagiri"];
console.log(wifes_1.indexOf("Sagiri"));       // 1
console.log(wifes_1.lastIndexOf("Sagiri"));   // 5
console.log(wifes_1.indexOf(1));              // -1
```

<br>

JavaScript还为数组提供了5个迭代的方法，每个方法都接受两个参数，要在每一项上运行的函数和（可选的）运行该函数的作用域对象。传入这些方法的函数会接受三个参数：数组的项、该项在数组中的位置和数组对象本身：
- `every()`方法：对数组的每一项都执行给定的函数，如果给定函数对每一项都返回`true`（或者返回值经过强制类型转换得到`true`），则函数本身返回`true`，否则返回`false`；

    ``` javascript
    var wifeAge = [12, 14, 9, 13, 15];
    var result = wifeAge.every(function(item, index, array) {
	      if (item >= 14)
            return true;
        else 
            return false;
    });
    console.log(result);  // false
    ```

- `some()`方法：迭代执行，如果给定函数对某一项返回了`true`（或者返回值经过强制类型转换得到`true`），则函数本身返回`true`，否则返回`false`：

    ``` javascript
     var wifeAge = [12, 14, 9, 13, 15];
    var result = wifeAge.some(function(item, index, array) {
	      if (item >= 14)
            return true;
        else 
            return false;
    });
    console.log(result);  // true
    ```

- `filter()`方法：迭代执行，函数返回的是一个数组，数组内只包含使给定函数返回`true`（或者返回值经过强制类型转换得到`true`）的数组元素：

    ``` javascript
    var wifeAge = [12, 14, 9, 13, 15];
    var result = wifeAge.filter(function(item, index, array) {
	      if (item >= 14)
            return true;
        else 
            return false;
    });
    console.log(result);  // Array(2) : [14, 15]
    ```

- `forEach()`方法：迭代执行，该函数没有返回值：

    ``` javascript
    var wifeAge = [12, 14, 9, 13, 15];
    var result = new Array();
    wifeAge.forEach(function(item, index, array) {
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
    var wifeAge = [12, 14, 9, 13, 15];
    var result = wifeAge.map(function(item, index, array) {
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
var Today = new Date();
```

<br>

调用Date构造函数而不传递参数的情况下，新创建的对象将自动获取当前的日期和时间。如果想根据特定的日期和时间创建对象，必须传入表示该日期的毫秒数。JavaScript提供两种方法：
- `parse()`方法：接受一个表示日期的字符串参数，然后尝试根据这个字符串返回相应日期的毫秒数，接受的字符串参数因实现的不同而不同（如果字符串参数无法表示日期，函数将返回`NaN`）；

    ``` javascript
    var day_1 = new Date(Date.parse("6/13/2004"));
    var day_2 = new Date(Date.parse("January 12,2006"));
    var day_3 = new Date(Date.parse("Tue May 25 2004 23:21:09 GMT+0800"));
    var day_4 = new Date(Date.parse("2022-02-03T23:09:00"));
    ```

- `UTC()`方法：UTC的参数分别是年份、基于0的月份（一月份是0，二月份是1）、月中的哪一天、小时数、分钟、秒及毫秒。

<br>

### 3.4 RegExp类型

<br>

JavaScript支持通RegExp类型来支持正则表达式。其语法如下：

<br>

``` plaintext
var expression = / pattern / flags
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
    var str = "Is is the cost of gasoline going up up";
    var pattern1 = /\b([a-z]+) \1\b/igm;
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
var pattern1 = /[bc]at/i;
var pattern2 = new RegExp("[bc]at", "i");
```

<br>

需要注意的是由于RegExp构造函数的参数是字符串，所以需要对元字符双重转义（因为字符`\`需要被转义，所以会变成`\\`），又因为`\`在正则表达式中需要被转义成`\\`，所以在RegExp构造函数中的字符串又被转义为`\\\\`。如下：

<br>

``` javascript
var pattern1 = /\[bc\]at/;  // RegExp("\\[bc\\]at")
var pattern2 = /\.at/;      // RegExp("\\.at")
var pattern3 = /name\/age/; // RegExp("name\\/age")
var pattern4 = /\d.\d{1,2}/;// RegExp("\\d.\\d{1,2}")
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
    var str = "67root@123root12-90";
    var pattern = /(?<=\d+)root/igm;
    var matches1 = pattern.exec(str);
    console.log(matches1.index);
    var matches2 = pattern.exec(str);
    console.log(matches2.index); 
    ```

    即使正则表达式设置了g标志，`exec()`方法每次也只能返回一个匹配项。在不设置g标志时，`exec()`方法每次调用都只能返回第一个匹配项；在设置g标志时，`exec()`方法每次调用都会继续查找新的匹配项；
- `test()`：该方法接受一个字符串参数，当与正则表达式匹配时，返回`true`，不匹配时返回`false`。

<br>

### 3.5 Function类型

<br>

在JavaScript中，函数实际是一种对象。每个函数都是Function类型的实例，函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定，于是函数的定义也可以这么写：

<br>

``` javascript
var sum = function(num1, num2) {
    return num1 + num2;
};

// var sum = new Function("num1", "num2", "return num1 + num2");
var result1 = sum(1, 2);
var anotherSum = sum;
sum = null;
var result2 = anotherSum(1,2);
```

<br>

函数表达式与函数声明是有区别的，解析器在向执行环境中加载数据时，对函数声明和函数表达式的加载顺序是有先后的。解析器会率先读取函数声明，并使其在执行任何代码之前可用；而函数表达式则必须等到解析器执行到它所在的代码行才会被真正解析：

<br>

``` javascript
haveHoisted();
function haveHoisted() {
    console.log("I love Sagiri!");
}

notHoisted();   // TypeError: notHoisted is not a function
var notHoisted() {
    console.log("I love Sagiri!");
};
```

<br>

在代码开始执行之前，解析器就已经通过一个名为函数声明提升的过程，读取并将函数声明添加到执行环境中。对代码求值时，JavaScript引擎会首先声明函数并将它们放在源代码树的顶端。对于函数表达式，引擎就不会做出这样的函数声明提升。

<br>

由于函数本身是一种对象，函数也能作为参数传入别的函数中。例如有`object1`和`object2`是一个类的两个对象，现在要根据两个对象的某个属性进行排序：

<br>

``` javascript
function createComparisonFunction(propertyName) {
    return function(object1, object2) {
        var value1 = object1[propertyName];
        var value2 = object2[propertyName];
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

var data = [
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
    var anotherFactorial = factorial;
    factorial = null;
    console.log(anotherFactorial(5));
    ```
- `this`对象：引用的是函数执行的环境对象，例如在网页中全局作用域调用函数时`this`对象引用的是`window`对象：

    ``` javascript
    window.color  = "red";
    var object = { color : "blue"};
    function colorOutput() {
        console.log(this.color);
    }
    colorOutput();  // "red"
    object.colorOutput = colorOutput;
    object.colorOutput();   // "blue"
    ```
- `caller`对象：这个对象中保存着调用当前函数的函数的引用，如果在全局作用域调用当前函数，`caller`的值为`null`