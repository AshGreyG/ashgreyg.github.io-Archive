---
title: wxWidgets学习笔记
author: AshGrey
date: 2024-02-13 00:00:00 +0800
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

> **Q1 如何利用 wxWidgets 库创建一个程序？**

<br>

首先创建一个继承自`wxApp`的自定义程序类`myApp`，并在该类的公有部分重载`OnInit()`虚函数，并在类外使用宏`wxIMPLEMENT_APP`和`wxDECLARE_APP`来创建程序：

<br>

``` cpp
class TetrisApp : public wxApp {
public:
    virtual bool OnInit() {
        if (!wxApp::OnInit())
            return false;
        wxInitAllImageHandlers();
        TetrisMainFrame* mainFrame = new TetrisMainFrame(wxT("Tetris"));
        (*mainFrame).Show();
        (*mainFrame).SetWindowStyle(wxDEFAULT_FRAME_STYLE
                                    & ~(wxMAXIMIZE_BOX)
                                    & ~(wxRESIZE_BORDER));
        return true;
    };
    std::string mAppname = "Tetris";
};
wxIMPLEMENT_APP(TetrisApp);
wxDECLARE_APP(TetrisApp);
```

