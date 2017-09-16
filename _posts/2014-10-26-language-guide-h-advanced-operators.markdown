---
layout: post
title: "Language Guide: Advanced Operators"
date: 2014-10-26 23:28:03 +0800
comments: true
categories: [Swift]
---

除了在基础操作符里提到的那些操作符，Swift还提供了一些高级操作符。这些包含了位操作符。

和C里算数操作符不一样，Swift的算数操作符不会溢出。溢出行为会被捕获并报错。如果不想要这种行为，可以使用另外一套操作符，默认是会溢出的。所有这种操作符都是新加一个 & 。

<!--more-->

当我们定义自定义类时，我们可以提供一些Swift里的一些标准操作符。Swift简化了这一过程。

我们并不被限制在已有的操作符。Swift里我们可以定义任意的中缀，前缀和后缀，附值操作符，还可以自定义优先级和关联度。这些操作符可以在代码像已有操作符一样使用，我们甚至可以扩展已有操作符来支持自定义操作符。



### 位操作符

位操作符让我们可以处理数据结构里的单个原始数据，经常用于底层变成，例如，图形编程和设备驱动制作。位操作符在我们处理外来原始数据时也很有用，例如自定义协议的编码和解码。

Swift支持所有C定义的位操作符。

#### 按位取反

~ 反转一个数字的所有位。

        Input 00001111
              11110000 Result


按位取反操作符是前缀操作符，出现在操作数前，没有空格。

``` swift
let initialBits: UInt8 = 0b00001111
let invertedBits = ~initialBits  // equals 11110000
```

#### 按位与

& 把两个数组按位组合，结果的值只有两个参数都是1是才是1，其他都是0。

        Input1 11111100
               00111100 Result
        Input2 00111111      


``` swift
let firstSixBits: UInt8 = 0b11111100
let lastSixBits: UInt8  = 0b00111111
let middleFourBits = firstSixBits & lastSixBits  // equals 00111100
```

#### 按位或

| 把两个数组按位组合，结果的值只要有1个参数都是1是就是1，其他都是0。

        Input1 10110010
               11111110 Result
        Input2 01011110

``` swift
let someBits: UInt8 = 0b10110010
let moreBits: UInt8 = 0b01011110
let combinedbits = someBits | moreBits  // equals 11111110
```

#### 按位异或

^ 操作符，组合两个数字，当两个数字位的值相同时，结果是0，不相同时是1。
        
        Input1 00010100
               00010001 Result
        Input2 00000101

``` swift
let firstBits: UInt8 = 0b00010100
let otherBits: UInt8 = 0b00000101
let outputBits = firstBits ^ otherBits  // equals 00010001
```

####    按位左移和右移操作符

<< 和 >> 分别把一个数字的所有位向左和向右移动特定数字位。

按位移动操作符相当于对数字做乘以2的指数幂或除以2指数幂。往左一位相当于乘以2，往右相当于除以2.

####    无符号整数的位移

1.  现有位移动到指定的位置。
2.  任何超出边界的位都被遗弃。
3.  剩余空位补0.

这种叫做逻辑位移。

``` swift
let shiftBits: UInt8 = 4   // 00000100 in binary
shiftBits << 1             // 00001000
shiftBits << 2             // 00010000
shiftBits << 5             // 10000000
shiftBits << 6             // 00000000
shiftBits >> 2             // 00000001
```

我们可以使用位移来编码和解码。

``` swift
let pink: UInt32 = 0xCC6699
let redComponent = (pink & 0xFF0000) >> 16    // redComponent is 0xCC, or 204
let greenComponent = (pink & 0x00FF00) >> 8   // greenComponent is 0x66, or 102
let blueComponent = pink & 0x0000FF           // blueComponent is 0x99, or 153
```

####    有符号整数的位移

有符号的位移要比无符号数的位移复杂许多，主要是和有符号数在内存的表示方式有关。

有符号数使用第一位来作为符号位，标示正数还是负数。0是正数，1是负数。

剩余的位是值位，正数就是按照整数本身来存储的。

        0 000 0100 = 4

负数，就不一样了，他们存储的是他们值的绝对值减掉2的n次幂，n是值位的个数。如果是Int8，那么n就是7，减去的就是128。

        1 111 1100 = -4
        0 111 1100 = 124

负数的编码叫做补码。它有一些优点。

首先做加法时，我们可以直接加，包括符号位，然后舍弃任何超出范围的位。

        0 1111 1100 = -4
    +   0 1111 1111 = -1
    =   1 1111 1011 = -5

其次，使用补码，我们在位移时可以像处理正数一样。但是有个额外的规则。
    *   在右移时，补位用符号位而不是0。

        1111111         01111111
        11111111        001111111

这样就确保了在位移后，数字的符号位不会变化，叫做算数位移。

因为正数和负数存储的特殊方式，右移他们都是把它们向0靠近。保留符号位意味着负数还是负数，同时更加接近0。

### 溢出操作符

如果我们尝试把一个超出存储范围的数字附值给一个常量或变量，Swift会报错。

例如，Int16的值的范围是-32768到32767。

``` swift
var potentialOverflow = Int16.max
// potentialOverflow equals 32767, which is the largest value an Int16 can hold
potentialOverflow += 1
// this causes an error
```

在这种时候，我们需要提供错误处理。
    *   加   &+
    *   减   &-
    *   乘   &*
    *   除   &/
    *   取余 &%

####    值上溢出

``` swift
var willOverflow = UInt8.max
// willOverflow equals 255, which is the largest value a UInt8 can hold
willOverflow = willOverflow &+ 1
// willOverflow is now equal to 0
```

        0 1111 1111 = 255
    &+  0 0000 0001 =   1
    =   1 0000 0000 =   0


####    值下溢出

``` swift
var willUnderflow = UInt8.min
// willUnderflow equals 0, which is the smallest value a UInt8 can hold
willUnderflow = willUnderflow &- 1
// willUnderflow is now equal to 255
```

        0000 0000 = 0
    &-  0000 0001 = 1
    =   1111 1111 = 255

有符号数也会发生下溢出。符号数减法时，所有位都参与，包括符号位。

``` swift
var signedUnderflow = Int8.min
// signedUnderflow equals -128, which is the smallest value an Int8 can hold
signedUnderflow = signedUnderflow &- 1
// signedUnderflow is now equal to 127
```

        1000 0000 = -128
    &-  0000 0001 = 1
    =   0111 1111 = 127

上面的这些溢出的结构，都是在最大值后回到了最小值，或者最小值后回到了最大值。

####    除以0

当我们尝试除以0或者对0取余时，会报错。

``` swift
let x = 1
let y = x / 0
```

然后如果用溢出操作符时，返回0.

``` swift
let x = 1
let y = x &/ 0
// y is equal to 0
```

### 优先级和关联性

操作符的优先级是说某些操作符优先于其他操作符进行计算。

操作符的关联性是说，如果两个相同优先级的操作符在一起时，是算到左边还是右边。这样想，他们和他们左边的表达式在一起，或者他们和他们右边的表达式在一起。

当我们考虑表达式如果计算时，优先级和关联性很重要。

``` swift
2 + 3 * 4 % 5
// this equals 4
```

    注意
    Swift的操作符优先级和关联性和C，ObjC中得很相似，并且更可预测。但是这意味着他不一样，所以在使用时需要注意。

### 操作符函数

类和结构体可以提供现有操作符的自定义实现，这叫做操作符重载。

``` swift
struct Vector2D {
    var x = 0.0, y = 0.0
}
func + (left: Vector2D, right: Vector2D) -> Vector2D {
    return Vector2D(x: left.x + right.x, y: left.y + right.y)
}
```

这个操作符函数被定义为全局函数，函数名和操作符匹配。因为是中缀操作符，所以有两个参数。

``` swift
let vector = Vector2D(x: 3.0, y: 1.0)
let anotherVector = Vector2D(x: 2.0, y: 4.0)
let combinedVector = vector + anotherVector
// combinedVector is a Vector2D instance with values of (5.0, 5.0)
```

####    前缀和后缀操作符

上面的例子是一个中缀操作符，类和结构体也可以提供一元操作符，可以区分前缀和后缀。

我们需要在函数前面写上 prefix 或 postfix来表示操作符是前缀还是后缀。

``` swift
prefix func - (vector: Vector2D) -> Vector2D {
    return Vector2D(x: -vector.x, y: -vector.y)
}
```

``` swift
let positive = Vector2D(x: 3.0, y: 4.0)
let negative = -positive
// negative is a Vector2D instance with values of (-3.0, -4.0)
let alsoPositive = -negative
// alsoPositive is a Vector2D instance with values of (3.0, 4.0)
```

####    组合附值操作符

组合附值操作符把附值操作符（=）和其他操作符组合起来。例如，+=是说把加法和附值操作符合为一个操作符。我们实现时需要把一个参数定义为 inout。

``` swift
func += (inout left: Vector2D, right: Vector2D) {
    left = left + right
}
```

``` swift
var original = Vector2D(x: 1.0, y: 2.0)
let vectorToAdd = Vector2D(x: 3.0, y: 4.0)
original += vectorToAdd
// original now has values of (4.0, 6.0)
```

我们可以把前缀或后缀与组合附值操作符结合使用。

``` swift
prefix func ++ (inout vector: Vector2D) -> Vector2D {
    vector += Vector2D(x: 1.0, y: 1.0)
    return vector
}
var toIncrement = Vector2D(x: 3.0, y: 4.0)
let afterIncrement = ++toIncrement
// toIncrement now has values of (4.0, 5.0)
// afterIncrement also has values of (4.0, 5.0)
```

    注意
    我们无法重载默认的附值操作符（=）。另外，三元条件操作符（a?b:c）也不能被重载。

####    相等操作符

自定义的类和结构体，默认并会有 == 和 != 操作符。

``` swift
func == (left: Vector2D, right: Vector2D) -> Bool {
    return (left.x == right.x) && (left.y == right.y)
}
func != (left: Vector2D, right: Vector2D) -> Bool {
    return !(left == right)
}
```

我们实现了 == 之后，在实现 != 时只需要取反即可。

``` swift
let twoThree = Vector2D(x: 2.0, y: 3.0)
let anotherTwoThree = Vector2D(x: 2.0, y: 3.0)
if twoThree == anotherTwoThree {
    println("These two vectors are equivalent.")
}
// prints "These two vectors are equivalent."
```

### 自定义操作符

我们可以自定义和实现一些操作符。

新的操作符，定义在全局范围，使用 operator 关键字，标示为 prefix，infix或者postfix。

``` swift
prefix operator +++ {}
```

我们定义了一个 +++ 操作符，它对于Swift没有任何意义。所以可以被用于Vector2D。

``` swift
prefix func +++ (inout vector: Vector2D) -> Vector2D {
    vector += vector
    return vector
}
var toBeDoubled = Vector2D(x: 1.0, y: 4.0)
let afterDoubling = +++toBeDoubled
// toBeDoubled now has values of (2.0, 8.0)
// afterDoubling also has values of (2.0, 8.0)
```

####    自定义中缀操作符的优先级和关联性

我们自定义中缀操作符时，还需要给出优先级和关联性。

关联性可以是，left，right或none。默认是none。优先级默认是100。

``` swift
infix operator +- { associativity left precedence 140 }
func +- (left: Vector2D, right: Vector2D) -> Vector2D {
    return Vector2D(x: left.x + right.x, y: left.y - right.y)
}
let firstVector = Vector2D(x: 1.0, y: 2.0)
let secondVector = Vector2D(x: 3.0, y: 4.0)
let plusMinusVector = firstVector +- secondVector
// plusMinusVector is a Vector2D instance with values of (4.0, -2.0)
```

    注意
    我们在定义前缀和后缀操作符时，不需要给出优先级。但是如果我们一个操作数同时使用前缀和后缀操作符时，后缀操作符优先执行。


