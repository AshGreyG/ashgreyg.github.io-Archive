---
title: wxWidgets学习笔记
author: AshGrey
date: 2024-02-13 00:00:00 +0800
categories: [Coumputer Science, Language]
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
>
> 本文章更类似wxWidgets的中文文档😀
{: .prompt-info}

<br>

## 1 `wxWidgets`引入

<br>

### 1.1 应用程序类引入

<br>

每个`wxWidgets`程序都要定义一个`wxApp`类的子类，并且需要创建并且只能创建一个这个子类的实例，该实例控制整个程序运行。该子类必须至少定义一个`OnInit()`函数，当程序准备运行代码时，它将调用这个函数。创建这个子类的实例是在`wxWidgets`内部代码中实现的，但是需要由程序员提供该创建哪个`wxApp`的子类的实例，所以需要提供一个名为`wxIMPLEMENT_APP`的宏，其后的括号内为自定义的应用程序类。

<br>

创建应用程序类的实例之后，`wxWidgets`将结果保存在全局变量`wxTheApp`中，使用的时候需要强制转化为自定的应用程序类。可以使用宏`wxDECLARE_APP`定义一个全局函数`wxGetApp()`，这个函数会返回一个到自定义的应用程序类的实例的引用。

<br>

``` cpp
class MyApp : public wxApp {
    public:
        virtual bool OnInit() wxOVERRIDE;
        std::string mAppname{ "MyApp" };
};
wxIMPLEMENT_APP(MyApp);
wxDECLARE_APP(MyApp);
```

<br>

在`OnInit()`函数中，通常应该创建至少一个窗口实例，该实例的创建方法一般是使用`new`方法创建指向自定义的窗口子类的指针。`OnInit()`函数对传入的命令行参数进行解析，为应用程序进行数据设置和其他初始化操作。如果这个函数返回值为`true`，`wxWidgets`将进入**事件循环**用于接收用户输入；如果返回值为`false`，`wxWidgets`将释放内部已分配的资源并结束运行。

<br>

``` cpp
bool MyApp::OnInit() {
    if (!wxApp::OnInit())
        return false;
    MyFrame* frame = new MyFrame(wxT("MyApp"));
    frame->Show(true);
    return true;
}
```

<br>

### 1.2 窗口类与事件处理函数引入

<br>

自定义的窗口类需要继承自基类`wxFrame`，一个`Frame`窗口是可以容纳别的窗口的顶层窗口，通常拥有一个标题栏和一个菜单栏，这个类的定义结构基本如下，其中`public`部分有一个构造函数和析构函数，`private`部分有一系列**事件处理函数**和一个`wxDECLARE_EVENT_TABLE`宏，该宏告诉程序这个类想要处理某些事件。

<br>

``` cpp
class MyFrame : public wxFrame {
    private:
        void OnAbout(wxCommandEvent& event);
        void OnQuit(wxCommandEvent& event);
        wxDECLARE_EVENT_TABLE();
    public:
        MyFrame(const wxString& title);
        ~MyFrame() = default;
};
```

<br>

事件表是一组位于类的实现文件中的宏，用于将来自用户或者其他地方的事件与类的成员函数（事件处理函数）对应起来，例如下面的事件表就将**标识符**分别为`wxID_ABOUT`和`wxID_EXIT`的菜单事件与`MyFrame`类的成员函数`OnAbout()`和`OnQuit()`关联起来。这里的标识符是`wxWidgets`预定义的，通常应该自己通过枚举、常量或者宏定义的方式定义自己的标识符。

<br>

``` cpp
wxBEGIN_EVENT_TABLE(MyFrame, wxFrame)
    EVT_MENU(wxID_ABOUT, MyFrame::OnAbout)
    EVT_MENU(wxID_EXIT, MyFrame::OnQuit)
wxEND_EVENT_TABLE()
```

<br>

事件处理函数定义如下：

<br>

``` cpp
void MyFrame::OnAbout(wxCommandEvent& event) {
    wxString msg;
    msg.Printf(wxT("Hello and welcome to MyApp %s"), wxVERSION_STRING);
    wxMessageBox(msg, wxT("About MyApp"), wxOK | wxICON_INFORMATION, this);
}

void MyFrame::OnQuit(wxCommandEvent& event) {
    Close();
}
```

<br>

当用户点击关于菜单项时，`MyFrame::OnAbout()`函数弹出一个消息框，这里使用了`wxWidgets`提供的`wxMessageBox`，它的四个参数分别是消息内容、标题、窗口类型及父窗口。当用户点击退出菜单项时，它调用`wxFrame`类的`Close()`函数来释放窗口（此时没有其他子窗口存在，触发应用程序的退出），该函数实现释放窗口的过程如下：产生一个`wxEVT_CLOSE_WINDOW`事件，该事件默认的处理函数调用`wxWindow::Destroy()`函数释放了窗口。

<br>

以下是`Frame`窗口的构造函数，其中实现了窗口的图标、菜单条（顶部）和状态条（底部）：

<br>

``` cpp
MyFrame::MyFrame(const wxString& title) : wxFrame(NULL, wxID_ANY, title) {
    wxMenu* fileMenu = new wxMenu;
    wxMenu* helpMenu = new wxMenu;
    (*helpMenu).Append(wxID_ABOUT, 
                       wxT("&About...\tF1"),
                       wxT("Show about dialog"));
    (*fileMenu).Append(wxID_EXIT,
                       wxT("E&xit\tAlt-X"),
                       wxT("Quit this program"));
    wxMenuBar* menuBar = new wxMenuBar();
    (*menuBar).Append(fileMenu, wxT("&File"));
    (*menuBar).Append(helpMenu, wxT("&Help"));
    SetMenuBar(menuBar);
    CreateStatusBar(3);
    SetStatusText(wxT("Welcome to MyApp!"));
}
```

<br>

这个构造函数首先调用它的基类`wxFrame`的构造函数，使用的参数是父窗口（此时还没有父窗口，选择`NULL`）、窗口标识（`wxID_ANY`让程序自己选择一个标识符）和标题。这个基类的构造函数真正创建了一个窗口的实例。除了使用这种调用方式，还可以在构造函数内部显式调用。增加菜单项的`Append()`函数的三个参数为菜单项标识符、菜单上的文本以及一个稍长的帮助字符串（在菜单项被高亮显示时自动显示在状态栏上。`&`符号前导的字符将成为菜单的快捷操作符，在显示的时候中用下划线表示，`t`符号则前导一个全局的快捷键，这个快捷键可以在菜单没有被打开的时候触发菜单功能。

<br>

### 1.3 wxWidgets的数据结构类

<br>

<br>

<br>

## 2 事件处理

<br>

### 2.1 静态事件处理过程

<br>

静态事件处理过程在程序编译期就已经确定，映射关系在程序运行期间就已经不能进行更改。静态事件处理过程实际是最常用的处理事件的方式。

<br>

所有继承自`wxWindow`的窗口类、应用程序类都是`wxEvtHandler`的派生类，每一个派生类都会在内部维护一个事件表，用来告诉程序事件和事件处理过程的对应关系。创建一个静态的事件表，实现一个静态事件处理过程的方法如下：
- 定义一个直接或间接继承自`wxEvtHandler`的派生类
- 为每一个处理的事件定义一个事件处理函数
- 在这个类中使用宏`DECALRE_EVENT_TABLE`声明事件表
- 在`.cpp`文件中使用宏`BEGIN_EVENT_TABLE`和`END_EVENT_TABLE`实现一个事件表
- 在事件表中增加事件宏，实现事件到事件处理过程的映射

<br>

所有的事件处理函数都具有相同的形式，它们的返回值都是`void`，且都不是虚函数，且它们只有一个事件对象作为参数，而这个事件对象的类型是随要处理的事件的变化而变化的。事件与事件处理函数之间的匹配是通过事件表实现的，如果`wxButton`类对应的按钮被点击，程序将首先搜索`wxButton`类中的事件表是否定义了相应的事件处理函数，如果没有，将搜索`wxButton`类的父窗口对应的类中的事件表，以此类推；也有一些事件的搜索机制是不会传递给其父窗口事件表的，这些事件通常只与当前窗口相关，传递给其他窗口是没有意义的。

<br>

可以使用`wxWindow::PushEventHandler`函数将事件处理器压入栈中，位于顶部的事件处理器将优先相应事件；可以使用`wxWindow::PopEventHandler`函数将事件处理器弹出栈。这样可以灵活地控制相应事件时窗口的优先级，尤其是在子窗口较多的时候很有用处。

<br>

``` cpp
class MyCustomEventHandler : public wxEvtHandler {
    public:
        MyCustomEventHandler();
        void OnCustomHandler(wxCommandEvent& event);
};


wxButton* clickButton = new wxButton(this,
                                     wxID_OK,
                                     wxT("OK!"),
                                     wxPoint(200, 200));
(*clickButton).PushEventHandler(new MyCustomEventHandler);
```

<br>

### 2.2 动态事件处理过程

<br>

动态事件处理过程在程序运行期改变事件表的映射关系，动态映射的方法可以使程序员更精确地控制事件表的细节，前面的堆栈压入弹出事件处理器的方法只能针对整个事件表进行处理。动态事件处理还允许在不同的类之间共享事件函数。

<br>

和动态事件处理过程相关的API有：`wxEvtHandler::Bind`和`wxEvtHandler::Unbind`，一般来说不需要使用`Unbind`函数，它将在程序结束运行时自动执行。

<br>

``` cpp
(*mBindButton).Bind(wxEVT_BUTTON, &MyFrame::OnBindBtn, this);
```

<br>

<br>

<br>

## 3 窗口基础知识

<br>

### 3.1 基础窗口类

<br>

`wxWindow`是所有类的基类，且`wxWindow`的构造函数原型如下：

<br>

``` cpp
wxWindow(wxWindow* parent,
         wxWindowID id,
         const wxPoint& pos = wxDefaultPosition,
         const wxSize& size = wxDefalutSize,
         long style = 0,
         const wxString& name = wxASCII_STR(wxFrameNameStr))
```

<br>

上述原型中的`wxWindow* parent`指的是该窗口的父窗口（创建时常用`NULL`指没有父窗口，即顶层窗口，或者用`this`指正在执行创建指令的窗口）；`wxPoint`指的是窗口左上角相对于屏幕左上角的位置，使用像素作为单位；`wxSize`指的是窗口的大小；`style`用来指定窗口的样式或属性，它是一个位掩码，可以通过按位或运算符将多个样式值组合在一起；`wxString`用来指定显示文字，可能是标题栏也可能是标签。虽然不同的窗口，其构造函数不尽相同，但是其参数基本相同，下面是一个例子：

<br>

``` cpp
wxDialog(parent,
         wxID_ANY,
         wxT("Create New File"),
         wxPoint(200, 200),
         wxSize(500, 300));
```

<br>

`wxControl`是一个虚类，它继承自`wxWindow`，用来作为控件的基类（控件指的是那些可以显示数据项并且通常需要相应鼠标或者键盘事件的窗口类）。

<br>

`wxControlWithItems`也是一个虚类，用来作为一些提供包含多个数据项的控件（比如`wxListBox`、`wxCheckListBox`、`wxChoice`和`wxComboBox`等）的基类。这些数据项拥有一个字符串和一个和这个字符串绑定的可选的客户数据，其中客户数据具有两种类型：一种是无类型指针`void*`，这意味这个控件只用于存储客户数据但用不到这些客户数据；另一种是`wxClientData`指针，这种类型的客户数据会在控件被释放或者数据项被清除的时候被自动释放。同一个控件的数据项必须拥有同样的客户区数据类型，且该数据类型是在第一次调用`Append`函数或者使用`SetClientData`、`SetClientObject`函数时确定的。

<br>

### 3.2 顶层窗口类

<br>

#### 3.2.1 `wxFrame`类

<br>

`wxFrame`类是主应用程序窗口的一个通用选择，该窗口具有一个可选的标题栏、一个菜单条、一个工具条、一个状态栏，剩下的区域称为客户区。`Frame`窗口允许包含多个工具条，但是只会自动放置第一个工具条，其余的需要另外布局。`wxFrame`的构造函数如下：

<br>

``` cpp
wxFrame(wxWindow *parent,
        wxWindowID id,
        const wxString& title,
        const wxPoint& pos = wxDefaultPosition,
        const wxSize& size = wxDefaultSize,
        long style = wxDEFAULT_FRAME_STYLE,
        const wxString& name = wxASCII_STR(wxFrameNameStr));
```

<br>

`wxFrame`类能够产生的事件：

<br>

|事件类型|说明|
|:---:|:---:|
|`wxEVT_SIZE`|事件类型常量，用于表示窗口大小改变事件。在wxWidgets中，<br>当用户调整窗口的大小时，会触发`wxEVT_SIZE`事件|
|`wxEVT_MENU_HIGHLIGHT`|事件类型常量，用于表示菜单项高亮事件。在wxWidgets中，<br>当用户将鼠标悬停在菜单项上时，会触发`wxEVT_MENU_HIGHLIGHT`事件|

<br>

`wxFrame`具体的静态事件处理名称：

<br>

|静态事件名称|说明|
|:---:|:---:|
|`EVT_CLOSE(func)`|窗口被关闭的时候将产生事件`wxEVT_CLOSE_WINDOW`|
|`EVT_ICONIZE(func)`|窗口被最小化的时候将产生事件`wxEVT_ICONIZE`|
|`EVT_MENU_OPEN(func)`|菜单条打开时产生的事件|
|`EVT_MENU_CLOSE(func)`|菜单条关闭时产生的事件|
|`EVT_MENU_HIGHLIGHT(id, func)`|ID值为`id`的菜单条选项被鼠标高亮选中时产生的事件|
|`EVT_MENU_HIGHLIGHT_ALL(func)`|所有菜单条中有一个选项被鼠标高亮选中时产生的事件|

<br>

`wxFrame`的构造函数以及`Create`函数中`style`参数可选的值：

<br>

|可选值|说明|
|:---:|:---:|
|`wxDEFAULT_FRAME_STYLE`|定义为`wxMINIMIZE_BOX | wxMAXIMIZE_BOX | wxRESIZE_BORDER | wxSYSTEM_MENU | wxCAPTION | wxCLOSE_BOX | wxCLIP_CHILDREN`|
|`wxICONIZE`|显示窗口最小化（图标化）的按钮，只对Windows系统有效|
|`wxMINIMIZE`|等同于`wxICONIZE`，也是只对Windows系统有效|
|`wxMINIMIZE_BOX`|显示窗口最小化按钮，对所有系统都有效|
|`wxMAXIMIZE`|显示窗口最大化按钮，只对Windows和GTK+系统有效|
|`wxMAXIMIZE_BOX`|显示窗口最大化按钮，当在使用`wxGTK`的情况下，必须同时包含`wxRESIZED_BORDER`|
|`wxCLOSE_BOX`|显示窗口关闭按钮|
|`wxRESIZE_BORDER`|可以通过鼠标拖拽窗口边缘重新设置窗口的大小|
|`wxCAPTION`|在窗口显示一个标签，注意在大多数系统上，这个选项需要`wxMINIMIZE_BOX`<br>`wxMAXMIZE_BOX`以及`wxCLOSE_BOX`同时开启|
|`wxSYSTEM_MENU`|在窗口的标题栏显示一个系统默认的操作菜单（一般是用右键点击开启）|
|`wxSTAY_ON_TOP`|悬浮显示在所有窗口之上，不被其他窗口遮挡|
|`wxFRAME_FLOAT_ON_PARENT`|悬浮显示在其父窗口上，不被其父窗口遮挡，其父窗口不得是`NULL`|
|`wxFRAME_SHAPED`|窗口大小可以被函数`SetShape()`改变|
|`wxFRAME_NO_TASKBAR`|窗口图标将不会显示在任务栏中|

<br>

`wxFrame`类的非继承公有成员函数：

<br>

``` cpp
void Centre(int direction = wxBOTH);
// 在显示器上将窗口中心化

bool Create(wxWindow* parent, 
            wxWindowID id, 
            const wxString& title, 
            const wxPoint& pos = wxDefaultPosition, 
            const wxSize& size=wxDefaultSize, 
            long style = wxDEFAULT_FRAME_STYLE, 
            const wxString& name = wxFrameNameStr);
// 创建窗口，参数列表与构造函数的参数列表一致

virtual wxStatusBar* CreateStatusBar (int number = 1, 
                                      long style = wxSTB_DEFAULT_STYLE, 
                                      wxWindowID id = 0, 
                                      const wxString& name = wxStatusBarNameStr);
// 在窗口的底部创建一个状态条

virtual wxToolBar* CreateToolBar (long style = wxTB_DEFAULT_STYLE, 
                                  wxWindowID id = wxID_ANY, 
                                  const wxString& name = wxToolBarNameStr);
// 在窗口的顶部或者左侧创建一个工具条

virtual void DoGiveHelp (const wxString& text, bool show);
// 该函数在事件`wxEVT_MENU_HIGHLIGHT`发生时被调用，用于在鼠标光标悬置在菜单条上时提供帮助

virtual wxPoint GetClientAreaOrigin () const;
// 返回用户区的原点坐标

virtual wxMenuBar* GetMenuBar () const;
// 返回指向窗口的菜单条的指针

virtual wxStatusBar* GetStatusBar () const;
// 返回指向窗口的状态栏的指针

virtual wxToolBar* GetToolBar () const;
// 返回指向窗口的工具栏的指针

int GetStatusBarPane () const;
// 返回状态条分立出来的方格数

virtual wxStatusBar* OnCreateStatusBar (int number, 
                                        long style, 
                                        wxWindowID id, 
                                        const wxString& name);
// 该函数在`CreateStatusBar()`函数调用之后被调用，基本不用手动管理它

virtual wxToolBar* OnCreateToolBar (long style, 
                                    wxWindowID id, 
                                    const wxString& name);
// 该函数在`CreateToolBar()`函数调用之后被调用，基本不用手动管理它

bool ProcessCommand (int id);
// 模拟一个菜单条的操作命令

virtual void SetMenuBar (wxMenuBar* menuBar);
// 将菜单栏绑定到窗口上

virtual void SetStatusBar (wxStatusBar* statusBar);
// 将状态条绑定到窗口上

virtual void SetToolBar (wxToolBar* toolBar);
// 将状态条绑定到窗口上

virtual void SetStatusText (const wxString& text, 
                            int number = 0);
// 设置状态栏第`number`个分割栏的字符串内容为`text`

virtual void SetStatusWidths (int n, 
                              const int *widths_field);
// 设置状态栏的宽度
```

<br>

#### 3.2.2 `wxDialog`类

<br>

一个对话框可以组合任何多个非顶层窗口或者控件。对话框基本分为两种：模式对话框和非模式对话框。模式对话框在应用程序关闭自己之前阻止其他的窗口处理来自应用程序的消息，而一个非模式的对话框的行为更像一个`Frame`窗口，它不会阻止应用程序中的别的窗口继续处理消息和事件。要使用模式对话框，应该调用`ShowModal`函数显示对话框；要使用非模式对话框，应该调用`Show(true)`显示对话框。

<br>

通常来说应该从`wxDialog`类派生出一个自定义的类，以方便处理对话框中的各种事件，一般来说应该在派生类的构造函数中构造其他控件。`wxDialog`类的构造函数如下：

<br>

``` cpp
wxDialog(wxWindow* parent, 
         wxWindowID id, 
         const wxString& title, 
         const wxPoint& pos = wxDefaultPosition, 
         const wxSize& size = wxDefaultSize, 
         long style = wxDEFAULT_DIALOG_STYLE, 
         const wxString& name = wxDialogNameStr);
```

<br>

`wxDialog`类具体的静态事件处理名称：

<br>

|静态事件名称|说明|
|:---:|:---:|
|`EVT_CLOSE(func)`|对话框关闭的事件|
|`EVT_INIT_DIALOG(func)`|对话框初始化的事件|

<br>

`wxFrame`的构造函数以及`Create`函数中`style`参数可选的值（代码块中的为和前面某种控件一致的属性，含义基本相同；表格中为前面所有控件都没有的属性，以下不再复述）：

<br>

``` plaintext
wxDEFAULT_DIALOG_STYLE = wxCAPTION | wxCLOSE_BOX | wxSYSTEM_MENU

wxMINIMIZE_BOX      wxMAXIMIZE_BOX      wxCLOSE_BOX
wxSYSTEM_MENU       wxRESIZE_BORDER     wxSTAY_ON_TOP
wxCAPTION
```

<br>

|可选值|说明|
|:---:|:---:|
|`wxDIALOG_NO_PARENT`|一个父窗口为`NULL`的对话框会被wxWidgets强制绑定到顶层窗口上，此时<br>对话框的父窗口变成该顶层窗口，这个可选值是阻止这一过程的，使对话<br>框强制为一个没有父窗口的孤立窗口|

<br>

`wxDialog`类的成员函数（功能和前面介绍过的类的成员函数相近时不再有注释）：

<br>

``` cpp
void Centre(int direction = wxBOTH);
bool Create(wxWindow* parent, 
            wxWindowID id, 
            const wxString& title, 
            const wxPoint& pos = wxDefaultPosition, 
            const wxSize& size = wxDefaultSize, 
            long style = wxDEFAULT_DIALOG_STYLE, 
            const wxString& name = wxDialogNameStr);

void AddMainButtonId(wxWindowID id);
// 设定一个非滚动的对话框中的按钮为默认确定按钮

virtual bool CanDoLayoutAdaptation();
// 当一个对话框可以使用函数`DoLayoutAdaptaion()`时返回`true`，这常常是在对话框尺寸较大时发生的

wxSizer* CreateButtonSizer(long flags);
// 创建一个由标准按钮组成的窗口布局控件，其中`flag`的值可以是以下几个值的组合：
// wxOK | wxCANCEL | wxYES | wxNO | wxAPPLY | wxCLOSE | wxHELP | wxNO_DEFAULT

wxSizer* CreateSeparatedButtonSizer(long flags);
// 同样是创建一个由标准按钮组成的窗口布局控件，但是它和对话框其他部分之间会用静态的分割线隔开

wxSizer* CreateSeparatedSizer(wxSizer* sizer);
// 将给定的窗口布局控件与对话框其他部分隔开，生成一条静态的分割线，这个窗口布局控件不得是`null`

wxSizer* CreateTextSizer(const wxString& message,
                         int widthMax = -1);
// 创建一个由标准静态文本标签组成的窗口布局控件，
```

<br>

### 3.3 容器窗口类

<br>

#### 3.3.1 `wxPanel`类

<br>

`wxPanel`经常用来摆放那些除了对话框或者`frame`窗口以外的其他控件窗口，它也常被用作`wxNoteBook`控件的页面，它通常使用系统的默认颜色。它通常不具有窗口类型和成员函数。其构造函数如下：

<br>

``` cpp
wxPanel(wxWindow* parent,
        wxWindowID winid = wxID_ANY,
        const wxPoint& pos = wxDefaultPosition,
        const wxSize& size = wxDefaultSize,
        long style = wxTAB_TRAVERSAL | wxNO_BORDER,
        const wxString& name = wxASCII_STR(wxPanelNameStr))
```

<br>

#### 3.3.2 `wxNotebook`类

<br>

`wxNotebook`类提供一个有多个页面的窗口，页面之间可以通过边上的TAB按钮来切换，每个页面通常是一个普通的`wxPanel`窗口或者其衍生类。其构造函数如下：

<br>

``` cpp
wxNotebook(wxWindow* parent, 
           wxWindowID id, 
           const wxPoint& pos = wxDefaultPosition, 
           const wxSize& size = wxDefaultSize, 
           long style = 0, 
           const wxString& name = wxNotebookNameStr);
```

<br>

`wxNotebook`类具体的静态事件处理名称：

<br>

|静态事件处理名称|说明|
|:---:|:---:|
|`EVT_NOTEBOOK_PAGE_CHANGED(id, func)`|`wxNotebook`的选中页面发生了变化|
|`EVT_NOTEBOOK_PAGE_CHANGING(id, func)`|`wxNotebook`的选中页面正要发生变化|

<br>

`wxNotebook`

<br>

### 3.5 非静态控件类

<br>

#### 3.5.1 `wxButton`类

<br>

`wxButton`类控件的外观是一个可以按下的按钮，它拥有一个文本标签，它可以放在对话框、面板等几乎所有的窗口，当用户点击它时会产生一个命令类型的事件。`wxButton`的构造函数如下：

<br>

``` cpp
wxButton(wxWindow *parent,
         wxWindowID id,
         const wxString& label = wxEmptyString,
         const wxPoint& pos = wxDefaultPosition,
         const wxSize& size = wxDefaultSize,
         long style = 0,
         const wxValidator& validator = wxDefaultValidator,
         const wxString& name = wxASCII_STR(wxButtonNameStr))
```

<br>

`wxButton`可以创建类型为`wxCommandEvent`的事件，在事件表中用`EVT_BUTTON`响应该事件。例如：

<br>

``` cpp
wxBEGIN_EVENT_TABLE(MyNewFileDialog, wxDialog)
    EVT_BUTTON(MyID_EXIT, MyNewFileDialog::OnQuit)
    EVT_BUTTON(MyID_OK, MyNewFileDialog::OnOk)
wxEND_EVENT_TABLE()
```

<br>

`wxButton`的成员函数`SetLabel`和`GetLabel`用来操作按钮标签文本，可以使用`&`前导来指示用于这个按钮的快捷键。`SetDefault`将按钮设置为其父窗口的默认按钮，这样用户按回车键就相当于用左键点击了这个按钮。

<br>

#### 3.5.2 `wxTextCtrl`类

<br>

`wxTextCtrl`是用于显示和编辑文本的控件，它支持单行和多行的文本编辑。其构造函数如下：

<br>

``` cpp
wxTextCtrl(wxWindow *parent, wxWindowID id,
           const wxString& value = wxEmptyString,
           const wxPoint& pos = wxDefaultPosition,
           const wxSize& size = wxDefaultSize,
           long  style = 0,
           const wxValidator& validator = wxDefaultValidator,
           const wxString& name = wxASCII_STR(wxTextCtrlNameStr));
```

<br>

### 3.6 静态控件

<br>

#### 3.6.1 `wxStaticText`类

<br>

静态文本控件用来显示一行或多行的静态文本，其构造函数如下：

<br>

``` cpp
wxStaticText(wxWindow *parent,
             wxWindowID id,
             const wxString& label,
             const wxPoint& pos = wxDefaultPosition,
             const wxSize& size = wxDefaultSize,
             long style = 0,
             const wxString& name = wxASCII_STR(wxStaticTextNameStr))
```

<br>

在静态文本控件的标签字符串上的`&`前导符定义一个快捷键，用该快捷键可以直接访问到下一个非静态控件。

<br>

`wxStaticText`类的窗口类型为：
- `wxALIGN_LEFT`使标签左对齐，`wxALIGN_RIGHT`使标签右对齐，`wxALIGN_CENTER`使标签在水平方向上居中对齐；
- `wxST_NO_AUTORESIZE`使得标签无法自动改变自身的大小，一般与对齐的窗口类型一起使用。

<br>

### 3.7 窗口布局控件

<br>

#### 3.7.1 布局控件的通用特性

<br>

所有的布局控件都是容器，它们都是用来容纳一个或者多个别的窗口或元素的。所有的子元素都具有以下特性：
- **最小大小**：布局控件中的每个元素都有计算自己的最小大小的能力，这是这个元素的自然大小。有一些控件只具有自然高度而不具有自然宽度或者只具有自然宽度；
- **边界**：每个元素都具有和别的元素分开的空白区域，这一区域称为边界，一般设置为5个像素；
- **对齐方式**：每个元素都可以被居中或者对齐某个边的方式放置。对于大部分控件来说，在水平方向对齐和垂直方向对齐中只有一个方向是有效的

<br>

#### 3.7.2 布局控件`wxBoxSizer`

<br>

`wxBoxSizer`可以将它的容器子元素进行横向（选择`wxHORIZONTAL`）或者纵向（选择`wxVERTICAL`）的排列。可以使用`Add`方法增加一个子元素，并使用`SetSizer`方法将布局控件（布局管理器）依附到一个父窗口：

<br>

``` cpp
wxBoxSizer* rightBoxSizer = new wxBoxSizer(wxVERTICAL);
rightBoxSizer->Add(outputField, 1, wxEXPAND | wxALL, 0);
rightBoxSizer->Add(eventPanel, 1, wxEXPAND | wxALL, 0);
rightPanel->SetSizer(rightBoxSizer);
```

<br>

注意当一个布局控件中有其他布局控件时，布局控件之间依附于父窗口的`SetSizer`方法顺序是不同的：**父子关系更深的布局控件应当先使用`SetSizer`方法，再逐步调用更浅的布局控件的`SetSizer`方法。**

<br>

`wxBoxSizer`的函数`Add`接受四个参数：增加的窗口或者布局控件的指针、缩放因子、一些可选的选项（以比特位列表显示）、边界间隔的大小。常选的选项有：
- `wxGROW`和`wxEXPAND`都表示子元素的大小会和布局控件一起改变大小；
- `wxALL`表示子元素四周都有边界间隔；
- `wxLEFT`、`wxRIGHT`、`wxBUTTON`和`wxTOP`都是将边界间隔置于特定的方向；
- `wxALIGN_CENTER_HORIZONTAL`和`wxALIGN_CENTER_VERTICAL`是居中选项；
- `wxALIGN_LEFT`左对齐，`wxALIGN_RIGHT`右对齐，`wxALIGN_TOP`顶部对齐，`wxALIGN_BOTTOM`底部对齐。

<br>

<br>

<br>

## 4 绘画与打印

<br>

### 4.1 设备上下文

<br>

**设备上下文**，即通常所说的在一个窗口或者一个打印页面上绘画的概念。在`wxWidgets`当中所有的绘画相关的动作都是由设备上下文完成的，且每一个设备上下文都是`wxDC`的一个派生类。程序不会直接在窗口上绘图，而是先创建一个窗口绘画设备上下文，并在这个设备上下文上进行绘画。

<br>

一个设备上下文具有一个坐标体系，它的原点通常在画布的左上角，这个原点的位置可以使用`SetDeviceOrigin`函数进行改变，还可以调用`SetAxisOrientation`进行对坐标轴正方向的更改。设备上下文还可以通过`SetClippingRegion`函数指定一个区域，所有对这个区域之外的部分进行绘画的动作将被忽略，也可以使用`DestroyClippingRegion`函数清除设备上下文当前指定的区域。

<br>

#### 4.1.1 使用`wxClientDC`在窗口客户区进行绘画

<br>

`wxClientDC`用来在非重绘事件处理函数中对窗口的客户区进行绘制。一种替代`wxClientDC`的方法是使用`wxBufferedDC`，它将所有绘画的结果保存在内存中，并在自己被释放的时候一次性将所有内容都传输到窗口上，这使得整个作画的过程更平滑。

<br>

窗口类通常会收到两种和绘画相关的事件：`wxPaintEvent`事件用来绘制窗口客户区的主要图形，`wxEraseEvent`事件用来通知应用程序擦除背景。如果只拦截`wxPaintEvent`事件，默认的`wxEraseEvent`事件处理函数会使用最近一次的`wxWindow::SetBackgroundColour`函数调用设置的颜色或者别的合适的颜色清除整个背景。所以应当自己