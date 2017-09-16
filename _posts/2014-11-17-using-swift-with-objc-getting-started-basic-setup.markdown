---
layout: post
title: "Using Swift with Cocoa and Objective-C: Getting Started-Basic Setup"
date: 2014-11-17 23:11:42 +0800
comments: true
categories: [Swift]
---


Swift从设计开始就保证了和Cocoa以及ObjC的无缝兼容。我们可以在Swift里使用ObjC的API，不论是系统提供的框架还是我们自己写得代码；我们也可以在ObjC中使用Swift。这样我们可以很轻松的把Swift加入到我们现在的App开发流程中。

整个这篇文档从三个重要的方面来讲述兼容性。
    *   互用性（Interoperability），让我们可以在Swift和ObjC之间交互，让我们在ObjC中使用Swift类，还可以在Swift使用Cococa的框架和模式。
    *   混合与匹配（Mix and Match），告诉我们如何同时在一个app内使用Swift和ObjC。
    *   迁移（Migration），在互用性和混合与匹配的基础上，从现有的ObjC代码迁移到Swift就会比较简单。

在我们开始之前，我们需要先做一些准备工作，来配置我们的开发环境。

<!-- more -->

### 配置Swift环境

我们需要从Xcode项目模板中选择一个，然后创建一个Swift的项目工程。

Swift的项目工程结构和ObjC的基本一样，但是有一个重要的区别：Swift文件没有头文件。在接口和实现没有明显的区别，都放在一个文件里。

### 理解Swift的Import过程

在配置好Xcode工程之后，我们可以导入任何来自Cocoa的框架。

所有的ObjC框架和C类库在Swift里都以模块（module）的形式被导入。

``` swift
import Foundation
```

这个命令导入了Foundation所有的API，包括NSDate，NSURL等等他们的方法，属性，类别，这些都直接可以在Swift内使用。

导入的过程非常直接，ObjC框架把它们的API放到头文件里。Swift里，这些头文件被编译到ObjC模块，然后被导入到Swift里。这个构成决定了ObjC里的函数，类，方法如何出现在Swift里。对于函数和方法，参数类型和返回值类型可能受到影响。对于类型，可能发生下面的改变：
    *   把ObjC特定的类型转到Swift里对应的类型。例如，id到AnyObject
    *   把ObjC的核心类型转到Swift里对应的类型。例如，NSString到String。
    *   把ObjC里的概念转到Swift里对应的概念。例如，指针到引用。

把Swift导入ObjC的模型和把ObjC导入到Swift的模型非常相似。Swift把它的API表达成Swift模块（module）。这些模块会生成ObjC的头文件。这些头文件就可以在ObjC中使用。有一些Swift的API不能映射到ObjC，因为有些概念在ObjC中不存在。

    注意
    我们不能在Swift中直接使用C++，必须使用ObjC来包裹C++代码。

