---
title: JavaScript中的高级数据类型
author: AshGrey
date: 2024-09-09 00:00:00 +0800
categories: [Computer Science, Language]
tags: [Computer Science, JavaScript]
---

> 本文章所属领域：
>
> [实践产业知识 - 工业产业 - 计算机科学 - 编程语言 - JavaScript]({% post_url /Computer Science/2024-02-08-计算机科学：索引笔记 %})
> 
> 本篇文章因为[「JavaScript基础知识笔记」]({%post_url /Computer Science/2024-02-16-JavaScript基础知识笔记%}) 篇幅有限，故转移至此
{: .prompt-info}

<br>

## 1 可迭代对象

<br>

可迭代对象就是说可以使用`for-of`语句循环的对象。为了让自己设计的对象可迭代，则必须为该对象添加一个`Symbol.iterator`的方法。这是一个专门用于使对象可迭代的内建 symbol，`for-of`循环会调用这个方法：
- 这个方法必须返回一个**迭代器**（iterator），这是一个有`next()`方法的对象；
- 注意`for-of`循环仅适用于这个被返回的对象；
- 当`for-of`循环需要下一个数值时，则调用该对象的`next()`方法；
- `next()`方法返回的结果的格式必须是`{done : Boolean, value : any}`的，当`done`为`true`时，表示循环结束。

<br>

``` javascript
class CollatzRange {
  constructor(init) {
    this.init = init;
    this.end = 1;
  }
  [Symbol.iterator]() {
    this.current = this.init;
    return this;
  }
  next() {
    if (this.current != this.end) {
      if (this.current % 2 == 0) {
        this.current /= 2;
      } else {
        this.current = 3 * this.current + 1;
      }
      return { done : false, value : this.current };
    } else {
      return { done : true };
    }
  }
};

let test = new CollatzRange(100);
let output = "";

for (let value of test) {
  output += value + " -> ";
}

alert(output);
```

<br>

我们需要注意可迭代对象和类数组对象之间的区别：可迭代对象是实现了`next()`以及`Symbol.iterator`的方法的对象，类数组对象（array-like）是有索引和`length`属性的对象。JavaScript 中提供的数组和字符串是既可迭代的又是类数组的。

<br>

全局方法`Array.from`方法接受一个可迭代或者类数组的值，其返回一个真正意义上的数组，可以对返回的这个数组调用数组方法。其第一个参数是可迭代对象或者类数组对象，第二个参数是一个映射函数，其只接受两个参数（`element`和`index`）

<br>

``` javascript
const images = document.querySelectorAll("img");
const sources = Array.from(images, (img) => img.src);
const secureSources = sources.filter((url) => url.startsWith("https://"));

for (let url of sources) {
  alert(url);
}
```

<br>

## 2 内置的高级数据结构

<br>

### 2.1 Map

<br>

与 Python 中的 dict 类似，JavaScript 提供 Map，Map 是一个带键的数据项的集合。其方法和属性如下：

<br>

|属性/方法|说明|
|---:|:---:|
|`new Map()`|创建一个 Map|
|`size`|返回 Map 对象中的键值对数量|
|`set(key, value)`|根据键存储值，返回一个新的 Map 对象|
|`clear()`|移除 Map 对象中所有的键值对|
|`delete(key)`|移除 Map 对象中指定的键值对|
|`entries()`|返回一个新的迭代器对象，该对象存储`[key, value]`|
|`keys()`|和`entries()`类似，该对象存储`key`的值|
|`values()`|和`entries()`类似，该对象存储`value`的值|
|`get(key)`|根据键返回值，如果不存在这个键返回`undefined`|
|`has(key)`|如果`key`存在返回`true`，如果不存在则返回`false`|
|`forEach(fn)`|以插入顺序为 Map 对象中的每个键值对调用一次函数`fn`|

<br>

``` javascript
let testMap = new Map();
testMap.set("name", "AshGrey")
       .set("id", 2022012050)
       .set("age", 20);

for (list of testMap.entries()) {
  alert(list[0] + " : " + list[1]);
}

// default = testMap.entries()
for (list of testMap) {
  alert(list[0] + " : " + list[1]);
}
```

<br>

Map 和 Objetc 之间可以相互转换：从 Object 生成 Map 可以使用`Object.entries()`，从 Map 生成 Object 可以使用`Object.fromEntries()`。

<br>

``` javascript
let testObject = {
  'name' : 'AshGrey',
  'id' : 2022012050,
  'age' : 20
};
let testMap = new Map();
testMap.set('name', 'huaier')
       .set('spouse', 'AshGrey')
       .set('age', 17);


let keyValueListToMap = new Map([
  ['name', 'AshGrey'],
  ['id', 2022012050],
  ['age', 20]
]);
let objectToMap = new Map(Object.entries(testObject));
let mapToObject = Object.fromEntries(testMap);

alert(keyValueListToMap.has('id'));
alert(objectToMap.has('name'));
console.log(mapToObject);
```

<br>

### 2.2 Set