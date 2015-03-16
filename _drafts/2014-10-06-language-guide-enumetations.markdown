---
layout: post
title: "Language Guide: Enumerations"
date: 2014-10-06 14:42:46 +0800
comments: true
categories: [Swift]
---


枚举是给一系列相关的值定义一个通用的类型，让你在使用这些值的时候可以保证类型安全。

如果你熟悉C，你应该知道C里的枚举是把一些有意义的名字附值给一系列整数。Swift里的枚举更加灵活，你不需要为每一个成员都定义一个值。如果枚举成员有对应的值（raw值），这个值可以是整数，字符串，字符，或是浮点数。（注：没有关联值的枚举是Hashable的）

另外，枚举成员可以给出任何类型的关联值。更像其他语言里的unions和variants。你可以把相关成员的通用集合定义为枚举的一部分，每一部分又可以和不同类型的关联值关联到一起。

在Swift里，枚举是头等成员。它又有许多成员是以前只有类才有的特性，例如，用来更好给出枚举当前值的计算属性，和枚举值相关的成员函数。枚举还可以定义初始化函数来给出枚举初始值，也可以被扩展来增加原来没有的功能，还能够实现协议。

<!--more-->

#####	枚举语法

用enum关键字定义枚举。

``` swift
enum SomeEnumeration {
}
```
例如：

``` swift
enum CompassPoint {
	case North
	case South
	case East
	case West
}
```

定义在枚举里的值（North...)叫做枚举成员值（或成员）。case关键字用来标示新的成员。

	注意
	和C,ObjC不一样，Swift的枚举成员并没有默认的和整数相关联。

多个枚举值可以写在同一行，用逗号隔开。

``` swift
enum Planet{
	case Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
}
```

每个枚举都定义了一个新的类型。和其他类型一样，都应该由大写字母开头。用单数不要用复数。

``` swift
var direction = CompassPoint.West
```

上面direction的类型被推断为CompassPoint。在此之后，你可以直接用 . 来更新它的值。

``` swift
direction = .East
```

因为它的类型已经知道了，所以你不用再写出它的类型。

#####	用switch语句来匹配枚举值

``` swift
direction = .South
switch direction {
case .North:
    println("Lots of planets have a north")
case .South:
    println("Watch out for penguins")
case .East:
    println("Where the sun rises")
case .West:
    println("Where the skies are blue")
}
```

在流程控制里，我们提到switch语句必须是穷举的。如果上面我们没有写.West的case，这段代码就无法编译。穷举保证了枚举值不会被忽略。

如果不适合给每个枚举值都写case，可以使用default来做。

``` swift
let somePlanet = Planet.Earth
switch somePlanet {
case .Earth:
    println("Mostly harmless")
default:
    println("Not a safe place for humans")
}
```

#####	关联值（associated value）

上面的例子里，枚举值都是使用他们自己作为值。但是有时候，你如果能够给枚举值关联一些其他类型的值会很有用。这将让你可以存储额外的信息到每个枚举值，并使他们在用的时候可以变化。

你可以给枚举值定义任何类型的关联值，而且同一个枚举下不同枚举值可以有不同类型的关联值。像这样子的枚举在其他语言里，可能叫discriminated unions，tagged unions，或variants。

例如，一个库存管理系统，需要记录两种不同的条码，有些商品用的UPC-A的1维条码，只用到了0-9这些数字，每一个条码都有一个数字系统号，然后5个制造商号，5个产品号和一个校检号。另外一个些商品使用二维码来标示，可以使用IOS 8859-1的任何字符，可以编码一个长度达2953的字符串。

如果我们可以把UPC-A条码表示为4个整数，二维码表示为一个字符串将会很方便。

在Swift里，定义如下：

``` swift
enum Barcode {
	case UPCA(Int,Int,Int,Int)
	case QRCode(String)
}
```

这段代码理解为，定义一个枚举类型，叫Barcode，它要么是一个关联了4个整数的UPCA值，要么是一个关联了一个字符串的QRCode值。

这个定义只是定义了类型，并没有给出真实的值。任何Barcode类型的常量或变量都可以存储Barcode.UPCA或Barcode.QRCode。

新的barcode可以用任何一个case来创建。

``` swift
var code = Barcode.UPCA(8,85909,51226,3)
```

同一个产品可以被赋给不同的枚举值。

``` swift
code = .QRCode("ABCDEFGHIJKLMNOP")
```

Barcode类型的常量或变量可以存储UPCA和QRCode任意一种，但是在任何时间都只能是一种。

barcode的值也可以通过switch来区分，不过，这次，关联值可以被拆解出来作为switch语句的一部分。

``` swift
switch productBarcode {
case let .UPCA(numberSystem, manufacturer, product, check):
    println("UPC-A: \(numberSystem), \(manufacturer), \(product), \(check).")
case let .QRCode(productCode):
    println("QR code: \(productCode).")
}
```

注：这里我们也可以在多写一些分支加上where语句。

######	原始值（raw value）

除了上面说的关联值，作为另外一种方式，枚举可以有原始值，他们必须是同一类型的。

``` swift
enum ASCIIControlCharacter : Character {
	case Tab = "\t"
	case LineFeed = "\n"
	case CarriageReturn = "\r"
}
```

这里ASCIIControlCharacter的原始值的类型是Character。

注意原始值和关联值是不一样的。原始值在你定义枚举时就已经给出了。对于某一个枚举成员，原始值是固定不变的。关联值是你在给一个常量或变量赋值时才给出，而且每次都可以不一样。

原始值可以是字符串，字符，整数或浮点数。每一个原始值在枚举定义时必须唯一。如果是整数，会自动递增。

``` swift
enum Planet: Int {
	case Mercury = 1, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
}
```

上面重新定义了Planet枚举，它的原始值是整数，第一个从1开始，后面的依次递增。

使用 toRaw 来访问枚举的原始值。

``` swift
let earthIndex = Planet.Earth.toRaw() //earthIndex is 3 and is an Int.
```

用 fromRaw 来尝试把一个值作为原始值转换到枚举值。

``` swift
let possiblePlanet = Planet.fromRaw(7) //possiblePlanet的类型是 Planet?。
```

因为不是所有的整数都能转换到这个枚举值，所以 fromRaw返回的是一个optional。如果你给出的值无法转换到枚举，那么 fromRaw返回的就是nil。





























