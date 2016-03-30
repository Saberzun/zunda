---
layout:     post
title:      "iOS面试总结"
subtitle:   "阿里巴巴    一面-杭州"
date:       2016-03-30 12:00:00
author:     "zunda"
header-img: "img/post-bg-alibaba.jpg"
catalog:    true
tags:
    - 面试
    - iOS
---
## 问题
首先是做自我介绍，然后介绍一下自己的项目经历，之后就是提问。

### 常用的controller

![iOS UIView](/img/UIKit.jpg)

UINavigationController 中A--》B--》C，如何直接从C中跳转回A<br>
__可以多次调用popViewControllerAnimated     <br> 或者使用popToRootViewControllerAnimated直接回到root view__

### 常用的开源框架

SDWebImage

### iOS Crash检测

#### 异常处理机制

任何语言都有异常的处理机制，Objective-C也不例外。与C++/Java类似的语法，它也提供@try, @catch, @throw, @finally关键字。使用方法如下

```
@try {
    ...
}
@catch (CustomException *ce) {
    ...
}
@catch (NSException *ne) {
// Perform processing necessary at this level.
    ...
}
@catch (id ue) {
    ...
}
@finally {
// Perform processing necessary whether an exception occurred or not.
    ...
}
```

#### Breakpoint
调试出现bug造成crash时，左侧窗口选择Breakpoint界面.
![creash](/img/breakpoint_1.jpg)


点击最底端的"+"按钮，添加Add Exception BreakPoint，这个就是捕获所有的exception, 貌似stackoverflow上说，bad_access那种错误无法捕获的，这个用于捕获那些SIGSEGV 的错误
![creash](/img/breakpoint_2.jpg)
![creash](/img/breakpoint_3.jpg)

Symbolic breakpoint的添加前两步和一基本是一样的，只是在第二步选择的时候选 Add Symbolic BreakPoint,添加完成之后添加上objc_exception_throw
![creash](/img/breakpoint_4.jpg)

完成，添加完成只两个断点之后，程序中很多异常也可以捕获了，直接定位到出问题的位置。当然程序中最好也加上异常处理的代码，可以参考这个处理，一般都是这个方式处理的.

### iOS Project中配置文件的作用

__.plist__ 全名为property list，属性列表文件，用来存储串行化后的对象文件，文件是xml格式的，主要作用是用来获取Bundle数据。

__.pch__ Xcode 5之前是自动生成的，pch文件的内容可以被所有源文件共享和访问。主要存放一些全局的宏和一些头文件。

__.xcassets__ 图片资源文件，这个包中不再需要为多倍像素的图片文件分别命名。

### iOS Project编译过程

__预处理-->编译-->汇编-->链接-->打包__

预处理：将宏命令和头文件转化相应的源代码

编译：将预处理的代码编译为汇编码。
编译过程可分为6步：扫描（词法分析）、语法分析、语义分析、源代码优化、代码生成、目标代码优化。

汇编：将汇编码转化为机器码

链接：通过链接器来链接程序运行所需要的目标文件，以及所依赖的其他库文件，最后生成可执行代码。

### MVVM（Model-View-ViewModel）

#### MVC和MVP（Model-View-Presenter）的区别

作为一种新的模式，MVP与MVC有着一个重大的区别：在MVP中View并不直接使用Model，它们之间的通信是通过Presenter (MVC中的Controller)来进行的，所有的交互都发生在Presenter内部，而在MVC中View会直接从Model中读取数据而不是通过 Controller。<br>

![mvc](/img/mvc.jpg)
![mvp](/img/mvp.jpg)

#### MVC存在的问题

MVC里，View是可以直接访问Model的！从而，View里会包含Model信息，不可避免的还要包括一些业务逻辑。 在MVC模型里，更关注的Model的不变，而同时有多个对Model的不同显示，及View。所以，在MVC模型里，Model不依赖于View，但是View是依赖于Model的。不仅如此，因为有一些业务逻辑在View里实现了，导致要更改View也是比较困难的，至少那些业务逻辑是无法重用的。

#### MVP如何解决MVC的问题

在MVP里，Presenter完全把Model和View进行了分离，主要的程序逻辑在Presenter里实现。而且，Presenter与具体的View是没有直接关联的，而是通过定义好的接口进行交互，从而使得在变更View时候可以保持Presenter的不变，即重用！ 不仅如此，我们还可以编写测试用的View，模拟用户的各种操作，从而实现对Presenter的测试--而不需要使用自动化的测试工具。 我们甚至可以在Model和View都没有完成时候，就可以通过编写Mock Object（即实现了Model和View的接口，但没有具体的内容的）来测试Presenter的逻辑。

#### MVP的优点

1、模型与视图完全分离，我们可以修改视图而不影响模型<br>
2、可以更高效地使用模型，因为所有的交互都发生在一个地方——Presenter内部<br>
3、我们可以将一个Presenter用于多个视图，而不需要改变Presenter的逻辑。这个特性非常的有用，因为视图的变化总是比模型的变化频繁。<br>
4、如果我们把逻辑放在Presenter中，那么我们就可以脱离用户接口来测试这些逻辑（单元测试）

#### MVVM介绍

MVVM（Model-View-ViewModel）框架的由来便是MVP（Model-View-Presenter）模式与WPF结合的应用方式时发展演变过来的一种新型架构框架,MVVM 模式便是使用的是__数据绑定__基础架构.View绑定到ViewModel，然后执行一些命令在向它请求一个动作。而反过来，ViewModel跟Model通讯，告诉它更新来响应UI.

![MVVM](/img/MVVM.jpg)

#### MVVM优点

MVVM模式和MVC模式一样，主要目的是分离视图（View）和模型（Model），有几大优点：

1. 低耦合。视图（View）可以独立于Model变化和修改，一个ViewModel可以绑定到不同的"View"上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。
2. 可重用性。你可以把一些视图逻辑放在一个ViewModel里面，让很多view重用这段视图逻辑。
3. 独立开发。开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计，使用Expression Blend可以很容易设计界面并生成xaml代码。
4. 可测试。界面素来是比较难于测试的，而现在测试可以针对ViewModel来写。

### iOS appStore 上架流程

 [iOS App 提交上架store 详细流程]( http://blog.csdn.net/tt5267621/article/details/39430659
 )
