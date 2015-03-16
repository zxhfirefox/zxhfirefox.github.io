---
layout: post
title: "Language Guide: Methods"
date: 2014-10-19 00:36:13 +0800
comments: true
categories: [Swift]
---

方法是关联到特殊类型的函数。类，结构体，枚举都可以定义实例方法，实例方法绑定到具体的实例。他们也都可以定义类型方法，类型方法关联到类型。类型方法和ObjC的类方法是相似的。

事实上，结构体和枚举可以定义方法是Swift和C，ObjC的主要区别之一。在ObjC里，只有类是可以定义方法的。在Swift里，类，结构体和枚举都可以定义方法。

<!--more-->

#####	实例方法

实例方法是属于一个类，结构体，枚举的实例的函数。他们要么提供访问实例属性，要么提供和实例相关的功能来支持这些实例。实例方法的语法和函数完全一样。

你把一个实例方法写在一个类型的大括号里。一个实例方法默认可以访问这个类型的所有其他方法和属性。实例方法仅能被类型的某个实例调用。没有实例，是无法调用实例方法的。

下面的例子：

``` swift
class Counter {
    var count = 0
    func increment() {
        count++
    }
    func incrementBy(amount: Int) {
        count += amount
    }
    func reset() {
        count = 0
    }
}
```

Counter类定义了一些实例方法。还定义了一个属性。

``` swift
let counter = Counter()
// the initial counter value is 0
counter.increment()
// the counter's value is now 1
counter.incrementBy(5)
// the counter's value is now 6
counter.reset()
// the counter's value is now 0
```

######  方法的外部和本地变量名

函数参数可以既有一个供外部使用的名字，还可以有一个供内部使用的名字。这点对于方法也是，因为方法仅仅是关联了一个类型的函数。然而，本地和外部名字的默认表现在函数和方法之间是有区别的。

Swift的方法和ObjC的很相似，和ObjC一样，Swift的方法的第一个参数总是有一个介词来修饰，例如with，for，by等等。使用介词，可以使方法调用读起来更像普通的句子。Swift通过使用一套不同于函数参数名字的方式来使这种命名规则得以继续。

特别的，Swift总是会给一个方法的第一个参数一个默认的内部名字，并且默认的给其他位置的参数同时内部和外部名字。这种规则和我们熟悉ObjC的方法调用的规则是一致的。

``` swift
class Counter {
    var count: Int = 0
    func incrementBy(amount: Int, numberOfTimes: Int) {
        count += amount * numberOfTimes
    }
}
```

incrementBy方法有两个参数，amount和numberOfTimes。默认的，Swift认为amount只是本地名字，而numberOfTimes既是本地名字，又是外部名字。

``` swift
let counter = Counter()
counter.incrementBy(5, numberOfTimes: 3)
```

你不需要为第一个参数定义外部的名字，因为它的目的可以从方法名得出，incrementBy。第二个参数，却需要一个外部参数来指出它的意义。

这个默认行为相当于你在第二个参数开始的所有参数都使用了 # 来标记。

``` swift
func incrementBy(amount: Int, #numberOfTimes: Int) {
    count += amount * numberOfTimes
}
```

上面描述的这种默认行为使得Swift的方法定义和ObjC很相似，调用起来更自然。

######  修改方法外部参数名字的行为

有时候，给方法的一个参数也给一个外部名字是有用的，尽管这不是默认行为。你可以显式给出一个名字，也可以给第一个参数加一个 # 来使用和本地名字一样的相同的外部名字。

相反的，如果不想给第二个以及其他参数给出外部名字，你可以使用 _ 来修改外部参数。

######  self属性

每一个类型的实例都有一个默认的属性叫 self，它总是和实例自己相一致。在方法里，是用self来引用当前的实例。

``` swift
func increment() {
    self.count++
}
```

事实上，你不需要经常使用self。如果你不写self，Swift会假设你在访问当前实例的一个属性或方法。

但是，如果你的方法的一个参数和一个属性的名字一样，那么你需要明确的使用self来区分参数和自己的属性。

``` swift
struct Point {
    var x = 0.0, y = 0.0
    func isToTheRightOfX(x: Double) -> Bool {
        return self.x > x
    }
}
let somePoint = Point(x: 4.0, y: 5.0)
if somePoint.isToTheRightOfX(1.0) {
    println("This point is to the right of the line where x == 1.0")
}
```

######  在实例方法里修改值类型

结构体和枚举是值类型，默认的，值类型的属性是不能在自己的实例方法被修改的。

但是，如果你需要在一个实例方法里修改结构体或枚举的属性，你可以把方法改成 mutating行为的。这个方法就可以在方法内部修改实例的属性，而且这些被修改的属性也会反映到真实的属性里。这种方法也可以直接把一个全新的实例附值给 self 属性，然后这个新实例将会在方法结束后代替原来的实例。

``` swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveByX(deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}
var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveByX(2.0, y: 3.0)
println("The point is now at (\(somePoint.x), \(somePoint.y))")
```

使用 mutating 关键字来修饰func定义。上面的例子中，moveByX方法修改了自己的属性。

注意，你不能在一个常量结构体或枚举上调用 mutating方法，因为他们的属性不能被改变，即使他们的属性是变量。

``` swift
let fixedPoint = Point(x: 3.0, y: 3.0)
fixedPoint.moveByX(2.0, y: 3.0)
// this will report an error
```

###### 在一个 mutating 方法里给 self 附值

mutating 方法可以把一个全新的实例附值给 self。

``` swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveByX(deltaX: Double, y deltaY: Double) {
        self = Point(x: x + deltaX, y: y + deltaY)
    }
}
```

这个版本的moveByX返回一个全新的Point。

枚举的 mutating方法可以给 self 附值一个新的枚举成员。

``` swift
enum TriStateSwitch {
    case Off, Low, High
    mutating func next() {
        switch self {
        case Off:
            self = Low
        case Low:
            self = High
        case High:
            self = Off
        }
    }
}
var ovenLight = TriStateSwitch.Low
ovenLight.next()
// ovenLight is now equal to .High
ovenLight.next()
// ovenLight is now equal to .Off
```

#####   类型方法

实例方法，如上所述，是在一个类型的具体实例上调用的方法。我们也可以定义调用在类型本身的方法。这种类型的方法叫类型方法。在类里添加类型方法使用 class关键字，在结构体和枚举里定义类型方法使用 static 关键字。

    注意
    在ObjC里，你只能给类定义类型方法。在Swift里，你可以给类，结构体，枚举定义类型方法。

类型方法也是用 . 来调用，不过 . 的左边不是实例，而是类型。

``` swift
class SomeClass {
    class func someTypeMethod() {
        // type method implementation goes here
    }
}
SomeClass.someTypeMethod()
```

在类型方法里， self 属性默认指向的就是类型本身，而不是实例。对于结构体和枚举，你可以使用 self 来区分静态属性和静态方法参数，就和实例方法一样。

更广泛来说，在类型方法里使用的任何未加修饰的方法和属性名字都会在类型级别寻找。类型方法可以调用类型方法，可以访问静态属性。

``` swift
struct LevelTracker {
    static var highestUnlockedLevel = 1
    static func unlockLevel(level: Int) {
        if level > highestUnlockedLevel { highestUnlockedLevel = level }
    }
    static func levelIsUnlocked(level: Int) -> Bool {
        return level <= highestUnlockedLevel
    }
    var currentLevel = 1
    mutating func advanceToLevel(level: Int) -> Bool {
        if LevelTracker.levelIsUnlocked(level) {
            currentLevel = level
            return true
        } else {
            return false
        }
    }
}
```

``` swift
class Player {
    var tracker = LevelTracker()
    let playerName: String
    func completedLevel(level: Int) {
        LevelTracker.unlockLevel(level + 1)
        tracker.advanceToLevel(level + 1)
    }
    init(name: String) {
        playerName = name
    }
}
```

``` swift
var player = Player(name: "Argyrios")
player.completedLevel(1)
println("highest unlocked level is now \(LevelTracker.highestUnlockedLevel)")
// prints "highest unlocked level is now 2"
```

``` swift
player = Player(name: "Beto")
if player.tracker.advanceToLevel(6) {
    println("player is now on level 6")
} else {
    println("level 6 has not yet been unlocked")
}
// prints "level 6 has not yet been unlocked"
```




























