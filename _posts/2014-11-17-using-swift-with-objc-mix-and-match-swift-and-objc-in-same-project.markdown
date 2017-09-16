---
layout: post
title: "Using Swift with Cocoa and Objective-C: Mix and Match-Swift and ObjC in the Same Project"
date: 2014-11-17 23:22:01 +0800
comments: true
categories: [swfit]
---

Swift和ObjC的互用性可以让我们在一个工程里包含这两种语言的源文件。我们可以使用这一特性，混合与匹配(Mix and Match)，来写出混合语言的代码。使用混合与匹配，我们可以把app部分的功能用最新的Swift特性来实现，又可以和已有的ObjC代码融合到一起。

<!--more-->

### 混合与匹配概述

ObjC和Swift代码可以在一个项目同时存在，不管项目最开始是ObjC得还是Swift的。我们只需要给项目添加需要的文件即可。

使用混合语言编程的过程对于不同的编译目标是不一样的。下面我们会详细讲解。

![](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/Art/DAG_2x.png)

### 同一个app目标的代码导入

下面的部分说的流程适用于非框架的目标。

####    把ObjC导入到Swift

要把一部分ObjC的文件导入成Swift代码，我们需要使用ObjC桥接头文件(bridging header)来把这些文件暴露给Swift。当我们在添加一个ObjC文件到一个Swift工程或Swift文件到ObjC工程时，Xcode会提醒你来添加这样的ObjC桥接头文件。

![](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/Art/bridgingheader_2x.png)

如果我们接受，Xcode会创建一个头文件，他的名字是工程模块的名字加上"-Bridging-Header.h"。

我们也可以自己添加这个头文件。

我们需要编辑这个头文件来把ObjC暴露给Swift

***同一目标导入ObjC代码给Swift***

1.  在桥接头文件里添加所有我们需要暴露的ObjC的头文件。

``` swift
    #import "XYZCustomCell.h"
    #import "XYZCustomView.h"
    #import "XYZCustomViewController.h"
```

2.  在编译设置里，确保这个ObjC桥接头文件在Swift的编译器-代码生成里有。这个路径应该是相对于工程路径的，和我们给出Info.plist路径一样。通常，我们不需要修改这个。


任何在这个头文件里的公开的ObjC都会在Swift可用。ObjC的功能在这个目标里的任何Swift文件都可见，不需要其他导入操作。我们在Swift里用Swift的语法来使用ObjC代码。

``` swift
let myCell = XYZCustomCell()
myCell.subtitle = "A custom cell"
```

####    把Swift导入ObjC

当我们需要把Swift代码导入到ObjC时，我们需要使用Xcode为这些文件生成的头文件来在ObjC中使用。这些自动生成的头文件是我们把目标里Swift接口转换到ObjC的头文件。这个可以认为是我们Swift代码的囊括头文件(umbrella header)。这个文件的名字是我们模块的名字加上"-Swift.h"

默认的，生成的头文件包含了Swift里标记为public的声明。如果我们的工程包含一个ObjC桥接头文件，它也会包含了那些标记为internal的声明。private的声明就不会出现在这个头文件。除非private的声明同时标记为了@IBAction,@IBOutlet,@objc，否则他们不会暴露给ObjC。

我们不需要做额外的其他工作，只需要在我们的ObjC代码里导入这个头文件即可。注意，这个生成的头文件里的Swift接口包含了所有他们引用到的ObjC类型。如果我们在Swift代码里用到我们自己的ObjC类型，那么我们在需要访问Swift的ObjC的m文件里需要把这些ObjC的头文件先于Swift生成的头文件。

***同一目标导入Swift代码给ObjC***

在需要使用Swift的ObjC的.m文件里导入这个生成的头文件。

``` objc
    #import "ProductModuleName-Swift.h"
```

目标里的Swift文件就会在这个.m文件里可见。

                 导入Swift                 导入ObjC
    Swift代码     不需要导入语句             #import "ProductModuleName-Swift.h"       
    ObjC代码      不需要导入语句，           #import "Header.h"
                 ObjC桥接头文件


### 同一个框架目标的代码导入

如果我们用多语言编写一个框架，我们也会需要在从ObjC访问Swift或是从Swift访问ObjC。

####    把ObjC导入到Swift

为了把同一个框架ObjC的部分代码暴露给Swift，我们需要把这些文件导入到框架的ObjC的囊括头文件(umbrella header).

***同一框架导入ObjC代码给Swift***

1.  在编译设置里的打包(Packaging)，确保为框架定义模块的设置是YES
2.  在我们的囊括头文件(umbrella header)，导入我们需要暴露的ObjC头文件

``` objc
    #import <XYZ/XYZCustomCell.h>
    #import <XYZ/XYZCustomView.h>
    #import <XYZ/XYZCustomViewController.h>
```

任何在这个头文件里的公开的ObjC都会在Swift可用。ObjC的功能在这个目标里的任何Swift文件都可见，不需要其他导入操作。我们在Swift里用Swift的语法来使用ObjC代码。

``` swift
let myCell = XYZCustomCell()
myCell.subtitle = "A custom cell"
```
####    把Swift导入ObjC

要把一个框架目标的Swift的部分文件导入到ObjC代码里，我们不需要在框架的囊括头文件(umbrella header)里导入任何东西。相反，我们需要在需要使用Swift的ObjC文件里导入Xcode产生的Swift头文件。

因为框架的产生的头文件是框架公开接口的一部分，所以只有标记为public的声明会出现在头文件里，但是在同一个框架里，我们在ObjC里使用那些Swift里面是internal的，并且是继承自ObjC的类的声明。


***同一框架导入Swift代码给ObjC***

1.  在编译设置里的打包(Packaging)，确保为框架定义模块的设置是YES
2.  使用下面的语法和对应的名字把头文件导入到需要使用Swift的ObjC的.m文件里。

``` objc
    #import <ProductName/ProductModuleName-Swift.h>
```

框架里的Swift文件就会在这个.m文件里可见。

|           |导入Swift                                |导入ObjC                                         |
|:----------|:---------------------------------------|:------------------------------------------------|
| Swift代码  | 不需要导入语句                           | #import <ProductName/ProductModuleName-Swift.h> |
| ObjC代码   | 不需要导入语句,需要ObjC囊括头文件          | #import "Header.h"                             |
     


### 导入外部框架

我们可以导入来自外部的框架，不论它是一个语言写的还是多个语言写的。这个过程和使用的语言没有关系。但是，我们需要确认，导入的框架在编译时，定义模块的选项是YES。

在Swift里：

``` swift
import FrameworkName
```

在ObjC里：

``` objc
@import FrameworkName;
```

### 在ObjC里使用Swift

一旦我们在ObjC里导入了Swift代码，我们就可以用ObjC的语法来使用这些Swift的类。

``` objc
MySwiftClass *swiftObject = [[MySwiftClass alloc] init];
[swiftObject swiftMethod];
```

Swift的类或协议必须标记为 @objc才能在ObjC中被使用。如果Swift的类是继承自ObjC的类，那么编译器会自动插入 @objc。

我们可以访问任何的标记为@objc的类和协议。除了下面这些：
1.  泛型
2.  元组
3.  Swift定义的枚举
4.  Swift定义的全局函数
5.  Swift定义的全局变量
6.  Swift定义的类型别名
7.  Swift风格的variadics
8.  嵌套的类型
9.  科里化(Curried)的函数

为了避免循环的引用，不要把Swift导入到ObjC的头文件。相反，我们可以前置声明一个Swift类用在头文件里。注意，我们不能在ObjC里继承Swift的类。

***在ObjC头文件里引用Swift类***

``` objc
@class MySwiftClass;
@interface MyObjcClass : NSObject
- (MySwiftClass *)returnSwiftObject;
/* ... */
@end
```

### 命名我们的产品模块

Xcode为Swift生成头文件和Xcode创建的ObjC桥接头文件的名字都依赖于我们的产品模块名。默认的，这个名字就是我们产品的名字。然而，如果我们的产品名字有不是字母数字字符的，例如 "."，会被取代为" _ "，如果由数字开头，第一个数字会被" _ "取代。

我们也可以给模块起自定义的名字，Xcode会使用这个。我们修改产品模块名的变异设置即可。

### 注意和技巧

1.  把ObjC和Swift文件想象为一套代码，注意命名冲突
2.  如果在制作框架，注意把打包(Packaging)下面的定义模块(Defines Module)设为YES
3.  如果在使用ObjC桥接头文件，这个头文件的路径必须在Swift编译器(Swift Compiler)-代码生成(Code Generation)-ObjC桥接头文件(Objective-C Bridging Header)的设置里。这个路径必须指向文件，而不是包含文件的目录。
4.  Xcode使用产品模块名，而不是目标名，来命名我们的ObjC桥接头文件和Swift生成的头文件
5.  要在ObjC中使用，Swift的类必须要么是ObjC类的子类，要么标记为@objc
6.  当我们把Swift代码导入ObjC时，注意不是所有的Swift特性都可以在ObjC中使用
7.  我们在ObjC里使用的Swift代码用到了我们自己其他的ObjC类型，我们需要把这些ObjC的头文件先于Swift的生成头文件导入到这个ObjC的.m文件里
8.  Swift的private的声明不会出现在生成的头文件里。private的声明除非标记为@IBAction, @IBOutlet, 或 @objc，否则不会暴露给ObjC
9.  如果目标是app，如果有ObjC桥接头文件，internal的声明就会出现在Swift生成的头文件里
10. 如果目标是框架，只有public的声明会出现在Swift生成的头文件里。但是我们仍然可以在ObjC里使用Swift标记为internal的方法和属性，只要这些声明的类继承自ObjC。

