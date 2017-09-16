---
layout: post
title: "Using Swift with Cocoa and Objective-C: Interoperability-Writing Swift Classes with ObjC Behavior"
date: 2014-11-17 23:16:04 +0800
comments: true
categories: [swift]
---


互用性(Interoperability)让我们可以定义包含ObjC行为的Swift类。我们在定义Swift类时，可以继承ObjC的类，可以实现ObjC的协议，可以使用ObjC其他的功能。这意味着我们可以以类为基础来创建ObjC行为相似的类，并且使用Swift现代的，强大的特性来加强我们的类。

<!-- more -->

### 从ObjC的类继承

在Swift里，我们可以定义ObjC类的子类。

``` swift
import UIKit
class MySwiftViewController: UIViewController {
    // define the class
}
```

我们将获得父类所有的功能。但是当我们需要重写父类的方法时，记得添加override。

### 实现协议

在Swift里，我们可以实现定义在ObjC的协议。和Swift协议一样，任何的ObjC的协议都写在父类的列表里，用逗号隔开。

``` swift
class MySwiftViewController: UIViewController, UITableViewDelegate, UITableViewDataSource {
    // define the class
}
```

ObjC的协议在导入Swift时，会变成Swift的协议。如果我们想在Swift代码里引用协议UITableViewDelegate，我们直接写成UITableViewDelegate，而不是像ObjC写成id<UITableViewDelegate> 。

因为Swift里的类和协议的命名空间是统一的，所以ObjC的NSObject协议对应到Swift变成了NSObjectProtocol。

### 写初始化函数和反初始化函数

Swift编译器确保我们的初始化函数不会留下未初始化的属性，这样增加了代码的安全性和可预判性(predictability)。另外，和ObjC不一样，Swift没有额外的内存分配调用。即使我们在使用ObjC类型，我们也使用Swift的语法来创建对象，Swift会把ObjC的初始化函数转化为Swift的初始化函数。

当我们需要在类实例被销毁时想执行更多的清理工作，我们实现deinit而不是dealloc方法。Swift反初始化会在对象被销毁前自动调用。Swift在结束我们本身的反初始化函数后也会自动调用父类反初始化函数。当我们使用ObjC的类，或是我们继承自ObjC的类，Swift也会自动调用对应的dealloc方法。

### 整合 InterfaceBuilder

Swift编译器包含了一些标记可以在Swift类里打开IB的特性。我们可以像在ObjC中一样，在Swift里使用outlet，action和实施渲染(live rendering)。

#### 使用 Outlet 和 Action

Outlet和Action让我们在IB里可以把界面元素和我们的代码连结起来。我们需要在属性和方法前面加上 @IBOutlet 和 @IBAction来使用。我们在数组前面标记 @IBOutlet来声明一个outlet集合(outlet collection)。

当我们在Swift声明outlet时，我们应该把属性的类型声明为自动解包的optional。这样，我们就可以让故事板(storyboard)来在运行时连接这些outlet。当我们的类从故事版或xib初始化时，我们可以认为属性已经连接好的。

``` swift
class MyViewController: UIViewController {
    @IBOutlet weak var button: UIButton!
    @IBOutlet var textFields: [UITextField]!
    @IBAction func buttonTapped(AnyObject) {
        println("button tapped!")
    }
}
```

####    实时渲染

我们可以使用 @IBDesignable 和 @IBInspectable 来在IB里打开实时，交互的视图设计。当我们从UIView或者NSView继承时，我们可以把子类标记为@ IBDesignable。这样，当我们在IB中加入自定义的类时，IB会把我们类渲染出来。

我们也可以在属性前面添加@IBInspectable来让我们在IB设计界面时，可以实时调整这些属性的值。

``` swift
@IBDesignable
class MyCustomView: UIView {
    @IBInspectable var textColor: UIColor
    @IBInspectable var iconHeight: CGFloat
    /* ... */
}
```

### 明确属性(Property)特性(Attribute)

在ObjC里，属性可以有一系列的特性来给出关于属性的额外信息。在Swift，这些信息需要用其他方式给出。

####    强引用和弱引用

Swift默认提供强引用，用weak关键字来说明弱引用。这个关键字只能用于那些类型是optional的类的属性。

####    读写和只读

在Swift里，我们没有readwrite和readonly特性，我们用let来表示只读，用var来表示可读可写。当声明计算属性时，我们如果只给出getter，那就是只读，如果给出了getter和setter，那就是可读可写。

####    拷贝

在Swift里，ObjC的copy属性特性被翻译为了@NSCopying。这个属性的类型必须实现NSCopying协议。

### 实现 Core Data 的管理对象(Managed Object)的子类

Core Data为 NSManagedObject 的子类的属性提供了底层存储和实现。我们需要在我们的管理对象(Managed Object)的子类的每一个属性或是每一个关系前加 @NSManaged标记。它和Objc的@dynamic一样，告诉Swift编译器这些属性的存储和实现会在运行时给出。然而，和@dynamic不一样，@NSManaged只给Core Data提供支持。

Swift类是有命名空间的，就是他们的模块，一般就是所在的项目。我们在使用NSManagedObject的Swift子类和Core Data模型时，需要在类名检查器前面加上模块的名字。

### 和ObjC的API一起使用Swift的类名

Swift的类有基于他们编译进的模块的命名空间，即使是在ObjC里使用的时候。和ObjC不同，ObjC的所有的名字都在一个全局的命名空间里，所以不能有重名，Swift的类可以在不同的模块里有相同的名字，而不会造成混淆。例如，在Swift里MyFramework的MyClass的全称是MyFramework. MyClass。一个Swift的app的目标就是模块，所以一个app名叫MyGreatApp里的一个名叫Observer的类的全名叫MyGreatApp.Observer。

为了在ObjC中使用Swift类时保留命名空间，Swift类在暴露给ObjC时暴露的全名。因此，当我们使用字符串表示Swift类名的时候，我们必须给出全名。例如，我们在做一个基于文档的app，我们需要在Info.plist里给出文档类的名字。在Swift里，我们必须使用全名，包括app的模块名字。

下面的例子，NSClassFromString通过一个字符串来获取一个类的引用。如果要得到一个Swift类，必须给出全名。

``` swift
let myPersonClass: AnyClass = NSClassFromString("MyGreatApp.Person")
```

