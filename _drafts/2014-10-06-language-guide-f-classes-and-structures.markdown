---
layout: post
title: "Language Guide: Classes and Structures"
date: 2014-10-06 15:31:42 +0800
comments: true
categories: [Swift]
---

类和结构将成为构建你程序的基础，他们是通用的，灵活的结构。你用和常量，变量，函数一样的语法来给它们添加属性和方法。

和其他语言不同，Swift并不需要你为自定义的类和结构体分开定义接口和实现。在Swift里，你把它们定义在一个文件里，然后它们的对外接口就自动对于其他代码可见了。

    注意
    一个类的实例一般叫做对象（object）。但是因为在Swift里类和结构体的功能非常相似，所以我们主要适用实例（instance）来讲解。

#####   类 VS 结构体

类和结构体有很多相同的地方，例如：

*   可以定义属性来存储值
*   可以定义方法类提供功能
*   可以定义角标来使用角标语法访问他们的值
*   可以定义初始化函数来初始化他们
*   可以在默认实现的基础上通过扩展来增加功能
*   可以实现特定的协议来提供一些预先定义的功能

类有些额外的结构体没有的功能，例如：

*   类可以有继承
*   类型转换可以让你在运行时检查实例的类型
*   反初始化函数可以让实例释放任何它占有资源
*   引用计数允许一个实例被多次引用

    注意
    结构体在把它们来回传递的时候是被拷贝的，它们没有引用计数。
    
<!--more-->

######   定义语法

类和结构体有相似的定义语法，使用class来定义类，使用struct来定义结构体。都需要把它们放到一对大括号前面。

``` swift
class SomeClass{
}
struct SomeStruct{
}
```

    注意
    无论什么时候你定义一个类或结构体，你都在定义一个新的Swift类型。类型的名字使用首字母大写的驼峰风格来和Swift里标准类相匹配。反之，属性和方法的名字一般都用首字母小写的驼峰风格。

下面是一个结构体和类的定义。

``` swift
struct Resolution {
    var width = 0
    var height = 0
}
class VideoMode{
    var resolution = Resolution()
    var interlaced = false
    var frameRate = 0.0
    var name: String?
}
```

上面的例子里定义了一个用来描述基于像素的Resolution结构体，有两个存储属性，宽和高。存储属性是和类或者结构体一起存储的常量或变量。他们的类型由初始值推断为Int。

上面的例子还定义了一个叫VideoMode的类，来描述一个视频显示的特殊模式。这个类有四个存储属性。第一个，初始化为一个Resolution结构体，它的类型就是Resolution。其他三个分别是一个Bool，一个Double，一个optional的String。

######   类和结构体实例

上面的Resolution和VideoMode只是定义了他们长什么样子。他们自己并没有表示一个真正的分辨率或视频模式。你需要他们的实例来表示他们。

创建实例的语法对于类和结构体是一样的。

``` swift
let someResolution = Resolution()
let someVideoMode = VideoMode()
```

结构体和类都是用初始化函数来来初始化实例。最简单就是类或实例的名字后面加上括弧。这样子一个新的类或结构体的实例就被创建了，他们的属性也会被初始化为默认值。

######   访问属性

你可以 . 来访问实例的属性。在点语法里，你把属性的名字紧跟着写在实例的名字的后面，用 . 来分割，没有空格。

``` swift
println("the width is \(someResolution.width)")
```

你可以一直使用 . 来访问更深层次的属性：

``` swift
someVideoMode.resulotion.width = 1280
println("the width of videoMode is \(someVideo.resolution.width)")
```

    注意
    和ObjC不一样，Swift允许你直接设置一个结构体属性的子属性（注：在ObjC里，没有办法直接设置，例如frame.size.height。）

######   按成员初始化结构体的初始化函数

所有的机构体都一个自动生成的按成员来初始化的初始化函数。属性的初始化值可以做通过名字来传递给初始化函数。

``` swift
let vga = Resolution(width: 640, height: 480)
```

类没有默认的按成员来初始化的初始化函数。

#####   结构体和枚举是值类型

一个值类型是说这种类型在被赋值给常量或变量时，或是作为参数传递给一个函数时，它的值是被拷贝的。

事实上，Swift所有的基础类型，整数，浮点数，布尔值，字符串，数组，字典都是值类型，底层都是由结构体实现的。

所有的结构体和枚举都是值类型。这意味着任何的结构体，枚举实例，在传递时，都是被拷贝的。

例如：

``` swift
let hd = Resolution(width: 1920, height = 1080)
var cinema = hd
```

上面的例子，定义了一个Resolution的实例hd，然后把它赋值给了cinema变量。因为Resolution是结构体，所以cinema是得到了一份hd的拷贝。

如果我们修改cinema的属性，例如：

``` swift
cinema.width = 2048
```

这时候，我们分别查看hd和cinema的width属性，就发现hd的width并没有变化。

``` swift
println("width of hd is \(hd.width)")
println("width of cinema is \(cinema.width)")
```

枚举也有着同样的行为。

``` swift
enum CompassPoint {
    case North, South, East, West
}
var currentDirection = CompassPoint.West
let rememberedDirection = currentDirection
currentDirection = .East
if rememberedDirection == .West {
    println("The remembered direction is still .West")
}
```

#####   类是引用类型

和值类型不一样，引用类型在被复制和作为参数传递时不会被拷贝，而是用了同一实例的一份引用。

例如：

``` swift
let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0
```

这个例子创建了一个VideoMode的实例tenEighty，并给它的属性做了赋值。下面

``` swift
let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0
```

这段代码把tenEighty赋值给了一个常量alsoTenEighty，并改变了frameRate属性。因为类是引用类型，所以，事实上他们只是引用了同一个实例。

检查tenEighty的frameRate属性，会发现它的值将是30.0。

``` swift
println("the frame rate of tenEighty is \(tenEighty.frameRate)")
```

注意，tenEighty和alsoTenEighty都是常量，但是你依然可以改变他们的属性，因为tenEighty和alsoTenEighty他们自己的值并没有改变，他们自己并没有存储VideoMode实例，相反他们存储的一个指向VideoMode实例的引用。所以，是这个VideoMode的实例的frameRate属性被改变了，而不是指向这个实例的常量引用（constant reference）。

######  Identity操作符

因为类是引用类型，所有有可能多个常量和变量引用到同一个类型。（结构体和枚举就不会出现这个问题）。

有时候判断两个变量或常量是否准确的引用了同一个实例是很有用的，所以Swift提供了两个同一性操作符

    *   ===
    *   !==

用这个操作符来判断两个变量或常量是否引用了同一个实例。

注意同一性操作符和相等操作符（两个=）不一样的。

*   同一性是指两个变量或常量引用了同一个实例。
*   相等是指两个实例被认为他们的值相等或是他们类型定义的某种相等

当你定义自己的类和结构体时，你有责任给出两个实例相等的规则。

######  指针

如果你使用过C, C++, ObjC，你应该知道这些语言用指针来指向内存地址。在Swift里，一个常量或变量指向一个引用类型和指针是类似的，但是不是一个直接指向内存的指针，也不需要你使用 * 来表示你在使用引用。相反，这些引用和Swift其他常量和变量一样的。

#####   在类和结构体之间选择

你可以选择使用类和结构体来实现的你的程序。

然而，因为结构体是按值传递，类是按引用传递，这意味着他们适用于不同的场景。所以你需要考虑在哪些情况使用类，哪些使用结构体。

一般来讲，如果有下面的情况，你该考虑使用结构体：

*   这个结构体的主要目的就是封装一些简单的数据值
*   你需要明确在传递时要拷贝数据而不是引用
*   任何存储在结构体的属性也是值类型
*   这个结构不需要从其他地方继承属性或方法

例如，几何图形的大小，一个区间，一个三维空间的点，这些都很适合用结构体类定义。

其他情况，一般来说都应该使用类来定义。

#####   字符串，数组，字典的赋值和拷贝

Swift里，字符串，数组，字典都是值类型。用结构体实现。这意味着他们被赋值或传递给函数时是被拷贝的。

这个行为和Foundation里的NSString，NSArray，NSDictionary是不一样的，他们由类实现。

    注意
    上面的描述指的是字符串，数组，字典的拷贝。这种行为总是看上去拷贝发生了，然而，Swift只在需要真正拷贝的时候才会拷贝。Swift管理了所有的值拷贝来保证性能，所以你不需要考虑赋值带来的影响。

    