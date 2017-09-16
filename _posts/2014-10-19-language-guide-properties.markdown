---
layout: post
title: "Language Guide: Properties"
date: 2014-10-19 00:35:02 +0800
comments: true
categories: [Swift]
---

属性把相关的值关联到类，结构体，或是枚举上面。存储的属性把值作为实例的一部分一起存储，然而计算的属性是计算一个值。计算的属性可以由类，结构体和枚举提供，但存储的属性只能有类和结构体提供。

存储和计算的属性通常和某个类型的实例相关。然而，属性可以直接和类型相关，这样的属性叫做类型属性。

另外，你可以定义属性观察者来检测属性值的变化，然后触发自定义的操作。属性观察者可以添加给你自己定义的存储属性，和你从父类继承的属性。

<!--more-->

#####	存储属性

存储属性，最简单的形式，就是和一个特定类或结构体的实例一起存储的常量或变量。

你可以给在定义属性时给它一个默认值。你可以在初始化函数里设置和改变它的初始值，即使是常量。

下面的例子定义了一个FixedLengthRange的结构体，表示了一个区间，一旦创建，长度不能改变。

``` swift
struct FixedLengthRange{
	var firstValue: Int
	let length: Int
}
var rangeOfThree = FixedLengthRange(firstVaule:0, length:3)
rangeOfThree.firstValue = 6
```

FixedLengthRange的实例，有一个可变的存储属性firstValue，和一个常量存储属性length。在上面的例子里firstValue可以改变，但是length却不能。

######	常量结构体实例的存储变量

如果你把一个结构体的实例附值给一个常量，那么你就不能改变这个结构体的存储属性，即使这个属性是个变量。

``` swift
let rangeOfFour = FixedLengthRange(firstValue:0, length:4)
rangeOfFour.firstValue // error!!!
```

因为rangeOfFour是个常量，所以不能修改firstValue，即使这个属性是变量。

这种行为是因为结构体是值类型，当一个值类型的实例标记为常量，它的属性也是常量。

这个对于类不是，因为类是引用类型，如果你把一个引用类型的实例附值给一个常量，你仍然可以修改这个实例的变量属性。

######	延迟的存储属性

一个延迟的存储属性是说它的初始值只有在第一次被使用的时候才会计算。你在需要标记为延迟的属性前面加lazy关键字。

	注意
	如果一个属性是延迟的，那它必须是变量，因为他的初始值可能在初始化函数完成后依然没有被用到。而常量属性必须在初始化函数结束前有值，所以不能是lazy的。

如果一个属性的值取决于一些外界因素，而这些外界因素在初始化函数结束后依然不确定，这时候把属性标记为lazy就很有用了。另外，如果一个属性的计算非常复杂，所以就在不需要的时候不去计算，这样标记为lazy也很好。

``` swift
class DataImporter{
	var filename = "data.txt"
}
class DataManager{
	lazy var importer = DataImporter()
	var data = [String]()
}
let manager = DataManager()
manager.data.append("some data")
manager.data.append(“some more data")
```

这个例子里，直到最后，manager的importer属性都没有值。

``` swift
println(manager.importer.filename)
```

这里，会初始化importer，并打印它的filename属性。

######	存储属性和成员变量

如果你熟悉ObjC，你应该直到有两种办法为一个类实例提供存储值或引用。除了属性，你还可以用成员变量作为属性的底层存储来存储值。

Swift把这些概念统一为属性。一个Swift的属性没有对应的成员变量，而且属性的底层存储是无法访问的。这种方式避免了在不同的上下文如何访问值的困惑，也简化了属性的声明。关于属性的所有信息，包含名字，类型，内存管理，都在同一个地方。

#####	计算属性

除了存储属性，类，结构体和枚举还可以定义计算属性，计算属性不会真实的存储值。相反，他们提供一个getter和一个可选的setter来间接的获取属性的值和修改其他属性的值。

``` swift
struct Point{
	var x = 0.0, y = 0.0
}
struct Size {
	var width = 0.0, height = 0.0
}
struct Rect {
	var origin = Point()
	var size = Size()
	var center: Point {
		get {
			 let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
		}
		set(newCenter){
			origin.x = newCenter.x - (size.width / 2)
          origin.y = newCenter.y - (size.height / 2)
		}
	}
}
var square = Rect(origin: Point(x: 0.0, y: 0.0),
    size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
square.center = Point(x: 15.0, y: 15.0)
println("square.origin is now at (\(square.origin.x), \(square.origin.y))")
```

上面的例子定义了三个结构体来表示几何的一些概念。

Point，Size，Rect。

Rect还定义了一个计算属性center。一个矩形的中心，总是可以从原点和大小计算出来，所以也就不用为它再声明一个存储属性。

上面的例子创建了一个Rect的实例square。square的属性center被访问时，结果是当时计算的；square的center被设置时，实际通过计算是修改了square的原点的值。

######	setter方法简写

如果一个计算属性没有给出新值的名字，那么会有一个默认的名字newValue可以使用。

``` swift
struct AlternativeRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

######	只读的计算属性

如果计算属性，只有getter没有setter就叫做只读的计算属性。一个只读的计算属性总是返回值，但是不能被设置。

	注意
	计算属性，必须是变量属性。let只能用于常量属性，这种属性一旦初始化结束后就无法改变。

把get关键字和大括号去掉就可以声明一个只读的属性。

``` swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
println("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
```

#####	属性观察者

属性观察者观察和相应属性值的改变。属性观察者在每次值被设置的时候都会调用，即使新值和旧值是一样的。

除了lazy的存储属性，可以给任何任何存储属性添加观察者。你也可以通过重写继承的属性，无论存储还是计算，添加观察者。
	
	注意
	你不需要给非重写的计算属性添加观察者，因为你可以在setter方法里直接操作。

你可以给属性添加下面两个观察者：

*	willSet，会在值被存储之前。
* 	didSet，会在值被存储之后。

如果你实现了willSet，新值会做为一个常量参数传入，你可以给它一个名字或是使用默认的名字newValue。

类似的，如果你实现了didSet，你会收到一个常量参数，是旧值。你可以给它一个名字，或是使用默认的oldValue。

	注意
	willSet和didSet在初始化时并不会调用，只在其他情况下调用。

下面的例子：

``` swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            println("About to set totalSteps to \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                println("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// About to set totalSteps to 200
// Added 200 steps
stepCounter.totalSteps = 360
// About to set totalSteps to 360
// Added 160 steps
stepCounter.totalSteps = 896
// About to set totalSteps to 896
// Added 536 steps
```

类StepCounter声明了一个totalSteps属性，还有willSet和didSet观察者。

例子里，willSet接收一个自定义的参数名newTotalSteps，而didSet用了默认的参数名oldValuew。

	注意
	如果你在didSet里给属性自己附值，那么这个新的值将取代刚刚赋给的值。

#####	全局和局部变量

上面描述的关于计算和观察属性的能力对于全局和局部变量也使用。全局变量就是定义在任何函数，方法，闭包和类型上下文外部的变量。局部变量是定义在函数，方法和闭包上下文的变量。

在此前的文章中，所有的全局和局部变量都是存储变量。存储变量和存储属性一样，提供一个特定类型的值的存储，并且可以获取和改变它。

然而，你也可以定义计算变量和存储变量的观察者，无论在全局还是局部。计算变量不存储值，而是计算值。

	注意
	全局变量和常量总是延迟计算的。和延迟存储属性一样。和延迟存储属性不一样，全局常量和变量不需要标示为lazy。局部变量和常量永远不会延迟计算。

#####	类型属性

实例属性是属于某一类型的实例的属性。每当一个新的实例被创建，它有自己的一套属性，和其他属性是分开的。

你可以给类型本身定义属性。这样的属性永远都只会有一份，无论创建了多少个实例。这种属性叫做类型属性。

类型属性用来定义一些对于所有实例都一样的值。例如一个所有实例都可以使用的常量（类似于C里的static常量）或一个对于所有实例相当于全局的一个变量（类似于C里的static变量）。

对于值类型，（结构体和枚举），你可以定义存储和计算的类型属性。对于类，你只能定义计算的类型属性。

值类型的存储属性可以是变量或常量。计算属性则永远是变量。

	注意
	和存储的实例属性不一样，你必须给类型存储属性一个默认值。这是因为类型本身并没有初始化的时刻。

######	类型属性语法

在C和ObjC里，用static来定义关联到一个类型的常量或变量作为全局静态变量。在Swift，类型属性作为类型定义的一部分，写在类型定义的大括号里，而且每个类型属性都限制在它的类型里。

使用static为值类型定义类型属性，用class为类定义类型属性。

``` swift
struct SomeStructure {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        // return an Int value here
    }
}
enum SomeEnumeration {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        // return an Int value here
    }
}
class SomeClass {
    class var computedTypeProperty: Int {
        // return an Int value here
    }
}
```

	注意
	上面例子的计算属性都是只读的，但是你可以定义可读可写的。

######	访问或设置类型属性

类型属性也用点语法来访问，但是，是通过类型，而不是类型的实例。

``` swift
println(SomeClass.computedTypeProperty)
// prints "42"
println(SomeStructure.storedTypeProperty)
// prints "Some value."
SomeStructure.storedTypeProperty = "Another value."
println(SomeStructure.storedTypeProperty)
// prints "Another value."
```

下面的例子：

``` swift
struct AudioChannel {
    static let thresholdLevel = 10
    static var maxInputLevelForAllChannels = 0
    var currentLevel: Int = 0 {
        didSet {
            if currentLevel > AudioChannel.thresholdLevel {
                // cap the new audio level to the threshold level
                currentLevel = AudioChannel.thresholdLevel
            }
            if currentLevel > AudioChannel.maxInputLevelForAllChannels {
                // store this as the new overall maximum input level
                AudioChannel.maxInputLevelForAllChannels = currentLevel
            }
        }
    }
}
```

上面的例子里，currentLevel是实例属性，而thresholdLevel是常量类型属性，maxInputLevelForAllChannels是变量类型属性。

在currentLevel的didSet观察者里，使用类型属性做一些检查和判断。

	注意
	上面第一个检查里，重新设置了currentLevel的值，这样并不会触发新的观察者调用。

``` swift
var leftChannel = AudioChannel()
var rightChannel = AudioChannel()
leftChannel.currentLevel = 7
println(leftChannel.currentLevel)
// prints "7"
println(AudioChannel.maxInputLevelForAllChannels)
// prints "7"
rightChannel.currentLevel = 11
println(rightChannel.currentLevel)
// prints "10"
println(AudioChannel.maxInputLevelForAllChannels)
// prints "10"
```























