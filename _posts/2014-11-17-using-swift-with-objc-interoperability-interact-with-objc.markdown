---
layout: post
title: "Using Swift with Cocoa and Objective-C: Interoperability-Interacting with ObjC APIs"
date: 2014-11-17 23:13:44 +0800
comments: true
categories: [Swift]
---


互用性（Interoperability）是说Swift和ObjC之间互相提供接口的能力，让我们可以从一个语言访问另外一个语言的部分代码。当我们开始用Swift来开发时，我们需要理解如何使用互用性（Interoperability）来重新定义，改进我们制作app的方法。

互用性（Interoperability）一个重要的方面就是在我们写Swift代码时可以使用ObjC的API。在我们导入ObjC的框架后，我们可以使用Swift的语法来创建这些类型的实例并和他们交互。

<!-- more -->

### 初始化

我们在Swift里创建ObjC类的实例时，我们用Swift语法调用类的一个初始化函数。当ObjC的init方法转换到Swift时，他们使用了Swift初始化函数的语法。init被删掉了作为了关键字。对于init方法由initWith开头的，With也被删掉了。剩下的部分的第一个字母变成小写成为了参数名字，其他的参数都一一对应过来。selector的每一部分都变成了参数。

``` objc
//ObjC
UITableView *myTableView = [[UITableView alloc] initWithFrame:CGRectZero style:UITableViewStyleGrouped];
```

``` swift
//Swift
let myTableView: UITableView = UITableView(frame: CGRectZero, style: .Grouped)
```

我们不需要调用alloc；Swift会调用。注意在Swift，init不会出现。

我们可以给出类型，也可以让Swift自动推导类型。

为了简单和保持一致，ObjC的工厂方法映射到了Swift得便利初始化函数。这样映射可以保证他们在使用时有一致，清晰的语法。

``` objc
//ObjC
UIColor *color = [UIColor colorWithRed:0.5 green:0.0 blue:0.5 alpha:1.0];
```

``` swift
//Swift
let color = UIColor(red: 0.5, green: 0.0, blue: 0.5, alpha: 1.0)
```

####    可失败的初始化

在ObjC里，初始化函数直接返回他们初始化后的对象。为了表示初始化失败，初始化函数直接返回nil。在Swift里，这种模式被增加到了语言级别，叫做可失败的初始化（failable initialization）。许多iOS和MacOS的ObjC初始化函数都被修改来表示他们可能失败。这些函数如果不能失败，会被导入为init(...)，如果可能失败，会被导入为init?(...)。我们自己的类，和系统类库里还没有修改的初始化会导入为init!(...)。

例如，NSString(contentsOfFile:) 可能失败，如果文件不存在。我们使用optional绑定来查看初始化是否成功。

``` swift
if let fileContents = NSString(contentsOfFile: "MyFile.txt") {
    println(fileContents)
}
```

### 访问属性

访问和设置ObjC对象的属性使用 . 语法。

``` swift
myTextField.textColor = UIColor.darkGrayColor()
myTextField.text = "Hello world"
```

在访问或设置属性时，直接用属性的名字，不需要加括号。上面的darkGrayColor是一个类方法，而不是属性。

ObjC里，如果一个方法返回一个值，没有任何参数可以被当做一个隐式的getter，也可以使用 . 来直接访问。但是在Swift里不是，Swift里只有在ObjC中使用@property定义的属性被导入后能作为属性使用。

### 调用方法

当我们在Swift里调用ObjC方法时，使用 . 语法。

当ObjC的方法被导入到Swift时，selector的第一部分成为了方法的名字，第一个参数没有名字，selector的其他部分都作为参数。selector的所有部分在调用时都是需要的。

``` objc
//ObjC
[myTableView insertSubview:mySubview atIndex:2];
```

``` swift
//Swift
myTableView.insertSubview(mySubview, atIndex: 2)
```

如果调用的方法没有参数，我们也要写上()。

``` swift
myTableView.layoutIfNeeded()
```

### id 兼容性

Swift使用一个协议，AnyObject来表示任何类型的对象，就好像ObjC里的id。AnyObject协议确保你写出类型安全的代码，又可以保留一个无类型对象的灵活性。因为这些额外的安全性，Swift会把id转化为AnyObject。

例如，我们可以把任何对象附值给AnyObject类型的变量或常量。

``` swift
var myObject: AnyObject = UITableViewCell()
myObject = NSDate()
```

我们可以不用转换，直接调用任何的ObjC方法和属性。这包括标记为@objc的兼容方法。

``` swift
let futureDate = myObject.dateByAddingTimeInterval(10)
let timeSinceNow = myObject.timeIntervalSinceNow
```

然而，因为AnyObject的具体类型在运行时是不知道的，所以很容易写出不安全的代码。和ObjC中一样，如果我们调用或访问一个AnyObject事实上不存在的方法或属性，这都会造成运行时错误。

``` swift
myObject.characterAtIndex(5)
// crash, myObject doesn't respond to that method
```

我们可以使用optional来去除这种在ObjC中常见的错误。当我们调用AnyObject类型对象的ObjC方法时，这个方法调用实际上像一个自动解包的optional。我们也可以使用optional链条来调用AnyObject的方法或属性。

``` swift
let myCount = myObject.count?
let myChar = myObject.characterAtIndex?(5)
if let fifthCharacter = myObject.characterAtIndex?(5) {
    println("Found \(fifthCharacter) at index 5")
}
```

和Swift里所有的向下转换一样，把AnyObject向下转换不一定成功。因此会返回一个optional。

``` swift
let userDefaults = NSUserDefaults.standardUserDefaults()
let lastRefreshDate: AnyObject? = userDefaults.objectForKey("LastRefreshDate")
if let date = lastRefreshDate as? NSDate {
    println("\(date.timeIntervalSinceReferenceDate)")
}
```

当然，如果我们确定类型，可以使使用as。

``` swift
let myDate = lastRefreshDate as NSDate
let timeInterval = myDate.timeIntervalSinceReferenceDate
```

### 使用 nil

在ObjC离，我们使用引用到对象是原始指针，它的值可能是NULL，在ObjC里也叫nil。在Swift里，所有值，包括结构体和类引用，都保证有值。相反，我们把可能没有值的对象包裹在一个optional里，当它的值不存在的时候，用nil表示。

因为ObjC并不会保证一个对象不是nil，Swift会把ObjC导入的API里的类类型包裹在一个自动解包的optional里。在使用一个ObjC对象前，我们应该检查对象是否存在。

``` objc
//ObjC
- (NSDate *)dueDateForProject:(Project *)project;
```

导入到Swift变成了：

``` swift
//Swift
func dueDateForProject(project: Project!) -> NSDate!
```

在某些情况下，你可以确保一个ObjC的方法或属性绝对不会返回nil。为了在这种情况下使用方便，Swift把它们导入为自动解包的optional。

### 扩展

Swift的扩展有点类似ObjC的类别。扩展可以扩展现有的类，结构体，枚举，包括那些定义在ObjC里的。我们可以扩展系统的或是我们自己定义的类型。直接导入模块，然后引用对应的类，结构体或枚举就可以了。

``` swift
extension UIBezierPath {
    convenience init(triangleSideLength: Float, origin: CGPoint) {
        self.init()
        let squareRoot = Float(sqrt(3.0))
        let altitude = (squareRoot * triangleSideLength) / 2
        moveToPoint(origin)
        addLineToPoint(CGPoint(x: triangleSideLength, y: origin.x))
        addLineToPoint(CGPoint(x: triangleSideLength / 2, y: altitude))
        closePath()
    }
}
```

我们可以通过扩展添加属性（包括类和静态属性），但是，这些属性都必须是计算的，不能添加存储的属性。

``` swift
extension CGRect {
    var area: CGFloat {
        return width * height
    }
}
let rect = CGRect(x: 0.0, y: 0.0, width: 10.0, height: 50.0)
let area = rect.area
// area: CGFloat = 500.0
```

我们可以使用扩展一个类来实现协议，而不用子类。如果协议定义在Swift，我们还可以让枚举和结构体实现协议，不管它们是定义在Swift还是ObjC。

我们不能用扩展来重写ObjC类型已有的方法和属性。

### 闭包

ObjC的blocks自动导入为Swift的闭包。

``` objc
//ObjC
void (^completionBlock)(NSData *, NSError *) = ^(NSData *data, NSError *error) {/* ... */}
```

``` swift
//Swift
let completionBlock: (NSData, NSError) -> Void = {data, error in /* ... */}
```

Swift的闭包和ObjC的block是互相兼容的，所以我们可以ObjC的方法传递Swift的闭包。Swift闭包和函数有着相同的类型，所以我们也可以传递Swift函数。

闭包和blocks有着相似的捕获行为，但有一点不同： *变量是可变的，而不是拷贝的*。换句话说，ObjC的 __block 行为在Swift是默认的。

### 对象比较

在Swift里比较对象有两种方式，第一个，相等性(equality)==，比较的是两个对象的内容。第二个，相同性(identity)===，比较两个引用是否指向一个对象实例。

Swift和ObjC对象通常在Swift里使用 == 和 === 来比较。Swift为NSObject的子类自动提供了一个实现。在这个实现里，Swift会调用NSObject的isEqual:方法，NSObject仅仅是相同性检查，所以我们需要自己实现isEqual:方法。因为我们可能把Swift的对象传递给ObjC，但是这些事例可能不是继承NSObject，所以需要在ObjC里做比较，就需要在这些类型里实现isEqual:方法。

作为实现相等性的一部分，我们需要确保实现hash属性。另外，如果我们打算把类型作为字典的key来使用，还需要实现Hashable协议，并给出hashValue属性。

### Swift类型兼容性

当我们继承从一个ObjC的类继承一个类时，这个类的属性，方法，角标和初始化函数都自动在ObjC可以使用。在某些时候，我们需要更细粒度的控制我们Swift的API如何暴露给ObjC。

我们可以使用 @objc 来标记一个不是继承自ObjC类型的类，来使它在ObjC中可用。或是我们想改变一个符号暴露给ObjC之后的名字。当我们正在使用类似于KVO的API，我们也可使用dynamic修饰符来要求成员的访问由ObjC运行时动态分发，这样可以动态替换一个方法的实现。

####    暴露Swift的接口给ObjC

当我们从NSObject或是任何其他ObjC的类继承来定义一个Swift类的时候，这个类自动会和ObjC的类兼容。这部分所说的所有内容，Swift编译器都会替我们做好。如果我们永远都不会在ObjC中导入一个Swift的类，这部分的内容我们也不用考虑。但是，如果我们的Swift类不是继承自NSObject或其他ObjC的类，我们还希望在ObjC中使用它，那么我就需要使用 @objc。

@objc 标记，可以使我们的Swift API在ObjC和ObjC运行时可用。换句话说，我们可以把 @objc 加在任何的方法，属性，角标，初始化函数，类的前面，让他们可以在ObjC中可用。如果我们的类继承自ObjC的类，编译器会帮我们添加 @objc标记，编译器也会把类的成员都加上@objc标记。当我们添加@IBOutlet，@IBAction或者@NSManaged标记，@objc也会自动加上。当我们使用ObjC的selector来实现目标-动作(target-action)模式的时候，@objc也很有用。

    注意
    编译器并不会给private的成员自动添加@objc。

当我们在ObjC中使用Swift时，编译器会做一次翻译。例如，Swift的函数func playSong(name: String)会被导入为- (void)playSong:(NSString *)name。然而，这里有个区别，当我们使用Swift的初始化函数时，编译器会在函数前加 initWith，例如，init (songName: String, artist: String) 会被导入为(instancetype)initWithSongName:(NSString *)songName artist:(NSString *)artist。

Swift还允许给出一系列的名字，来给出@objc标记的内容在ObjC中得名字。

``` swift
@objc(Squirrel)
class Белка {
    @objc(initWithName:)
    init (имя: String) { /*...*/ }
    @objc(hideNuts:inTree:)
    func прячьОрехи(Int, вДереве: Дерево) { /*...*/ }
}
```

当我们在一个Swift类上用@objc(<#name#>)时，这个类会在ObjC中出现，并且没有任何命名空间。另外，这个属性，在我们从ObjC类迁移到Swift时候也很有用，因为归档的对象会在归档里存储类名，我们也需要使用@objc(<#name#>)来给出名字，这样旧的类名可以被Swift解析出来。

####    要求动态分发

尽管@objc标记可以把Swift的API暴露给ObjC，但是它不能保证属性，方法，角标和初始化函数时动态分发的。Swift编译器还是有可能为了优化性能去虚拟化(devirtualize)或是而把这些访问变成inline的，而不通过ObjC运行时。当我们把一个成员标记为dynamic，这个成员的访问就永远是动态分发的。因此，标记为dynamic自然也是@objc的。

我们很少需要动态分发。但是，如果我们需要，就必须标记为dynamic。因为我们可能使用ObjC运行时的函数method_exchangeImplementations来在app运行时替换实现，但是如果Swift编译器把函数inline或是去虚拟化，那么替换的实现就不会被调用。

### ObjC Selector

ObjC的Selector是一种可以用来引用ObjC方法的类型。在Swift里，ObjC的selector类型使用Selector结构体表达。我们可以使用一个字符串字面量来创建一个Selector，例如 let mySelector: Selector = "tappedButton:".因为字符串字面量可以自动的转换到selector。

``` swift
import UIKit
class MyViewController: UIViewController {
    let myButton = UIButton(frame: CGRect(x: 0, y: 0, width: 100, height: 50))
    init(nibName nibNameOrNil: String?, bundle nibBundleOrNil: NSBundle?) {
        super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil)
        myButton.addTarget(self, action: "tappedButton:", forControlEvents: .TouchUpInside)
    }   
    func tappedButton(sender: UIButton!) {
        println("tapped button")
    }
}
```

    
    注意
    performSelector:和类似的方法在Swift里都没有实现，因为这些方法其实是不安全的。

如果我们的Swift类继承自ObjC的类，这个类的所有方法和属性都在ObjC作为selector使用。否则，我们需要添加@objc。
