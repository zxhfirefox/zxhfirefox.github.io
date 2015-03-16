---
layout: post
title: "Language Guide: Initialization"
date: 2014-10-19 00:39:16 +0800
comments: true
categories: [Swift]
---

初始化是让一个类，结构体，枚举的实例变的可用的过程。这个过程包含了把实例上的每一个存储属性都给与一个初始值，还有任何的确保在实例可以被使用前需要做的工作。

我们使用初始化函数来做初始化，初始化函数是一种特殊的函数，在实例被创建时，它会被调用。和ObjC的初始化函数不同，Swift的初始化函数不返回值。他们的主要目的就是确保这个新的实例在被使用之前正确的初始化。

类类型的实例也可以实现反初始化函数，它在实例被销毁时可以用来做任何的清理工作。

<!--more-->

#####   为存储属性设置初始值

类和结构体必须在实例被使用前为所有的存储属性设置初始值。存储属性不能处于未决定的状态。

我们可以在初始化函数给存储属性初始值，也可以在定义属性时给出初始值。

    注意
    在初始化函数里给属性附值和定义属性时给出初始值，都不会触发属性观察者

######  初始化函数

初始化函数在创建一个类型的实例后被调用，一个最简单的初始化函数就像一个实例方法，没有参数，函数名是 init 关键字。

``` swift
init(){
}
```

例如：

``` swift
struct Fahrenheit {
    var temperature: Double
    init() {
        temperature = 32.0
    }
}
var f = Fahrenheit()
println("The default temperature is \(f.temperature)° Fahrenheit")
```

######  默认属性值

我们可以像上面的例子里那样在初始化函数里设置属性初始值。也可以在属性定义时给出初始值。

    注意
    如果一个属性总是有着同样的初始值，我们应该在属性定义时给出初始值，而不是在初始化函数里给出。

我们把上面的例子可以改写为：

``` swift
struct Fahrenheit {
    var temperature = 32.0
}
```

#####   自定义初始化

我们可以通过给出输入参数，或optional的属性类型，或是在初始化时改变常量属性来自定义初始化过程。

######  初始化参数

我们可以给初始化函数定义参数，初始化参数的能力和函数，方法的一样。

``` swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
}
let boilingPointOfWater = Celsius(fromFahrenheit: 212.0)
// boilingPointOfWater.temperatureInCelsius is 100.0
let freezingPointOfWater = Celsius(fromKelvin: 273.15)
// freezingPointOfWater.temperatureInCelsius is 0.0
```

上面的例子里，两个初始化函数接受不同的温度值，分别转换到Celsius温度。

######  本地和外部参数名

和函数，方法一样，初始化参数也可以本地参数名和外部参数名。

然而，初始化函数并不像函数和方法一样，有一个函数名。因此，参数的名字和类型将决定哪一个初始化函数被调用。因此，如果我们不给出外部参数名子，Swift会默认给每一个参数一个外部名字。这个外部名字和本地名字一样，就好像你给每个参数都写 # 一样。

``` swift
struct Color {
    let red, green, blue: Double
    init(red: Double, green: Double, blue: Double) {
        self.red   = red
        self.green = green
        self.blue  = blue
    }
    init(white: Double) {
        red   = white
        green = white
        blue  = white
    }
}
let magenta = Color(red: 1.0, green: 0.0, blue: 1.0)
let halfGray = Color(white: 0.5)
```

注意，我们无法不给外部参数名字来调用初始化函数，即使我们可以通过参数个数来确定使用哪个初始化函数。

``` swift
let veryGreen = Color(0.0, 1.0, 0.0)
// this reports a compile-time error - external names are required
```

######  没有外部名字的初始化参数

如果你真的不像给出初始化参数的外部名字， 使用 _ 来作为外部参数名字给出。

``` swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
    init(_ celsius: Double) {
        temperatureInCelsius = celsius
    }
}
let bodyTemperature = Celsius(37.0)
// bodyTemperature.temperatureInCelsius is 37.0
```

因为外部参数名字是 _ ，所以可以直接调用，而不给出参数名字。

######  optional的属性类型

如果我们自定义的类型有一个存储属性在逻辑上可能没有值，或许因为它的值在初始化时还不能确定，或许因为它在某些时刻允许没有值，我们把它声明为optional的属性。optional的属性默认被初始化为 nil，表明这个属性在初始化时没有值。

``` swift
class SurveyQuestion {
    var text: String
    var response: String?
    init(text: String) {
        self.text = text
    }
    func ask() {
        println(text)
    }
}
let cheeseQuestion = SurveyQuestion(text: "Do you like cheese?")
cheeseQuestion.ask()
// prints "Do you like cheese?"
cheeseQuestion.response = "Yes, I do like cheese."
```

上面的例子里，response是一个optional的字符串。

######  在初始化阶段修改常量属性的值

我们可以再初始化阶段修改一个常量属性的值，只要在初始化结束之前给它一个值。

    注意
    对于类实例，一个常量属性只能在定义属性的类的初始化过程里被修改，而不能在子类里被修改。

``` swift
class SurveyQuestion {
    let text: String
    var response: String?
    init(text: String) {
        self.text = text
    }
    func ask() {
        println(text)
    }
}
let beetsQuestion = SurveyQuestion(text: "How about beets?")
beetsQuestion.ask()
// prints "How about beets?"
beetsQuestion.response = "I also like beets. (But not with cheese.)"
```
我们text属性改为常量，来表示问题并会改变。虽然他是常量，但是初始化过程中还是可以被改变。

#####   默认初始化函数

对于任意的结构体或基类，当他们的所有属性都一个默认值，并且没有给出任何初始化函数的时候，Swift给出一个默认初始化函数。这个初始化函数就是简单的把所有的默认值附值给属性。

``` swift
class ShoppingListItem {
    var name: String?
    var quantity = 1
    var purchased = false
}
var item = ShoppingListItem()
```

因为ShoppingListItem的所有属性都有默认值，而且他还没有基类，也没给出自己的初始化函数，Swift会为它提供一个默认的初始化函数。

######  结构体类型的按成员的初始化函数

结构体对象如果没有给出自己的初始化函数，将得到一个默认的按成员初始化的初始化函数。即使有些属性并没有给出默认值。

按成员初始化的初始化函数是一种初始化结构体的一种简写，初始值根据名字传递给初始化函数。

``` swift
struct Size {
    var width = 0.0, height = 0.0
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```

#####   值类型的初始化代理

初始化函数可以调用其他初始化函数来执行初始化过程，这种方式叫做初始化代理，可以避免重复代码。

初始化代理如果工作，那种形式的代理是可以的，在值类型和类类型是不一样的。值类型，不支持继承，所以他们的初始化代理比较简单，因为他们只能把初始化代理给其他的由他们自己提供的初始化函数。然而，类，可以从其他类继承。这就意味着类有着额外的责任来确保他们继承的属性也被初始化。

对于值类型，self.init 来引用其他的初始化函数， self.init只能在一个初始化函数里被调用。

如果我们给值类型定义了自定义的初始化函数，那么我们就无法再访问默认的初始化函数。这个限制是为了防止我们有额外的初始化步骤，而有时候不小心使用了默认的初始化函数来初始化了实例。

    注意
    如果我们希望使用默认的初始化函数，还想定义自定义的初始化函数，我们可以把自定义的初始化函数写在扩展里。

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
    init() {}
    init(origin: Point, size: Size) {
        self.origin = origin
        self.size = size
    }
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

第一个init，就相当于默认初始化函数。
第二个init，相当于按成员来初始化的初始化函数。
第三个init，是我们自己的，相对复杂的初始化函数。

``` swift
let basicRect = Rect()
// basicRect's origin is (0.0, 0.0) and its size is (0.0, 0.0)
let originRect = Rect(origin: Point(x: 2.0, y: 2.0),
    size: Size(width: 5.0, height: 5.0))
// originRect's origin is (2.0, 2.0) and its size is (5.0, 5.0)
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
    size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

#####   类继承和初始化

一个类的所有的存储属性，包括任何继承的属性，都必须在初始化过程中被初始化。

Swift定义了两种初始化函数来简化并确保所有的属性都被初始化，叫做指定初始化函数和便利初始化函数。

######  指定初始化函数和便利初始化函数

指定初始化函数是一个类的基本初始化函数。一个指定初始化函数初始化一个类所有属性，并且调用父类的初始化函数来完成初始化过程。

类应该只有很少的指定初始化函数，很多情况下只有一个。指定初始化函数是初始化过程的"漏斗"位置，经过它来进入父类的初始化链。

每个类至少有一个指定初始化函数。有些时候，这个是从父类继承了一个或多个指定初始化函数。

便利初始化函数是辅助的初始化函数。我们可以定一个便利初始化函数来调用同一个类的指定初始化函数，并给它一些属性的默认值。我们也可以定义便利初始化函数来给一个实例某些特殊的值。

如果我们不需要，我们可以不给出任何的便利初始化函数。当我们需要时，我们定义便利初始化函数来明确和简化我们的代码。

######  语法

指定初始化函数和值类型的普通初始化函数语法一样

``` swift
init( [parameters]){
    [statements]
}
```

便利初始化函数写法基本一致，但是需要用 convenience 来标示出来。

``` swift
convenience init([parameters]) {
    [statements]
}
```

注：  []并不是数组。

######  类类型的初始化代理

为了简化指定初始化函数和便利初始化函数之间的关系，Swift使用下面3条规则。

1.  一个指定的初始化函数必须调用它的直接父类的一个指定初始化函数。
2.  一个便利的初始化函数必须调用它自己同一个类的另一个初始化函数。
3.  一个便利的初始化函数必须最终调用一个指定的初始化函数。

简单来讲就是：

*   指定的初始化函数需要向上代理
*   便利的初始化函数必须横向代理

注： 有两张图片比较清楚对这一概念给出例子。

######  两步初始化

在Swift的类初始化里，有两步，第一步，每一个由定义这个属性类给出的存储属性被初始化。第二步，每一个类都可以进一步的修改这些属性来使得实例可用。

这种方式可以确保初始化安全，并且仍然提供了在类继承里完整的灵活度。两步初始化防止了某些属性在被初始化之前就被使用了，也防止了某个属性被其他的初始化函数修改。

    注意
    Swift的两步初始化和ObjC的很相似，主要区别在于第一步里，ObjC会把0或nil附值给每个属性，而Swift更灵活的可以把自定义的初始值附值给属性。

Swift编译器会做4种检查来确保两步初始化没有错误。

1.  一个指定的初始化函数必须确保在调用父类初始化之前，自己的所有属性都被初始化。
2.  一个指定的初始化函数在给继承的属性附值之前，必须调用父类的初始化函数。
3.  一个便利初始化函数必须在给属性附值之前调用另外的初始化函数，如果不这样，这个属性的值会被覆盖。
4.  一个初始化函数在第一步完成之前，不能调用任何的实例方法，不能读取任何属性，也不能把self作为值来引用。

一个实例在第一步结束之前都不算完整初始化，而方法，属性只有在第一步结束后才能访问。

下面就是两步初始化如何进行的：

第一步：

*   一个指定的或便利的初始化函数被调用。
*   这个实例的内存被分配，但没有初始化。
*   一个指定的初始化确保所有的由当前类引进的属性被初始化。
*   这个指定的初始化函数向上代理调用父类的初始化函数，让父类进行初始化。
*   这一步骤一直进行直到没有父类。
*   直到继承链的顶端，最后的父类把自己的属性都初始化完毕之后，那么这个实例的内存就被初始化完毕。第一阶段结束。

第二步：

*   从继承链顶端往下，每个指定的初始化函数可以自定义属性的值。这时候，初始化函数可以访问 self，也可以修改属性值，调用方法等等。
*   最终，任何的便利方法可以修改属性的值以及使用self。

######  初始化函数继承和重写

和ObjC不同，Swift子类默认不继承他们父类的初始化函数。这样可以避免，如果子类从父类继承了一个简单的初始化函数，而是得子类的实例没有完全初始化。

    注意
    父类的初始化函数只在特定情况下被继承了，需要确保安全。

如果你想给一个子类加一个和父类一样的初始化函数，你可以在子类重新给出实现。

如果你在子类写了一个和父类的指定的初始化函数一样的初始化函数，你实际上是重写了那个初始化函数，所以你必须加上 override 。即便你是重写了一个自动提供的默认初始化函数，也要写override。

和重写属性，方法，角标一样，override会使得编译器去检查是否和父类相匹配。

    注意
    你应该在重写父类的指定的初始化函数时总是写上 override，即使你子类的实现是一个便利的初始化函数。

相反的，如果你重写的初始化函数和父类的一个便利初始化函数想匹配，那父类的便利初始化函数就永远不能在你的子类里被直接调用。因此，子类其实严格来说并没有重写父类的初始化函数，所以你可以不写 override。

``` swift
class Vehicle {
    var numberOfWheels = 0
    var description: String {
        return "\(numberOfWheels) wheel(s)"
    }
}
```

上面的例子，Vehicle给出了属性的默认值，也没有自定义初始化函数，也么有父类，那么它由一个默认的初始化函数。

``` swift
let vehicle = Vehicle()
println("Vehicle: \(vehicle.description)")
// Vehicle: 0 wheel(s)
```

我们定义一个子类

``` swift
class Bicycle: Vehicle {
    override init() {
        super.init()
        numberOfWheels = 2
    }
}
```

Bicycle定义了一个指定的初始化函数，这个初始化函数和父类的一个指定的初始化函数匹配，所以Bicycle的初始化函数被标记为 override。

Bicycle的init，调用了父类的init。然后修改了numberOfWheels的值。

    注意
    子类可以在初始化时修改继承属性的值，但是只能修改变量属性，而不能修改常量属性。

######  自动的初始化函数继承

如上所述，子类默认不能从父类继承初始化函数。然而，如果特定情况满足，父类的初始化函数自动被继承。在实际使用中，这意味着你不需要大量的重写父类的初始化函数，而是可以从父类中安全的继承初始化函数。

假设我们在子类里给新加的属性提供默认值，下面两条规则会使用

1.  如果子类没有定义任何的制定的初始化函数，它会自动从父类继承所有的指定的初始化函数。
2.  如果子类提供了父类的所有的指定的初始化函数的实现，不管是通过 1 还是自己给出实现，它都会自动继承所有的便利初始化函数。

这2条规则，即使子类有自定义的便利初始化函数也一样适用。

    注意
    一个子类可以把一个父类的指定初始化函数实现为一个子类的一个便利初始化函数来满足规则2.

######  指定初始化函数和便利初始化函数实战

下面的例子给出指定初始化函数，便利初始化函数，自动继承的初始化函数。

基类：

``` swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }
    convenience init() {
        self.init(name: "[Unnamed]")
    }
}
```

Food定义了一个属性name。一个指定的初始化函数，一个便利的初始化函数，没有默认的初始化函数。

子类：

``` swift
class RecipeIngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
        self.quantity = quantity
        super.init(name: name)
    }
    override convenience init(name: String) {
        self.init(name: name, quantity: 1)
    }
}
```

RecipeIngredient定义了一个自己的指定的初始化函数，接受name和quantity参数。自己先把自己的quantity初始化，然后调用super来初始化父类。

RecipeIngredient它还定义了一个便利的初始化函数，接受name参数，因为这个初始化函数和父类的一个指定初始化函数相匹配，所以需要加上 override关键字。

尽管RecipeIngredient把父类的一个指定初始化函数重写为便利初始化函数，它也是实现了所有父类的指定初始化函数，所以它还继承了父类的所有便利初始化函数。因为RecipeIngredient的父类是Food，只有一个便利初始化函数，如果我们在RecipeIngredient调用这个初始化函数，会代理到RecipeIngredient，而不是Food。

``` swift
let oneMysteryItem = RecipeIngredient()
let oneBacon = RecipeIngredient(name: "Bacon")
let sixEggs = RecipeIngredient(name: "Eggs", quantity: 6)
```

第三个类：

``` swift
class ShoppingListItem: RecipeIngredient {
    var purchased = false
    var description: String {
        var output = "\(quantity) x \(name)"
        output += purchased ? " ✔" : " ✘"
        return output
    }
}
```

    注意
    ShoppingListItem没有提供初始化函数来初始化purchased，因为东西从开始都是未购买的。

因为ShoppingListItem的属性都有默认值而且没有定义任何的初始化函数，那么它继承了所有的指定初始化函数和便利初始化函数。

``` swift
var breakfastList = [
    ShoppingListItem(),
    ShoppingListItem(name: "Bacon"),
    ShoppingListItem(name: "Eggs", quantity: 6),
]
breakfastList[0].name = "Orange juice"
breakfastList[0].purchased = true
for item in breakfastList {
    println(item.description)
}
// 1 x Orange juice ✔
// 1 x Bacon ✘
// 6 x Eggs ✘
```

#####   可失败的初始化函数

有些时候，定义一个可失败的初始化函数是有意义的。这个失败可能是因为参数错误，或是缺少需要的参数，或者其他阻止初始化过程的情况。

我们在init后面加一个 ？ 来标示这个初始化函数可能失败。

    注意
    你不能定义一个可失败的和不可失败的初始化函数，接受相同的参数类型和名字。

可失败的初始化函数创建一个optioanl的值。如果我们在可失败的初始化函数里写了 return nil，就意味着初始化失败了。

    注意
    严格来讲，初始化函数不返回值。相反，他们的目的是确保 self 在初始化结束后是完整并正确可用的。 所以虽然我们使用 return nil 来表示初始化失败，但是不用在成功初始化时使用 return。

``` swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty { return nil }
        self.species = species
    }
}
let someCreature = Animal(species: "Giraffe")
// someCreature is of type Animal?, not Animal
if let giraffe = someCreature {
    println("An animal was initialized with a species of \(giraffe.species)")
}
// prints "An animal was initialized with a species of Giraffe"
let anonymousCreature = Animal(species: "")
// anonymousCreature is of type Animal?, not Animal
if anonymousCreature == nil {
    println("The anonymous creature could not be initialized")
}
// prints "The anonymous creature could not be initialized"
```

    注意
    检查空字符串和检查 nil 是不一样的。


######  枚举的可失败初始化函数

我们可以使用可失败的初始化函数根据参数来从枚举成员里选择适当值。如果没有合适的值，我们就失败了。

``` swift
enum TemperatureUnit {
    case Kelvin, Celsius, Fahrenheit
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .Kelvin
        case "C":
            self = .Celsius
        case "F":
            self = .Fahrenheit
        default:
            return nil
        }
    }
}
let fahrenheitUnit = TemperatureUnit(symbol: "F")
if fahrenheitUnit != nil {
    println("This is a defined temperature unit, so initialization succeeded.")
}
// prints "This is a defined temperature unit, so initialization succeeded."
let unknownUnit = TemperatureUnit(symbol: "X")
if unknownUnit == nil {
    println("This is not a defined temperature unit, so initialization failed.")
}
// prints "This is not a defined temperature unit, so initialization failed."
```

######  有原始值的枚举的可失败初始化函数

有原始值的枚举自动的有一个可失败的初始化函数，init?(rawValue:)。

``` swift
enum TemperatureUnit: Character {
    case Kelvin = "K", Celsius = "C", Fahrenheit = "F"
}
let fahrenheitUnit = TemperatureUnit(rawValue: "F")
if fahrenheitUnit != nil {
    println("This is a defined temperature unit, so initialization succeeded.")
}
// prints "This is a defined temperature unit, so initialization succeeded."
let unknownUnit = TemperatureUnit(rawValue: "X")
if unknownUnit == nil {
    println("This is not a defined temperature unit, so initialization failed.")
}
// prints "This is not a defined temperature unit, so initialization failed."
```

######  类的可失败初始化函数

一个值类型的可失败的初始化函数可以在初始化的任何阶段失败。

然而对于类，一个可失败的初始化函数，只能在所有的由当前类引进的存储属性都被初始化之后，并且任何的初始化代理开始后才能触发失败。

``` swift
class Product {
    let name: String!
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}
```

上面的例子，name属性是个optional的，所以有初始值 nil。是满足我们的要求的。

因为name是常量属性，所以我们一旦初始化成功，我们就可以确保name始终有值，可以不用去再检查。

``` swift
if let bowTie = Product(name: "bow tie") {
    // no need to check if bowTie.name == nil
    println("The product's name is \(bowTie.name)")
}
// prints "The product's name is bow tie"
```

######  初始化失败的传递

一个可失败的初始化函数可以代理到同一个类的其他的可失败的初始化函数，对于类，也可以代理到父类的可失败的初始化函数。

不论如何代理，如果一个可失败的初始化函数失败，那么整个初始化过程失败。

    注意
    可失败的初始化函数也可以代理到一个不可失败的初始化函数。如果你需要在一个已有的初始化过程中添加一个潜在的失败的初始化函数，可以这种方式。

``` swift
class CartItem: Product {
    let quantity: Int!
    init?(name: String, quantity: Int) {
        super.init(name: name)
        if quantity < 1 { return nil }
        self.quantity = quantity
    }
}
```

上面的例子中，quantity是optional的，初始值是nil。我们先调用了super.init(name: name) 来满足可失败的初始化函数的要求。如果name是空的，那么super.init就会失败，整个初始化就失败了。

``` swift
if let twoSocks = CartItem(name: "sock", quantity: 2) {
    println("Item: \(twoSocks.name), quantity: \(twoSocks.quantity)")
}
// prints "Item: sock, quantity: 2"
if let zeroShirts = CartItem(name: "shirt", quantity: 0) {
    println("Item: \(zeroShirts.name), quantity: \(zeroShirts.quantity)")
} else {
    println("Unable to initialize zero shirts")
}
// prints "Unable to initialize zero shirts"
if let oneUnnamed = CartItem(name: "", quantity: 1) {
    println("Item: \(oneUnnamed.name), quantity: \(oneUnnamed.quantity)")
} else {
    println("Unable to initialize one unnamed product")
}
// prints "Unable to initialize one unnamed product"
```

######  重写可失败的初始化函数

我们可以在子类里重写可失败的初始化函数，我们甚至可以在子类里把可失败的初始化函数重写为不可失败的初始化函数。

如果我们在子类里把一个父类的可失败的初始化函数重写为一个不可失败的初始化函数，那么我们就不能向上代理这个初始化函数。一个不可失败的初始化函数永远都不能代理到一个可失败的初始化函数。

    注意
    我们可以把一个可失败的重写为一个不可失败的，相反则不行。

``` swift
class Document {
    var name: String?
    // this initializer creates a document with a nil name value
    init() {}
    // this initializer creates a document with a non-empty name value
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}
```
Document的名字可以是nil，但是不能为空字符串""。

``` swift
class AutomaticallyNamedDocument: Document {
    override init() {
        super.init()
        self.name = "[Untitled]"
    }
    override init(name: String) {
        super.init()
        if name.isEmpty {
            self.name = "[Untitled]"
        } else {
            self.name = name
        }
    }
}
```

AutomaticallyNamedDocument重写了可失败的init，但是因为AutomaticallyNamedDocument是自动提供名字的，所以它不会失败。

######  init! 可失败初始化函数

因为 init? 初始化一个 optional的值。

我们也可以使用 init! 来初始化一个自动解包的otpional的值。

我们可以在init? 和 init! 之前互相代理，也可以互相重写。我们也可以从 init 代理到 init!，但是这样做，如果init! 失败就会触发一个断言。

#####   必须的初始化函数

在一个类的初始化函数前面加上 required 来表示任何的子类都必须实现这个初始化函数。

``` swift
class SomeClass {
    required init() {
        // initializer implementation goes here
    }
}
```

在子类里，你也必须加上 required 关键字，但是不需要写 override。

``` swift
class SomeSubclass: SomeClass {
    required init() {
        // subclass implementation of the required initializer goes here
    }
}
```

    注意
    你需要显式的实现 required的初始化函数，如果你可以通过一个继承的初始化函数来实现。

#####   通过闭包或函数来设置默认的属性值

如果一个存储属性的默认值需要一些自定义或是配置，我们可以使用闭包或全局函数来提供默认值。每当一个实例被创建，这个闭包或函数就被调用，返回值会设置给对应的属性。

这种类型的闭包和函数一般创建一个和属性类型一致的变量，然后返回这个变量。

``` swift
class SomeClass {
    let someProperty: SomeType = {
        // create a default value for someProperty inside this closure
        // someValue must be of the same type as SomeType
        return someValue
        }()
}
```

注意大括号后面的括弧，这是告诉Swift来执行这个闭包，如果不写括弧，其实是把闭包附值给了属性，而不是闭包执行的结果。

    注意
    如果使用闭包来初始化属性，记得实例的其他的部分还没有被初始化，所以不能在闭包里使用其他属性，self也不能用，还不能调用实例方法。

``` swift
struct Checkerboard {
    let boardColors: [Bool] = {
        var temporaryBoard = [Bool]()
        var isBlack = false
        for i in 1...10 {
            for j in 1...10 {
                temporaryBoard.append(isBlack)
                isBlack = !isBlack
            }
            isBlack = !isBlack
        }
        return temporaryBoard
        }()
    func squareIsBlackAtRow(row: Int, column: Int) -> Bool {
        return boardColors[(row * 10) + column]
    }
}
```

每当一个Checkerboard创建时，boardColors对应的闭包都会执行。































