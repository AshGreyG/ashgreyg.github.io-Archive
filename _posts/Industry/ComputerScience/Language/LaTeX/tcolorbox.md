> 本文章在一段时间内将更新完毕，请注意查收更新的内容

<br>

## 1 tcolorbox基础命令

<br>

tcolorbox的使用方法为；

``` latex
\begin{tcolorbox}[<options>]
    <environment content>
\end{tcolorbox}
```
<br>

其中\<options\>更改盒子的样式，例如：

``` latex
\begin{tcolorbox}[colback=red!5!white,colframe=red!75!black,title=My nice heading]
    This is another \textbf{tcolorbox}.
    \tcblower
    Here, you see the lower part of the box.
\end{tcolorbox}
```
<br>

|命令|描述|
|:---:|:---:|
|\tcblower|在盒子中分隔出上下两个部分|
|\tcbset{\<options\>}|为之后所有的盒子设定相同的样式，但不会应用在未设置样式而使用默认样式的盒子上|
|\tcbsetforeverylayer{\<options\>}|与\tcbset不同，该命令内的样式也会应用在未设置样式而使用默认样式的盒子上|
|\tcbox[\<options\>]{\<box content\>}|创建一个适应于由\<box content\>给定内容宽度的盒子，该盒子可以具有大部分正常盒子的样式，但不能使用\tcblower|
|\newtcolorbox[\<init options\>]{\<name\>}[\<number\>][\<default\>]{\<options\>}|创建一个名叫\<name\>的环境，其行为类似于\newenvironment，例如创建新的环境themebox，之后可以直接使用\begin{themebox}，\<init options\>用于设定对盒子编号进行自动计数等行为|
|\renewtcolorbox[\<init options\>]{\<name\>}[\<number\>][\<default\>]{\<options\>}|行为与\newtcolorbox类似，但是基于\renewenvironment|
|\newtcbox[\<init options\>]{\<name\>}[\<number\>][\<default\>]{\<options\>}|创建一个新的名叫\<name\>的类似于\tcbox的命令，其行为类似于\newcommand，例如创建新的命令\mybox，之后可以直接使用\mybox{}，可选值的含义与\newtcolorbox相同|
|\renewtcbox[\<init options\>]{\<name\>}[\<number\>][\<default\>]{\<options\>}|行为与\newtcbox类似，但是基于\renewcommand|
|\tcolorboxenvironment{\<name\>}{\<options\>}|将一个已经存在的环境重新定义到tcolorbox的环境当中|


<br>

以上命令的一些例子：

``` latex
%\tcbset
\tcbset{
    colframe    = blue!50!back,
    colback     = white,
    colupper    = red!50!black,
    fonttitle   = \bfseries,
    nobeforafter,
    center title
}

%\tcbox
\tcbox[colback=blue!85!black,left=0mm,right=0mm,top=0mm,bottom=0mm,
boxsep=1mm,arc=0mm,boxrule=0.5pt,title=My picture]{
    \includegraphics[width=5cm]{Sagiri.png}
}

%\newtcolorbox
\newtcolorbox[auto counter,number within=section]{themebox}[2][]{
    colback=red!5!white,colframe=red!75!black,fonttitle=\bfseries,
    title=Examp.~\thetcbcounter: #2,#1}

\begin{themebox}{colback=yellow}{Hello there}
    This is a themebox.
\end{themebox}

%\newtcbox
\newtcbox{\mybox}[2][]{colback=red!5!white,colframe=red!75!black,fonttitle=\bfseries,title=#2,#1}

\mybox[colback=yellow]{Hello there}{This is my own box.}

%\tcolorboxenvironment
\newenvironment{myitemize}{\begin{itemize}}{\end{itemize}}

\tcolorboxenvironment{myitemize}{blanker,
before skip=6pt,after skip=6pt,
borderline west={3mm}{0pt}{red}}

Some text.
\begin{myitemize}
    \item Alpha
    \item Beta
    \item Gamma
\end{myitemize}
More text.

```

<br>

## 2 \<options\>中可选的关键字

<br>

### 2.1 Title

<br>

|title类的属性及其值的类型|描述|
|:---:|:---:|
|title=\<text\>|创建一个内容为\<text\>的标题栏|
|notiitle|将一个设置过标题栏样式的盒子主题去除标题栏|
|adjusted title=\<text\>|创建一个内容为\<text\>的标题栏。一般而言盒子标题栏的最小高度是由内容文本决定的，但adjusted title的作用是使相邻的盒子的标题栏高度一致而不管其具体的文本内容|
|adjusted text=\<text\>|\<text\>中的内容不作为标题栏的内容，但是标题栏的高度适应于\<text\>中的文本|
|squeezed title=\<text\>|创建一个以\<text\>为内容的标题栏，如果\<text\>的文本长度比可以提供的空间还要长，则将文本字体的宽度压缩|
|squeezed title*=\<text\>|这结合了adjusted title和squeezed title的功能|
|titlebox=\<mode\>|控制盒子的标题栏内容的可见性，有两个可选值，visible代表可见，invisible代表不可见|
|detach title|将标题内容从其正常位置剥离下来，其值存储在\tcbtitletext命令里，标题的格式和位置关系等由命令\tcbtitle执行。detach title的主要应用是将标题从正常的位置移动到另一个|
|attach title|该命令用于恢复detach title造成的标题偏离|
|attach title to upper=\<text\>|将标题内容移动到文本内容的前端部分，\<text\>的内容作为标题与正文内容之间的内容|

<br>

``` latex
%title=<text>
\begin{tcolorbox}[title=Theorem-1.1]
This is a thorem.
\end{tcolorbox}

%adjusted title=<text>
\tcbset{colback=White,arc=0mm,width=(\linewidth-4pt)/4,
equal height group=AT,before=,after=\hfill,fonttitle=\bfseries}

The following titles are adjusted:\\
\foreach \n in {title=Theorem-1.1,title=Theorem-1.2,title=Theorem-1.3}
{\begin{tcolorbox}[adjusted title=\n,colframe=blue!75!black]
    Some content.
\end{tcolorbox}}

%titlebox=<mode>
\begin{tcolorbox}[title=Theorem-1.1,titlebox=invisible]
This is a \textbf{tcolorbox}
\end{tcolorbox}

```
<br>

### 2.2 Subtitle

<br>

|Subtitle类的属性及其值的类型|描述|
|:---:|:---:|
|\tcbsubtitle[<options>]{\<text\>}|使用该命令，副标题栏所在的是一个新的盒子，标题内容是\<text\>中的值|
|subtitle style=\<options\>|设计副标题栏及其盒子的样式，当使用\tcbsubtitle调用时使用该样式|

<br>

``` latex
%\tcbsubtitle[<options>]{<text>}
\begin{tcolorbox}[title=My title,
    colback=red!5!white,
    colframe=red!75!black,
    fonttitle=\bfseries]
This is a \textbf{tcolorbox}.
\tcbsubtitle[before skip=\baselineskip]{My subtitle}Further text.
\end{tcolorbox}

%subtitle style=<options>
\begin{tcolorbox}[title=My title,
    colback=red!5!white,
    colframe=red!75!black,
    colbacktitle=yellow!50!red,
    coltitle=red!25!black,
    fonttitle=\bfseries,
    subtitle style={boxrule=0.4pt,
    colback=yellow!50!red!25!white}]
This is a \textbf{tcolorbox}.
\tcbsubtitle{My subtitle}
Further text.
\tcbsubtitle{Second subtitle}
Further text.
\end{tcolorbox}
```

<br>

### 2.3 Upper Part

<br>

|Upper Part类的属性及其值的类型|描述|
|:---:|:---:|
|upperbox=\<mode\>|upperbox可选值有visible和invisible两个，visible指upperbox及其样式均可见，invisible指upperbox及其样式均不可见。注意该命令将盒子分为上下两个部分，其中upperbox是强制的，lowerbox是可选的|
|visible|直接设置upperbox、lowerbox和titlebox及其样式均可见|
|invisible|直接设置upperbox、lowerbox和titlebox及其样式均不可见|
|saveto=\<file name\>|将一个盒子中的文本封装起来以便之后继续使用，但注意也包含lowerbox的部分，且该命令与savelowerto=\<file name\>是冲突的。封装之后可以使用命令\input调用|

<br>

``` latex
%saveto=<file name>
\begin{tcolorbox}[saveto=\jobname_mysave2.tex]
This is a \textbf{tcolorbox}.
\tcblower
This is the lower part.
\end{tcolorbox}

Now, we load the saved text:\\

\begin{tcolorbox}[colframe=red,colback=red!10,
    coltitle=black,colbacktitle=red!20,sidebyside,
    title=Here we see the saved content including the lower part]
\input{\jobname_mysave2.tex}
\end{tcolorbox}
```

<br>

### 2.4 Lower Part

<br>

|Lower Part类的属性及其值的类型|描述|
|:---:|:---:|
|lowerbox=\<mode\>|lowerbox可选值有visible和invisible两个，visible指lowerbox及其样式均可见，invisible指lowerbox及其样式均不可见。|
|savelowerto=\<file name\>|将一个盒子中lowerbox的文本封装起来以便之后继续使用，封装之后可以使用命令\input调用|
|lower seperated=true/false|



