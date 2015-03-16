---
layout: post
title: "Using Swift with Cocoa and Objective-C: Interoperability-Adopting Cocoa Design Patterns"
date: 2014-11-17 23:18:39 +0800
comments: true
categories: [swift]
---

使用Cocoa已经建立好的设计模式可以帮助我们写出良好设计的app。许多的这种模式都依赖ObjC里定义的类。因为Swift和ObjC可以互相调用，所以我们可以在Swift里使用ObjC的模式。在很多情况下，我们可以使用Swift的特性来简化或扩展现有的Cocoa模式，让他们更加强大，更加有用。

<!-- more -->

### 代理

在Swift和ObjC里，代理通常使用一个定义了一些要求的协议和一个实现了协议的代理属性来表达。和在ObjC里一样，在我们给一个可能不存在的代理发消息时，我们询问代理是否响应这个selector。在Swift里，我们可以用optional链条来调用一个可能是nil的对象上的一个可选的方法，然后用if-let语法来检查返回的结果。

``` swift
class MyDelegate: NSObject, NSWindowDelegate {
    func window(NSWindow, willUseFullScreenContentSize proposedSize: NSSize) -> NSSize {
        return proposedSize
    }
}
var myDelegate: NSWindowDelegate? = MyDelegate()
if let fullScreenSize = myDelegate?.window?(myWindow, willUseFullScreenContentSize: mySize) {
    println(NSStringFromSize(fullScreenSize))
}
```

我们先检查myDelegate是不是nil，检查myDelegate是否实现了window:willUseFullScreenContentSize:方法，如果都是true，调用方法，返回一个对象叫fullScreenSize。


### 错误报告

Swift里错误报告的模式和ObjC的一样，使用可选的返回值。在最简单的情况下，我们返回Bool值来表示函数调用成功与否。如果我们需要返回错误信息，我们添加一个out的NSError参数，类型是NSErrorPointer。这个类型基本和ObjC的 NSError** 类似，但是由额外的内存安全和optional typing。我们使用 & 来传入一个optional的NSError类型给NSErrorPointer。

``` swift
var writeError: NSError?
let written = myString.writeToFile(path, atomically: false,
    encoding: NSUTF8StringEncoding,
    error: &writeError)
if !written {
    if let error = writeError {
        println("write failure: \(error.localizedDescription)")
    }
}
```

当我们自己实现函数，需要提供错误报告时，我们把一个NSError对象设置到NSErrorPointer的memory属性。确保调用者给我们传入了一个不是nil的NSErrorPointer对象：

``` swift
func contentsForType(typeName: String!, error: NSErrorPointer) -> AnyObject! {
    if cannotProduceContentsForType(typeName) {
        if error {
            error.memory = NSError(domain: domain, code: code, userInfo: [:])
        }
        return nil
    }
    // ...
}
```

### KVO

KVO是一种可以让对象在其他对象某个属性变化时接到通知的一种方式。只要我们的Swift类继承自NSObject，我们就能在这个类上使用KVO。我们需要三步来实现KVO。

1.  在我们想观察的属性前面加上dynamic。

``` swift
class MyObjectToObserve: NSObject {
    dynamic var myDate = NSDate()
    func updateDate() {
        myDate = NSDate()
    }
}
```

2.  创建一个全局的private的上下文变量。

``` swift
private var myContext = 0
```

3.  添加一个观察者，重写observeValueForKeyPath:ofObject:change:context:，然后在deinit里移除观察者。

``` swift
class MyObserver: NSObject {
    var objectToObserve = MyObjectToObserve()
    override init() {
        super.init()
        objectToObserve.addObserver(self, forKeyPath: "myDate", options: .New, context: &myContext)
    }
    override func observeValueForKeyPath(keyPath: String, ofObject object: AnyObject, change: [NSObject: AnyObject], context: UnsafeMutablePointer<Void>) {
        if context == &myContext {
            println("Date changed: \(change[NSKeyValueChangeNewKey])")
        } else {
            super.observeValueForKeyPath(keyPath, ofObject: object, change: change, context: context)
        }
    }
    deinit {
        objectToObserve.removeObserver(self, forKeyPath: "myDate", context: &myContext)
    }
}
```

### 目标-动作

目标-动作是一种通用的模式，他可以让一个对象在某个特定事件发生时给另外一个对象发消息。它在ObjC和Swift里是很相似的。在Swift里，我们用Selector类型来代表ObjC的selectors。

### Introspection(自省)

在ObjC里，我们用isKindOfClass:来检查一个对象是否是某个类以及它子类的实例(注：这里，苹果文档是错的= =)，用conformsToProtocol:来检查对象是否实现了某个协议。在Swift里，我们用is 来检查类型，用 as? 来向下转换一个类型。

我们用is来检查一个实例是否是一个类的实例：(注：这里不明确是类本身还是子类也可以，所以我做了实现，是子类也可以。应该说成，用 is 来判断一个比较广泛的类的实例是否是这个广泛类的某一个子类的实例)。

``` swift
if object is UIButton {
    // object is of type UIButton
} else {
    // object is not of type UIButton
}
```

注：下面是我测试的代码。

``` swift
import UIKit
class ParentView : UIView
{
}
class ChildView : ParentView
{
}
let view : UIView = ChildView() //我们必须给出view的类型，如果自动推导，它就是ChildView类型，下面的代码就会报错。
if view is ParentView {
    println(true) // true
}
if view is ChildView {
    println(true) // true
}
```

我们也可以用 as? 来尝试把一个实例向下转换到一个子类的类型。as?返回一个optional的对象，然后用if-let语句来检查结果。

``` swift
if let button = object as? UIButton {
    // object is successfully cast to type UIButton and bound to button
} else {
    // object could not be cast to type UIButton
}
```

检查是否实现协议和检查是否是类的实例是一样的：

``` swift
if let dataSource = object as? UITableViewDataSource {
    // object conforms to UITableViewDataSource and is bound to dataSource
} else {
    // object not conform to UITableViewDataSource
}
```

注意，上面的dataSource是UITableViewDataSource的对象，如果我想调用它的其他方法(不是UITableViewDataSource定义的方法)，我们需要把它们在转换到其他类型来调用。


