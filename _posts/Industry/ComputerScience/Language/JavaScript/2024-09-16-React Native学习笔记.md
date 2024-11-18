---
title: React Native学习笔记
author: AshGrey
date: 2024-09-16 00:00:00 +0800
categories: [Computer Science, Language]
tags: [Computer Science, JavaScript]
---

> 本文章所属领域：
>
> [实践产业知识 - 工业产业 - 计算机科学 - 编程语言 - JavaScript]({% post_url /Computer Science/2024-02-08-计算机科学：索引笔记 %})
> 
> 本文章所需的前置知识：
>
> - [JavaScript基础知识]({%post_url /Computer Science/Language/2024-02-16-JavaScript基础知识笔记%})

<br>

> **Q1 什么是 React 的函数式组件？它们最基础的用法是怎么样的？**

<br>

React 的函数式组件支持返回 JSX，这些组件都是箭头函数。注意 JSX 的标签都是大写字母开头，大部分标签也是和 HTML 标签一样闭合的。这里使用`export default`将这个组件导出。其余组件可以用`<Cat/>`调用这个组件。React 和 React Native 都使用 JSX 语法，JSX 本质就是 JavaScript，所以可以在其中直接使用变量：

<br>

``` jsx
import React from 'react'
import { Text } from 'react-native'

const Cat = () => {
  const name = "neko";
  return (
    <Text>Hello, this is {name} speaking!</Text>
  );
};

export default Cat;
```

<br>

可以把包含其他组件的组件称为**父组件**或者**父容器**，其中的子组件都可以有不同的属性。JSX 标签的属性和 HTML 标签是类似的，但是注意在 JSX 中引用的都是 JS 值，必须使用`{}`包裹。在 JS 中定义一个对象也是需要使用`{}`的，所以在定义属性时可能出现双层括号。例如：

<br>

``` jsx
const Bishojo = () => {
  return (
    <View>
      <Image
        source = { {uri: "https://picbed.huaier-ashgrey.top/image/image-1.webp"} }
        style = { {width: 200, height: 200} }
      />
      <Text>There is a bishojo image</Text>
    </View>
  );
};

export default Bishojo;
```

<br>

单纯只有属性的组件无法和用户产生交互，使用 React 的 Hook 函数`useState()`可以为组件添加状态。`useState()`函数接受的参数是给属性设置的初始值，其返回一个由当前的属性值和属性的`set`函数构成的数组，并使用解构赋值传递给属性和设置属性的函数：

<br>

``` jsx
import React, { useState } from 'react'
import { View, Button } from 'react-native'

const Counter = () => {
  const [count, setCount] = useState(0);
  return (
    <>
      <Button
        onPress = { () => {setCount(count + 1);} }
        title = { String(count) }
      />
    </>
  );
};

const App = () => {
  return (
    <View>
      <Counter />
      <Counter />
    </View>
  );
};

export default App;
```

<br>

> **Q2 如何创建最基础的不同界面之间的导航？**

<br>

使用 React Navigation 库，使用`<NavigationContainer>`包裹不同界面的 JSX 标签，不同界面的 JSX 标签都是`<xxx.Screen>`这样的形式，其中`xxx`是自定义的一个对象，这个对象是函数`createNativeStackNavigator()`的返回值。每一个`<xxx.Screen>`标签的`component`参数都必须是对应的界面组件：

<br>

``` jsx
import React from 'react'
import { View, StyleSheet, Text } from 'react-native'
import { NavigationContainer } from '@react-navigation/native'
import { createNativeStackNavigator } from '@react-navigation/native-stack'

const HomeScreen = () => {
  return (
    <View style = { {flex: 1, alignItems: 'center', justifyContent: 'center'} }>
      <Text>Home Screen</Text>
    </View>
  );
};

const DetailsScreen = () => {
  return (
    <View style = { {flex: 1, alignItems: 'center', justifyContent: 'center'} }>
      <Text>Details</Text>
    </View>
  );
};

const Stack = createNativeStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name = "Home" component = {HomeScreen}/>
        <Stack.Screen name = "Details" component = {DetailsScreen}/>
      </Stack.Navigator>
    </NavigationContainer>
  );
};

export default App;
```

<br>

如果要创建导航，则需要在用户与组件交互时触发`navigate`函数，我们可以在主界面的函数式组件的参数中添加一个对象`{navigation}`，并且调用它的`navigate`函数，其中参数为想要跳转的界面的名字（`name`参数）：

<br>

``` jsx
const HomeScreen = ({navigation}) => {
  return (
    <View style = { {flex: 1, alignItems: 'center', justifyContent: 'center'} }>
      <Text>Home Screen</Text>
      <Button
        title = "Go to the Details"
        onPress = { () => navigation.navigate('Details') }
      />
    </View>
  );
};
```

<br>

`navigate`这种导航方式是不能继续跳转多个同样的界面的，如果需要跳转到同样的界面，则需要使用`push`函数。从这些函数的命名可以看出，这种界面的层级顺序可能是使用栈实现的，所以每次调用`push`跳转的都是相同样式的不同界面，而且最新加入的界面也最先退出（栈确实是符合这种逻辑的数据结构）：

<br>

``` jsx
const DetailsScreen = ({navigation}) => {
  return (
    <View style = { {flex: 1, alignItems: 'center', justifyContent: 'center'} }>
      <Text>Details</Text>
      <TextInput
        style = { {height: 40} }
        placeholder = 'Test'
      />
      <Button
        title = "Go to the Details again"
        onPress = { () => navigation.push('Details') }
      />
    </View>
  );
};
```

<br>

同理的还有使用`goBack`回到上一级，使用`popToTop`回到主界面（第一个呈现的界面）。

<br>

> **Q3 React Native 项目的结构一般是怎么样的？**

<br>

我现在给出的项目结构如下：

<br>

``` tree
│
├── /assets
│    ├── /icons
│    └── /images
│
├── /src                        ;; source code
│    ├── /components
│    │    ├── Button.js
│    │    └── Header.js
│    │          ...
│    ├── /contexts              ;; contexts API
│    │    ├── ThemeContext.js
│    │    └── LanguageContext.js
│    │          ...
│    ├── /screens
│    │    ├── HomeScreen.js
│    │    └── SettingScreen.js
│    │          ...
│    ├── /styles                 ;; global styles
│    │
│    └── /translations
│         ├── zh.js
│         └── en.js
│
└── App.js                       ;; entrance file
```

<br>

> **Q4 **