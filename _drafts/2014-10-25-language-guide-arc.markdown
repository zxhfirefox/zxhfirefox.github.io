---
layout: post
title: "Language Guide: ARC"
date: 2014-10-25 10:21:04 +0800
comments: true
categories: [Swift]
---

Swift使用自动引用计数（ARC）来追踪和管理内存使用。在绝大多数情况下，这意味着内存管理并不需要你自己去思考太多。ARC自动会把那些不在需要的对象实例的内存释放掉。

然而，在少数情况下，ARC需要对实例之间的关系有更明确的了解来更好的管理内存。这里我们就讲解这些情况来让ARC更好的管理内存。

    注意
    引用计数只针对类实例，枚举和结构体都是值对象，不是引用对象。


<!-- more -->

### ARC原理

每当你创建一个类的实例，ARC会为这个实例分配一定的内存。这块内存包含实例类型的信息和类型存储属性的值。

另外，当一个实例不在需要时，ARC会释放之前分配给这个实例的对象，回收的内存会作为其他使用。这样确保了不在使用的实例不会依然占用内存。

然而，如果ARC要销毁一个仍在使用的实例，那我们就无法在访问这个实例的属性，调用它的方法。事实上，如果你这么做了，程序就会崩溃。

为了确保实例在我们需要的时候不会被销毁，ARC会追踪有多少属性，常量，变量正在引用每一个类实例。只要还有引用存在，ARC就不会去释放对象的内存。

每当你把一个类实例附值给一个属性，常量或变量，这个属性，常量或变量就会对这个实例有一个强引用。这个引用叫强引用是因为它可以保持持有一个对象，不允许这个对象被销毁。

### ARC实战

``` swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
        println("\(name) is being initialized")
    }
    deinit {
        println("\(name) is being deinitialized")
    }
}
```

我们先定义一个类Person，在初始化和反初始化里打印一些信息。

``` swift
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

声明3个optional的Person变量，初始值为 nil。

``` swift
reference1 = Person(name: "John Appleseed")
// prints "John Appleseed is being initialized"
```

创建一个对象。这时候reference1 是对这个实例的一个强引用。

``` swift
reference2 = reference1
reference3 = reference1
```

我们把reference1附值给reference2和reference3，这样我们又多了2个强引用到我们的实例。

``` swift
reference1 = nil
reference2 = nil
```

我们reference1和reference2 设置为 nil。这样他们就不在持有实例的强引用，注意，我们虽然把reference1 设置了 nil，实例的反初始化函数并没有调用，因为我们其实还有reference3持有对象的强引用。

``` swift
reference3 = nil
// prints "John Appleseed is being deinitialized"
```

在我们把reference3 设置 nil 之后， ARC会把实例对象销毁。

### 类实例间的强引用循环

在上面的例子中，ARC可以追踪对Person实例的强引用，并且在没有强引用释放内存。

然而，我们有可能写出使一个类实例的强引用永远不会降到0的代码。比如我们让两个对象互相引用对方，那么这2个对象一致持有对方，这叫做强引用循环。

我们通过在这种引用关系里定义一些类似于弱的，非持有的关系来打断引用循环。

我们先来看看引用循环是如何造成的。

``` swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { println("\(name) is being deinitialized") }
}
class Apartment {
    let number: Int
    init(number: Int) { self.number = number }
    var tenant: Person?
    deinit { println("Apartment #\(number) is being deinitialized") }
}
```

我们定义了2个类，Person有一个名字，和一个optional的apartment，初始值是nil。Apartment有个数字，和一个optional的tenant，初始值为nil。两个类都实现了反初始化函数来打印一些信息。

``` swift
var john: Person?
var number73: Apartment?
john = Person(name: "John Appleseed")
number73 = Apartment(number: 73)
```

我们分别创建两个实例。这时候john的apartment和number73的tenant都还是nil。

``` swift
john!.apartment = number73
number73!.tenant = john
```

然后，我们把他们关联起来，注意使用 ！来解包。在这时候，两个实例互相之间都拿着对方的强引用。

``` swift
john = nil
number73 = nil
```

尽管我们把变量设置为nil。也没有触发反初始化函数，证明了他们之间存在互相的强引用。这样我们再也无法获取到这2个实例的内存。我们的程序就内存泄露了。

### 解决强引用循环

Swift有两种方式来解决强引用循环。弱引用（weak reference）和 非持有引用（unowned reference）。

他们两个都使得一个实例引用到另一个实例，但不是一个强引用。这样对象之间就可以互相引用而不形成强引用循环。

当你知道如果这个引用有时候会成为nil的时候使用弱引用。相反的，如果你知道这个引用一旦设置就永远不会是nil得时候使用非持有引用。

####    弱引用

弱引用是一种不会持有实例的引用，也就不会阻止ARC去销毁实例。这种行为可以阻止强引用循环。使用 weak 关键字来声明弱引用。

如果这个引用在使用期间可能是nil得话，就需要使用weak，如果这个引用在使用期间总是有值，那么就该使用非持有引用。在上面的例子里，因为apartment可能没有tenant，所以用weak来打破引用循环比较合适。

    注意
    弱引用必须声明为变量，来表示他们的值可能是nil。弱引用不能是常量。

因为弱引用允许没有值，所以我们必须把每一个弱引用都声明为optional的。

因为弱引用不会阻止ARC销毁实例，所以ARC在销毁实例时，弱引用不应该继续引用实例。ARC会在销毁时把弱引用设置为nil。我们可以像检查普通optional一样来检查弱引用是否为nil。

``` swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { println("\(name) is being deinitialized") }
}
class Apartment {
    let number: Int
    init(number: Int) { self.number = number }
    weak var tenant: Person?
    deinit { println("Apartment #\(number) is being deinitialized") }
}
var john: Person?
var number73: Apartment?
john = Person(name: "John Appleseed")
number73 = Apartment(number: 73)
john!.apartment = number73
number73!.tenant = john
john = nil
// prints "John Appleseed is being deinitialized"
number73 = nil
// prints "Apartment #73 is being deinitialized"
```

####    非持有引用

和弱引用一样，非持有引用也不会对实例强引用。和弱引用不一样，非持有引用会认为引用的实例一致存在。因为这个，非持有引用一般并不需要定义为optional的。使用 unowned 关键字来定义非持有引用。

因为非持有引用不是optional的，所以你不用每次都去解包。但是,ARC没有办法在实例被销毁时把非持有引用设置为nil，因为他不是optional的。
    
    注意
    如果我们尝试在一个实例已经被销毁了，还去访问一个非持有引用，这将是运行时错误。只有在你确认对象不会销毁的情况下使用非持有引用。
    
    另外，Swift确保你在这么做的时候肯定崩溃。所以我们需要避免它。


``` swift
class Customer {
    let name: String
    var card: CreditCard?
    init(name: String) {
        self.name = name
    }
    deinit { println("\(name) is being deinitialized") }
}
class CreditCard {
    let number: UInt64
    unowned let customer: Customer
    init(number: UInt64, customer: Customer) {
        self.number = number
        self.customer = customer
    }
    deinit { println("Card #\(number) is being deinitialized") }
}
```

我们定义了两个类。Customer和CreditCard。因为CreditCard从创建开始就会一值有一个持有者。所以为了避免引用循环，这里使用了unowned。

``` swift
var john: Customer?
john = Customer(name: "John Appleseed")
john!.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)
```

我们创建对象，并把他们关联起来。

``` swift
john = nil
// prints "John Appleseed is being deinitialized"
// prints "Card #1234567890123456 is being deinitialized"
```

当我们把john设置为nil时，它引用的Custyomer实例就不在被任何强引用引用，所以被ARC销毁，在这之后，对应的CreditCard实例也不再被任何强引用引用。所以也跟着被ARC销毁了。

####    非持有引用和自动解包的optional属性

我们分别用例子讲解了弱引用和非持有引用来打断强引用循环的方式。

然而，有第三种情况，在这里，两个属性都应该有值，而且在初始化结束后就该一直有值。这时候，我们需要把一个类的非持有属性和一个另一个类的自动解包的optional组合起来。

这样，我们可以直接访问这两个属性，不用解包，同时也避免了引用循环。

``` swift
class Country {
    let name: String
    let capitalCity: City!
    init(name: String, capitalName: String) {
        self.name = name
        self.capitalCity = City(name: capitalName, country: self)
    }
}
class City {
    let name: String
    unowned let country: Country
    init(name: String, country: Country) {
        self.name = name
        self.country = country
    }
}
```

在上面的例子中，一个Country都必须有个capitalCity，每个City都必须有个country。

City的初始化函数在Country的初始化函数里调用，它需要一个Country实例，但是根据两步初始化的要求，Country在它的初始化函数里在实力初始化完毕之前无法使用self。所以capitalCity是一个自动解包的optional，它的初始值为nil，也就是说在设置capitalCity时，Country对象已经初始化完毕。这样就可以用来创建City对象。

所有这些都意味着我们可以再一个语句里创建Country和City对象，也不会造成引用循环，而且capitalCity可以直接被访问，不需要加 ！ 来解包。

``` swift
var country = Country(name: "Canada", capitalName: "Ottawa")
println("\(country.name)'s capital city is called \(country.capitalCity.name)")
// prints "Canada's capital city is called Ottawa"
```

### 闭包的强引用循环

我们说明了如何让两个实例互相引用对方来造成引用循环，也说明了如何使用弱引用和非持有引用来打破引用循环。

当我们把一个闭包附值给一个实例的属性时，如果这个闭包捕获了这个实例，那么这也是一种强引用循环。这种捕获可能是闭包里访问了实例的属性（self.someProperty)或是调用了方法(self.someMethod)。不管哪一种，闭包都捕获了self，导致了引用循环。

这种引用循环的发生是因为闭包和类一样，也是引用类型。当我们把闭包附值给属性时，我们是把闭包的引用附值了。这样，其实就是两个强引用互相引用了对方。然而，不是两个实例，而是一个实例和一个闭包。

Swift通过一种叫做闭包捕获列表的方式来解决这个问题。我们先来看如何造成这样的引用循环。

``` swift
class HTMLElement {
    let name: String
    let text: String?
    lazy var asHTML: () -> String = {
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }
    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }
    deinit {
        println("\(name) is being deinitialized")
    }
}
```

HTMLElement定义了两个属性name和text来表示HTML元素。它还定义了一个延迟属性，asHTML，它的类型是 ()->String。

这个asHTML属性看起来是个方法，但是它使属性，我们可以改变它的实现来自定义实现。

    注意
    asHTML是延迟的（lazy）。因为我们只是在需要的时候才会调用访问这个属性。因为是延迟的，我们可以在闭包里使用self，因为延迟属性只会在初始化结束后被访问，而这时候，self是已经存在的。

``` swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
println(paragraph!.asHTML())
// prints "<p>hello, world</p>"
```

我们创建一个实例，访问asHTML属性。

可是，我们上面所写的，会造成强引用循环。

    注意
    虽然我们在闭包里多次引用self。但是这也只会造成一个强引用。

``` swift
paragraph = nil
```

我们把paragraph设置为nil，但是HTMLElement的反初始化函数并没有调用，也证明了存在强引用循环。

### 解决闭包的强引用循环

我们通过在闭包定义时给出捕获列表来解决闭包的强引用循环。捕获列表定义了当闭包捕获一个或多个引用时的规则。就好像两个实例强引用一样，我们把捕获列表里的引用表示为弱引用或非持有引用。弱引用还是非持有引用取决其他部分代码之间的关系。

    注意
    Swift要求我们在闭包中访问属性或调用方法时都必须加self。这样提醒我们，闭包会捕获self。

####    定义捕获列表

在捕获列表里的每一个元素都是一对由weak或unowned和一个类实例（self或someInstane）。这些元恶写在方括号里，由逗号隔开。

如果闭包有参数列表和返回值，捕获列表需要写在他们前面：

``` swift
lazy var someClosure: (Int, String) -> String = {
    [unowned self] (index: Int, stringToProcess: String) -> String in
    // closure body goes here
}
```

如果没有参数列表或返回值，捕获列表可以写在闭包的一开始，跟着 in ：

``` swift
lazy var someClosure: () -> String = {
    [unowned self] in
    // closure body goes here
}
```

####    弱引用和非持有引用

如果闭包和捕获的实例会一直互相引用的话，在捕获列表用 unowned，这样他们会同时被销毁。

如果捕获的实例有可能为nil，那么就是用 weak。 弱引用永远都是optional的，也会自动设置为nil。这样你需要在闭包里检查他们是否还存在。

    注意
    如果捕获的引用永远不会是nil，那么应该用 unowned。

``` swift
class HTMLElement {
    let name: String
    let text: String?
    lazy var asHTML: () -> String = {
        [unowned self] in
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }
    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }
    deinit {
        println("\(name) is being deinitialized")
    }
}
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
println(paragraph!.asHTML())
// prints "<p>hello, world</p>"
paragraph = nil
// prints "p is being deinitialized"
```






















