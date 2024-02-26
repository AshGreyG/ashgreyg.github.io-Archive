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

JavaScript