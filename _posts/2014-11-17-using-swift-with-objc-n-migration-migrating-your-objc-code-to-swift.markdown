---
layout: post
title: "Using Swift with Cocoa and Objective-C: Migration-Migrating Your ObjC Code to Swift"
date: 2014-11-17 23:24:58 +0800
comments: true
categories: [Swift]
---

迁移，让我们有机会去重新审视现有的ObjC的app，我们可以使用Swift来替换其中的一部分来改进它的架构，逻辑和性能。直接来说，我们使用前面学得知识(混合与匹配，互用性)来一点点迁移我们的app。混合与匹配，让我们选择合适的部分来使用Swift重写，把剩下的留在ObjC。互用性让我们可以把这些新的特性在融会到ObjC里。使用这些工具来探索Swift丰富的功能，然后把这些在整合回ObjC得app，我们不需要一次性从头使用Swift写一个新的app。

<!--more-->

### 为了迁移修改ObjC的代码

在我们迁移代码之前，确保我们的Swift和ObjC都有着最好的兼容性。这意味着需要整理和现代化我们现有的ObjC代码。我们现有的遵循现代化的ObjC代码会让迁移过程变得简单。如何现代化ObjC的代码，可以去看[这个文档](https://developer.apple.com/library/ios/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html#//apple_ref/doc/uid/TP40014150)。

### 迁移过程

最高效的迁移过程是按照文件来做，一次迁移一个类。因为我们不能在ObjC里继承Swift的类，所以我们最好选择那些没有子类的类。我们用.swift文件来代替.h和.m文件。所有的接口和实现都在一个文件里。我们不需要创建头文件，Xcode会在我们需要引用时自动为我们创建一个头文件。

####    开始之前

1.  创建一个对应的.swift文件，我们可以使用和.m一样名字的也可以选择不一样的。类名字的前缀也是可选的。
2.  导入相关的系统框架。
3.  在ObjC桥接头文件里添加我们需要在Swift使用的ObjC文件。
4.  为了让我们的Swift类在ObjC里可见和可用，我们要么让它继承自一个ObjC的类，要么标记为@objc。为了给出在ObjC里使用的一个名字，可以使用@objc(<#name#>)，其中<#name#>是我们在ObjC里要用来引用这类的名字。

####    迁移中

1.  我们可以通过继承ObjC的类，实现ObjC的协议等等让我们Swift类可以整合ObjC的行为。
2.  当我们使用ObjC的API时，我们需要知道Swift是如何把ObjC的API导入进来的。
3.  当我们使用Cocoa框架时，记得有些类型Swift是桥接的，这意味着我们可以直接使用Swift类型。
4.  当我们使用Cocoa框架时，我们需要使用Cocoa转换过来的常用设计模式。
5.  当我们打算把ObjC的属性转到Swift时，读一下属性那篇文档。
6.  使用@objc(<#name#>)来给出在ObjC里使用时的名字。

``` swift
var enabled: Bool {
@objc(isEnabled) get {
    /* ... */
}
}
```

7.  把实例方法(-)和类方法(+)，转到对应的func和class func。
8.  把简单的宏定义为常量，把复杂的宏定义为函数。

####    迁移结束后

1.  在ObjC里，把头文件改成 #import "ProductModuleName-Swift.h"。
2.  从目标(target)里删掉原来的.m和.h文件，不需要删除文件。
3.  我代码里的旧的ObjC的类的名字用Swift的名字替换掉。

### 注意和技巧

尽管我们需要迁移的代码不尽相同，但是下面是一些通用的步骤和技巧。

1.  记得我们无法在ObjC里继承一个Swift类。因此，迁移的ObjC的类不能有子类。
2.  迁移完毕一个类之后，从目标里把ObjC的类移除，避免编译错误(重复的符号)。
3.  为了让Swift在ObjC中可用，Swift的类必须继承自ObjC的类，或是用@objc标记。
4.  在ObjC使用Swift代码，记得有些Swift特性是无法转换到ObjC的。
5.  cmd-click来查看一个Swift的类的生成的头文件。
6.  option-click一个符号，来查看它的信息：类型，特性，文档注释。











