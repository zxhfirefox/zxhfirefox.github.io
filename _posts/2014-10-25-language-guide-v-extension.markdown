---
layout: post
title: "Language Guide: Extension"
date: 2014-10-25 10:25:34 +0800
comments: true
categories: [Swift]
---


扩展可以给已有的类，结构体，枚举添加新的功能。这包括可以给我们无法获取源代码的类型添加功能。扩展和ObjC的类别很相似。（和ObjC的类别不同，Swift的扩展没有名字）

<!--more-->

Swift里扩展可以：

*   添加计算属性和静态计算属性
*   添加实例方法和类型方法
*   提供新的初始化函数
*   定义角标
*   定义和使用新的嵌套类型
*   实现协议

        注意
        扩展只能添加功能，而不能重写已有的功能


### 语法

使用 extension 关键字声明扩展：

``` swift
extension SomeType{
}
```

扩展可以让一个现有类型实现一个或多个协议。这种情况下，协议的名字和对于类或者结构体是一样的。

``` swift
extension SomeType: SomeProtocol, AnotherProtocol{
}
```

    注意
    我们使用扩展给现有类型添加功能，这些功能将对所有实例产生效果，即使是在扩展定义之前就创建的实例

### 计算属性

扩展可以给现有类型添加计算的实例属性或类属性。

``` swift
extension Double{
    var km: Double { return self * 1_000.0 }
    var m: Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
println("One inch is \(oneInch) meters")
// prints "One inch is 0.0254 meters"
let threeFeet = 3.ft
println("Three feet is \(threeFeet) meters")
// prints "Three feet is 0.914399970739201 meters"
```

这些计算属性可以用于数值字面量。

他们也是只读的属性。

``` swift
let aMarathon = 42.km + 195.m
println("A marathon is \(aMarathon) meters long")
// prints "A marathon is 42195.0 meters long"
```

    注意
    扩展可以添加计算属性，但是不能添加存储属性，也不能注册属性观察者

### 初始化函数

扩展可以给已有类型添加新的初始化函数。这样我们可以扩展一些类型让他们可以接受我们自己的初始化参数，或者增加一些原有实现没有的初始化选项。

扩展可以给类添加便利初始化函数，但是不能添加指定初始化函数，或者反初始化函数。这两种函数必须在原有实现上实现。

    注意
    如果我们通过扩展给值类型添加一个初始化函数，而这个函数提供了所有存储属性的默认值，并且没有添加自定义的初始化函数，那么我们可以在扩展的初始化函数里调用默认初始化函数或按成员的初始化函数。
    
    因为如果我们在值类型里定义了自定义的初始化函数，默认的初始化函数就不会提供。但是扩展里可以。

``` swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
}
```

我们定义了3个结构体，他们都有默认的初始化函数和按成员的初始化函数。

``` swift
let defaultRect = Rect()
let memberwiseRect = Rect(origin: Point(x: 2.0, y: 2.0),
    size: Size(width: 5.0, height: 5.0))
```

我们添加一个扩展：

``` swift
extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

我们可以使用这个初始化来创建实例：

``` swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
    size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

    注意
    所有我们在扩展里添加的初始化函数，都依然需要保证初始化结束后，实例是完整的被初始化的。

### 方法

扩展还可以给已有类型添加实例方法或类型方法。

``` swift
extension Int {
    func repetitions(task: () -> ()) {
        for i in 0..<self {
            task()
        }
    }
}
3.repetitions({
    println("Hello!")
})
// Hello!
// Hello!
// Hello!
3.repetitions {
    println("Goodbye!")
}
// Goodbye!
// Goodbye!
// Goodbye!
```

注：这种看起来就和Ruby的有些相似了。

####    可变的实例方法

通过扩展添加的实例方法也可以修改实例本身。结构体和枚举方法修改的自己的必须添加 mutating。

``` swift
extension Int {
    mutating func square() {
        self = self * self
    }
}
var someInt = 3
someInt.square()
// someInt is now 9
```

### 角标

扩展也可以给给已有类型添加角标。

``` swift
extension Int {
    subscript(var digitIndex: Int) -> Int {
        var decimalBase = 1
        while digitIndex > 0 {
            decimalBase *= 10
            --digitIndex
        }
        return (self / decimalBase) % 10
    }
}
746381295[0]
// returns 5
746381295[1]
// returns 9
746381295[2]
// returns 2
746381295[8]
// returns 7
```

注：很有意思的扩展。。。

### 嵌套类型

扩展也可以在已有类型中添加新的嵌套类型。

``` swift
extension Int {
    enum Kind {
        case Negative, Zero, Positive
    }
    var kind: Kind {
        switch self {
        case 0:
            return .Zero
        case let x where x > 0:
            return .Positive
        default:
            return .Negative
        }
    }
}
```

``` swift
func printIntegerKinds(numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .Negative:
            print("- ")
        case .Zero:
            print("0 ")
        case .Positive:
            print("+ ")
        }
    }
    print("\n")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
// prints "+ + - 0 - 0 +"
```

















