---
title: Common Lisp基础知识笔记
author: AshGrey
date: 2024-11-05 00:00:00 +0800
categories: [Computer Science, Language]
tags: [Computer Science, Common Lisp]
math: true
---

> 本文章所属领域：
>
> [实践产业知识 - 工业产业 - 计算机科学 - 编程语言 - Common Lisp]({% post_url /Computer Science/2024-02-08-计算机科学：索引笔记 %})
{: .prompt-info}

<br>

## 1 Common Lisp 基础语法

Common Lisp 和所有 Lisp 系语言一样，采用前缀表达式，即将操作符放在操作数之前，在 Common Lisp 中使用`;`表示注释：

``` cl
(+ 1 2)        ;; 3
(+ 2 3 4)      ;; 9
(- 4 2)        ;; 2
(/ 1 2)        ;; 0.5
(* 2 3 4)      ;; 24
```

Common Lisp 的求值顺序依旧是从内层括号跳到外层括号。例如对于语句`(/ (+ 2 4) (- 4 2))`是先计算内层的`(+ 2 4)`以及`(- 4 2)`，再执行外层括号内的操作符，此时整个表达式为`(/ 6 2)`，结果为`3`。

大部分的 Common Lisp 操作符可以看作函数，其对之后的实参从左到右进行求值。在 Common Lisp 中有一种特殊的操作符`quote`，这个操作符接受一个实参并原封不动地返回它，在 Common Lisp 中可以用`'`表示`quote`：

``` cl

```