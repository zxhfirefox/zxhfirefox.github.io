---
layout: post
title: "Language Guide: Optional Chaining"
date: 2014-10-25 10:22:10 +0800
comments: true
categories: [Swift]
---


Optional链条是一种在一个optional上查询，调用属性，方法和角标得时候，这个optional的值可能是nil得一种操作。如果这个optional有值，那么属性，方法，角标的调用会成功，如果optional是nil，属性，方法，角标的调用返回nil。多个查询可以链接在一起，整个链接在有任何一个地方为nil时简单的返回失败。

<!--more-->

    注意
    Optional链条有点类似于ObjC中给nil发送消息，但是它可以使用在任何类型上，而且也可以检查成功或失败。


### Optional链条作为强制解包的替换方案

我们在我们期望的属性，方法，角标后面加？来表示optional链条。这和在一个optional值的后面加！来强制解包很相似。主要区别在于如果optional值为nil，链条会简单的失败，而强制解包会触发运行时错误。

为了反映optional链条可以调用在一个nil上，整个链条的返回值都是一个optional的值，即使你访问的属性，方法，角标并不是optional的。我们可以使用这个返回值来检查optional链条的调用是否成功，或者因为有nil值而没有成功。

特别的，optional链条调用的结果和预期的结果类型相同，包裹在一个optional里。一个返回Int的属性会返回Int？。

``` swift
class Person{
    var residence: Residence?
}
class Residence {
    var numberOfRooms = 1
}
```

我们定义了两个类。


``` swift
let john = Person()
```

创建一个Person对象，这时候它的residence是nil。如果我们强制访问residence的numberOfRooms属性就会触发运行时错误。

``` swift
let roomCount = john.residence!.numberOfRooms
// this triggers a runtime error
```

然而，我们使用optional链条来处理这个：

``` swift
if let roomCount = john.residence?.numberOfRooms {
    println("John's residence has \(roomCount) room(s).")
} else {
    println("Unable to retrieve the number of rooms.")
}
// prints "Unable to retrieve the number of rooms."
```

因为residence是nil，所以optional链条失败，返回nil。

如果我们给residence附值。

``` swift
john.residence = Residence()
if let roomCount = john.residence?.numberOfRooms {
    println("John's residence has \(roomCount) room(s).")
} else {
    println("Unable to retrieve the number of rooms.")
}
// prints "John's residence has 1 room(s)."
```

optional链条就成功了，返回了正确的numberOfRooms。

### 为optional链条定义模型类

我们多层次的使用optional链条来调用属性，方法，角标。这使得你可以不断向下的访问复杂的模型类的属性，并且检查是否可以访问属性，方法，角标。

``` swift
class Person {
    var residence: Residence?
}
class Residence {
    var rooms = [Room]()
    var numberOfRooms: Int {
        return rooms.count
    }
    subscript(i: Int) -> Room {
        get {
            return rooms[i]
        }
        set {
            rooms[i] = newValue
        }
    }
    func printNumberOfRooms() {
        println("The number of rooms is \(numberOfRooms)")
    }
    var address: Address?
}
```

这次，我们的Residence有多个Room，所以numberOfRooms成为了一个计算属性。并实现了角标访问来访问或设置Room。最后，它还有一个optional的Address。

``` swift
class Room {
    let name: String
    init(name: String) { self.name = name }
}
class Address {
    var buildingName: String?
    var buildingNumber: String?
    var street: String?
    func buildingIdentifier() -> String? {
        if buildingName != nil {
            return buildingName
        } else if buildingNumber != nil {
            return buildingNumber
        } else {
            return nil
        }
    }
}
```

我们定义了Room和Address类，Address类有3个optional的属性，和一个返回optional的String的方法。

### 通过optional链条访问属性

我们可以通过optional链条来访问一个optional值的属性，并检查访问是否成功。

``` swift
let john = Person()
if let roomCount = john.residence?.numberOfRooms {
    println("John's residence has \(roomCount) room(s).")
} else {
    println("Unable to retrieve the number of rooms.")
}
// prints "Unable to retrieve the number of rooms."
```

上面的例子和之前一样，因为没有residence，所以没有办法访问房间数。

``` swift
let someAddress = Address()
someAddress.buildingNumber = "29"
someAddress.street = "Acacia Road"
john.residence?.address = someAddress
```

这次，我们通过optional链条来给属性附值，但是因为residence是nil，所以附值依然是失败的。

### 通过optional链条调用方法

我们可以用optional链条来调用一个optional值的方法，然后检查调用是否成功。即使这个方法不返回值，我们也能这么做。

Residence的printNumberOfRooms方法，没有给出返回值。

但是，没有给出返回值的方法其实是返回了Void类型，这意味着他们返回了一个空元组()。

如果我们把这个方法调用在optional链条时，这个方法会返回Void？，而不是Void。这样我们可以用if语句来检查我们能不能调用printNumberOfRooms方法即便方法自己没有定义返回值。

``` swift
if john.residence?.printNumberOfRooms() != nil {
    println("It was possible to print the number of rooms.")
} else {
    println("It was not possible to print the number of rooms.")
}
// prints "It was not possible to print the number of rooms."
```

任何尝试通过optional链条设置属性的操作都会返回一个Void？，我们可以用来检查设置属性是否成功：

``` swift
if (john.residence?.address = someAddress) != nil {
    println("It was possible to set the address.")
} else {
    println("It was not possible to set the address.")
}
// prints "It was not possible to set the address."
```

### 通过optional链条访问角标

我们可以通过在optional的值上使用optional链条来获取或设置角标的值，并且检查是否成功。

    注意
    当我们使用optional链条来访问角标值的时候我们总是把？放在方括号的前面。事实上，optional链条的？总是紧跟着optional的值。

``` swift
if let firstRoomName = john.residence?[0].name {
    println("The first room name is \(firstRoomName).")
} else {
    println("Unable to retrieve the first room name.")
}
// prints "Unable to retrieve the first room name."
```

？紧跟在residence后面，因为residence是我们要检查的optional值。

``` swift
john.residence?[0] = Room(name: "Bathroom")
```

通过角标设置也是，这个操作也是失败的，因为residence是nil。

``` swift
let johnsHouse = Residence()
johnsHouse.rooms.append(Room(name: "Living Room"))
johnsHouse.rooms.append(Room(name: "Kitchen"))
john.residence = johnsHouse
if let firstRoomName = john.residence?[0].name {
    println("The first room name is \(firstRoomName).")
} else {
    println("Unable to retrieve the first room name.")
}
// prints "The first room name is Living Room."
```

在我们给residence附值之后，optional链条的操作就会成功。

####    访问optional类型的角标

如果一个角标的返回值是optional，那我们在使用optional链条时需要把？放到方括号的后面。

``` swift
var testScores = ["Dave": [86, 82, 84], "Bev": [79, 94, 81]]
testScores["Dave"]?[0] = 91
testScores["Bev"]?[0]++
testScores["Brian"]?[0] = 72
// the "Dave" array is now [91, 82, 84] and the "Bev" array is now [80, 94, 81]
```

### 连接多个层次的链条

我们多层次的使用optional链条来访问深入访问模型的方法，属性和角标。但是，多层次的optional并不会增加多层次的optional值。

换句话说：

*   如果我们获取的类型不是optional的，它会变成optional。
*   如果获取的已经是optional的，它不会变的更加optional。

因此：

*   如果我们访问的返回值是Int，返回它会返回Int？
*   如果我们访问的已经是Int？，那么返回的就是Int？

``` swift
if let johnsStreet = john.residence?.address?.street {
    println("John's street name is \(johnsStreet).")
} else {
    println("Unable to retrieve the address.")
}
// prints "Unable to retrieve the address."
```

这里，我们连续的使用optional链条来访问street属性。而street属性本身就是String？的，所以整个链条返回的类型也是String？。

``` swift
let johnsAddress = Address()
johnsAddress.buildingName = "The Larches"
johnsAddress.street = "Laurel Street"
john.residence!.address = johnsAddress
if let johnsStreet = john.residence?.address?.street {
    println("John's street name is \(johnsStreet).")
} else {
    println("Unable to retrieve the address.")
}
// prints "John's street name is Laurel Street."
```

### 链接到有optional返回值的方法

上面的例子是我们通过optional链条来尝试访问一个optional类型的属性。

我们也可以把一个返回optional值的方法链接到链条里。

``` swift
if let buildingIdentifier = john.residence?.address?.buildingIdentifier() {
    println("John's building identifier is \(buildingIdentifier).")
}
// prints "John's building identifier is The Larches."
```

我们也可以继续把更多的方法链接到一起。

``` swift
if let beginsWithThe =
    john.residence?.address?.buildingIdentifier()?.hasPrefix("The") {
        if beginsWithThe {
            println("John's building identifier begins with \"The\".")
        } else {
            println("John's building identifier does not begin with \"The\".")
        }
}
// prints "John's building identifier begins with "The"."
```


    注意
    注意?在括弧的后面。

