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
> [实践产业知识 - 工业产业 - 计算机科学 - 编程语言 - C++]({% post_url /Computer Science/2024-02-08-计算机科学：索引笔记 %}#C++)
>
> 本文章所需前置知识：
>
> - [C++基础知识]({% post_url /Computer Science/Language/2024-02-08-C++基础知识笔记 %})
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
		std::string mAppname{ "X-chi" };
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
	MyFrame* frame = new MyFrame(wxT("X-chi"));
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
	msg.Printf(wxT("Hello and welcome to X-chi %s"), wxVERSION_STRING);
	wxMessageBox(msg, wxT("About X-chi"), wxOK | wxICON_INFORMATION, this);
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
	SetStatusText(wxT("Welcome to X-chi!"));
}
```

<br>

这个构造函数首先调用它的基类`wxFrame`的构造函数，使用的参数是父窗口（此时还没有父窗口，选择`NULL`）、窗口标识（`wxID_ANY`让程序自己选择一个标识符）和标题。这个基类的构造函数真正创建了一个窗口的实例。除了使用这种调用方式，还可以在构造函数内部显式调用。增加菜单项的`Append()`函数的三个参数为菜单项标识符、菜单上的文本以及一个稍长的帮助字符串（在菜单项被高亮显示时自动显示在状态栏上。`&`符号前导的字符将成为菜单的快捷操作符，在显示的时候中用下划线表示，`t`符号则前导一个全局的快捷键，这个快捷键可以在菜单没有被打开的时候触发菜单功能。

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
mBindButton->Bind(wxEVT_BUTTON, &MyFrame::OnBindBtn, this);
```

<br>

<br>

<br>

## 3 窗口基础知识

<br>

### 3.1 窗口的组成部分与基本行为

<br>

窗口可以被改变大小、自我刷新、可以被显示或者隐藏，它还可以包含别的窗口，也可以不包含任何子窗口。在使用`wxWidgets`生成的窗口，基本都和一个`wxWindow`类或者它的派生类对应。窗口的基本组成部分与基本行为如下：

- **客户区**：指的是窗口中那些能被绘制或者它的子窗口能被放置的子区域，不包括菜单栏、状态栏、工具栏等区域（这些是用于修饰的边框的非客户区）；
- **滚动条**：大部分窗口具有滚动条，这些滚动条通常是窗口自己增加的而不是由应用程序手动增加的。为了优化性能，只有拥有`wxHSCROLL`和`wxVSCROLL`类型的窗口才会自动生成自己的滚动条；
- **光标和鼠标指针**：一个窗口可以拥有一个光标（`wxCaret`，用来显示当前的输入文本的位置）和一个鼠标指针（`wxCursor`，用来显示当前鼠标指针的位置）。当鼠标移入窗口时，`wxWidegts`将自动显示鼠标指针；当一个窗口成为**焦点窗口**时，光标将会显示当前文本的输入位置，或者如果这个焦点是由于鼠标点击造成的，光标将会显示在鼠标指针对应的位置；
- **顶层窗口**：窗口分为顶层窗口（`wxFrame`、`wxDialog`、`wxPopup`等）和其他窗口，只有顶层窗口创建的时候才可以使用`NULL`作为其父窗口，只有顶层窗口是延迟删除的，即当所有的事件处理完毕之后才会被删除。除了`wxPopup`窗口外，所有的顶层窗口都具有一个标题栏和一个关闭按钮；
- **坐标**：窗口的坐标体系通常以左上角为原点，单位是像素。在某个用于窗口绘制的特定的上下文中，原点和比例允许被改变；
- **窗口绘制**：当一个窗口需要重绘的时候，它将接收到两个事件：`wxEVT_ERASE_BACKGROUND`事件用于通知应用程序重新绘制背景，`wxEVT_PAINT`则用于通知重新绘制前景；
- **大小改变**：当一个窗口的大小发生改变时，无论是来自用户的还是来自应用程序的，它都将接收到一个`wxEVT_SIZE`事件。如果这个窗口拥有子窗口，它们可能需要被重新放置和重新计算大小。大多数已经确定的窗口类都有一个默认的大小和位置，这两个值的修改可以在创建窗口的时候修改`wxDefalutSize`和`wxDefaultPosition`实现。每一个控件都实现了`DoGetBestSize`函数，这个函数会返回这个窗口基于其内容、当前字体以及其他因素来说最合理的大小；
- **输入**：任何窗口在任何时候都可以接收鼠标事件，除非这个窗口被禁止使用或者临时限制了鼠标事件的区域；只有当前处于活动状态的窗口才可以收到键盘事件。正在活动状态的窗口会收到`wxEVT_SET_FOCUS`事件，失去焦点的窗口会收到`wxEVT_KILL_FOCUS`事件；
- **窗口的创建与删除**：一般来说，窗口都是在堆上使用`new`分配内存的。大多数窗口可以通过单步创建或双步创建：

	``` cpp
	// One step:
	wxButton* saveButton = new wxButton(this, 100, wxT("Button!"), wxPoint(200, 225));
	// Two steps:
	wxButton* changeButton = new wxButton;
	changeButton->Create(this, 110, wxT("Button?"), wxPoint(200, 250));
	```

	当创建一个窗口类，或者其他任何非顶层的派生类的时候，如果它的父窗口是可见的，那么它也总是可见的，可以使用`Show(false)`使其不可见。顶层窗口在创建的时候通常是不可见的，需要使用`Show(true)`使其可见。窗口是通过调用其`Destroy`函数（对于顶层窗口）或者调用`delete`释放其内存（很少直接调用）。

<br>

### 3.2 基础窗口类

<br>

`wxWindow`是所有类的基类，且`wxWindow`的构造函数原型如下：

<br>

``` cpp
wxWindow(wxWindow* parent,
	wxWindowID id,
	const wxPoint& pos = wxDefaultPosition,
	const wxSize& size = wxDefalutSize,
	long style = 0,
	const wxString& name = wxT("panel"))
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

### 3.3 顶层窗口类

<br>

#### 3.4.1 `wxFrame`类

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
        const wxString& name = wxASCII_STR(wxFrameNameStr))
```

<br>

`wxFrame`需要使用显式调用`Show(true)`才能显示，要删除一个`Frame`窗口，应该使用`Destroy`方法或者`Close`方法而不是`delete`操作符。

<br>

---

<br>

`wxFrame`类中的菜单的类为`wxMenu`，其相关的事件类型有`wxCommandEvent`，`wxUpdateUIEvent`、`wxMenuEvent`和`wxContentMenuEvent`。其中`wxCommandEvent`用于处理菜单命令，无论是弹出菜单命令还是主菜单命令。这种事件宏和工具条上的事件映射宏是一致的，这使得菜单和工具条上的按钮产生的事件可以通过同一个处理函数进行处理。`wxUpdateUIEvent`是在系统空闲的时候产生的，以便给程序一个更新用户界面的机会，例如允许或者禁止一个菜单项，与之相关的事件为`EVT_UPDATE_UI(id, func)`和`EVT_UPDATE_UI_RANGE(id1, id2, func)`。

<br>

`wxMenu`的点击事件为`EVT_MENU(id, func)`和`EVT_MENU_RANGE(id1. id2, func)`。前者在某个菜单项被选中的时候产生，后者在某个范围内菜单项被选中的时候产生。其他事件列举如下：
- `EVT_CONTEXT_MENU(func)`用来处理用户或者通过某个特殊按键或者通过单击鼠标右键产生的弹出一个上下文菜单的请求，关联的处理函数的参数类型为`wxContextMenuEvent`；
- `EVT_COMMAND_CONTEXT_MENU(id, func)`和前者是类似的，多了一个窗口标识符参数；
- `EVT_MENU_OPEN(func)`在菜单即将被打开时产生，`EVT_MENU_CLOSE(func)`在菜单被关闭时产生；
- `EVT_MENU_HIGHLIGHT(id, func)`在某个菜单项被高亮显示时产生，通常用于在主程序的状态栏上产生关于这个菜单项的帮助信息；
- `EVT_MENU_HIGHLIGHT_ALL(func)`在任何一个菜单项被高亮显示的时候产生。
  
<br>

`wxMenu`的成员函数如下：
- `Append`增加一个菜单项，参数为标识符、菜单项标签、帮助信息、菜单项类型（其中有`wxITEM_NORMAL`、`wxITEM_SEPERATOR`、`wxITEM_CHECK`和`wxITEM_RADIO`），也可以使用`AppendSeparator`、`AppendCheckItem`和`AppendRadioItem`来避免手动选择菜单项类型，还有一种`Append`的重载函数支持直接使用`wxMenuItem`来增加一个菜单项，这是在菜单中增加图片或者使用自定义字体的唯一方式； 
	``` cpp
	wxMenuItem* pictureItemExit = new wxMenuItem(mainFrameFileMenu, MyID_EXIT, wxT("E&xit\tAlt+X"));
	pictureItemExit->SetBitmaps(bmpEnaled, bmpDisabled);
	pictureItemExit->SetFont(fontLarge);
	mainFrameFileMenu->Append(pictureItemExit);
	```
- **`AppendSubMenu`用于添加一个子菜单，参数为子菜单变量、子菜单标签；**
- `Insert`在特定的位置插入一个菜单项；
- `Prehend`在菜单最开始的地方插入一个菜单项；
- `Break`在菜单里插入一个中断点，导致下一个插入的菜单项出现在另外一栏，整体效果就是菜单栏变成两倍宽了；
- `Check`标记复选框或者单选框的状态，参数是菜单项标识和一个`Bool`值；
- `Delete`删除并释放一个菜单项，使用菜单项标识符或者`wxMenuItem`指针，如果这个菜单项是一个子菜单，则子菜单不会被删除，释放一个子菜单需要通过`Destroy`函数；
- `Remove`函数移除一个菜单项但是不释放它，而是返回指向它的指针；
- `Enabled`允许或者禁止一个菜单项，`IsEnabled`返回当前的可用状态；
- `FindItem`根据标签或者标识符找到一个菜单项，`FindItemByPosition`根据位置找到一个菜单项；
- `Get`系函数主要用于访问菜单项显示出的一些信息。

<br>

---

<br>

`wxFrame`类中的菜单条的类为`wxMenuBar`，其行为类似`wxMenu`。

<br>

`wxFrame`类中的工具条的类为`wxToolBar`，使用`SetToolBar`和当前`Frame`窗口绑定，则`Frame`窗口将会管理这个工具条的的位置和大小以及释放。例如下面就是一个创建工具条的示例：

<br>

``` cpp
wxSize STANDARD(24, 24);

wxImage saveImage;
saveImage.LoadFile(wxT("resource/xpmImage/save.xpm"), wxBITMAP_TYPE_XPM);
saveImage.Rescale(STANDARD.GetWidth(), STANDARD.GetHeight());

wxBitmap saveBitmap(saveImage);

wxToolBar* editToolBar = new wxToolBar(this, wxID_ANY, wxDefaultPosition, wxDefaultSize);

editToolBar->AddTool(wxID_SAVE, wxT("Save"), saveBitmap, wxT("Save"));
editToolBar->Realize();
SetToolBar(editToolBar);
```

<br>

可以看到对于工具条上的图标按钮，我们需要将导入的`xpm`位图文件的大小设置为标准大小，可以提前设置一个标准的`wxSize`对象，并使用`wxImage`实例化一个传入的位图文件，然后将其作为图标按钮的图标（以传入的位图文件对应的`wxImage`对象作为参数创建一个`wxBitmap`对象）。最后将这个`wxBitmap`绑定到工具栏上。**注意，在VS中，以上`LoadFile`方法中的文件路径不仅需要相对于生成的`.exe`可执行程序，还需要一份拷贝相对于源码的位置**。

<br>

工具条的事件映射宏和菜单条是一样的，既可以使用`EVT_MENU`宏也可以使用`EVT_TOOL`宏，它们的事件处理函数的参数都是`wxCommandEvent`。

<br>

`wxToolBar`的成员函数如下：
- `AddTool`增加一个控件，`InsertTool`在某个位置插入一个控件；
- `AddSeparator`增加一个分割线，基于实现的不同，它可能显示为一条线或者一段空白；
- `AddControl`增加一个控件，`InsertControl`在某个位置插入一个控件；
- `Realize`显示工具栏；

<br>

#### 3.3.3 `wxDialog`类

<br>

一个对话框可以组合任何多个非顶层窗口或者控件。对话框基本分为两种：模式对话框和非模式对话框。模式对话框在应用程序关闭自己之前阻止其他的窗口处理来自应用程序的消息，而一个非模式的对话框的行为更像一个`Frame`窗口，它不会阻止应用程序中的别的窗口继续处理消息和事件。要使用模式对话框，应该调用`ShowModal`函数显示对话框；要使用非模式对话框，应该调用`Show(true)`显示对话框。

<br>

通常来说应该从`wxDialog`类派生出一个自定义的类，以方便处理对话框中的各种事件，一般来说应该在派生类的构造函数中构造其他控件。

<br>

``` cpp
class MyNewFileDialog : public wxDialog {
	private:
		void OnQuit(wxCommandEvent& event) {
			Close();
		}
		wxDECLARE_EVENT_TABLE();
	public:
		MyNewFileDialog(wxWindow* parent) :
		wxDialog(parent, wxID_ANY, wxT("Create New File"), wxPoint(200, 200), wxSize(500, 300)) {
			wxButton* cancelButton = new wxButton(this, MyID_EXIT, wxT("Cancel"), wxPoint(10, 10), wxSize(50, 10));
		}
		~MyNewFileDialog() = default;
};
```

<br>

`wxDialog`的成员函数如下：
- `GetTitle`和`SetTitle`用来操作对话框标题栏上的文本；
- `Iconize`最小化或者从最小化状态恢复，可以使用`IsIconized`函数检测当前的最小化状态；
- `Maximize`最大化或者从最大化状态恢复，可以使用`IsMaximized`函数检测当前的最大化状态；
- `SetIcon`设置对话框的图标，图标会在对话框被最小化的时候显示；
- `ShowModal`用来显示模式对话框，`Show`函数用来显示非模式对话框。

<br>

### 3.4 容器窗口

<br>

#### 3.4.1 `wxPanel`类

<br>

`wxPanel`经常用来摆放那些除了对话框或者`frame`窗口以外的其他控件窗口，它也常被用作`wxNoteBook`控件的页面，它通常使用系统的默认颜色。它通常不具有窗口类型和成员函数。其构造函数如下：

<br>

``` cpp
wxPanel(wxWindow *parent,
        wxWindowID winid = wxID_ANY,
        const wxPoint& pos = wxDefaultPosition,
        const wxSize& size = wxDefaultSize,
        long style = wxTAB_TRAVERSAL | wxNO_BORDER,
        const wxString& name = wxASCII_STR(wxPanelNameStr))
```

<br>

#### 3.4.2 `wxNotebook`类

<br>

`wxNotebook`类提供一个有多个页面的窗口，页面之间可以通过边上的TAB按钮来切换，每个页面通常是一个普通的`wxPanel`窗口或者其衍生类。使用`wxNotebook`的方法是创建一个`wxNotebook`对象，并使用`AddPage`以及`InsertPage`传递一个用来作为页面的窗口指针，或者使用`DeletePage`来删除某个页面。

<br>

`wxNotebook`的窗口类型如下：
- `wxNB_TOP`让标签放在顶部，`wxNB_BOTTOM`让标签放在底部，`wxNB_LEFT`让标签放在左侧，`wxNB_RIGHT`让标签放在右侧；
- `wxNB_FIXEDWIDTH`使所有的标签宽度都相同；
- `wxNB_MULTILINE`可以具有多行标签。

<br>

`wxNotebook`的相关事件有两个：`EVT_NOTEBOOK_PAGE_CHANGED`表示当前页面已经发生改变，`EVT_NOTEBOOK_PAGE_CHANGING`表示当前页面正在发生改变，可以使用`Veto`函数阻止这种变化。

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