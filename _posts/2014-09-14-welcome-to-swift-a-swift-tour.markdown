---
layout: post
title: "Welcome to Swift - A Swift Tour"
date: 2014-09-14 18:29:00 +0800
comments: true
categories: [Swift]
---

传统来说，我们学习一门语言的第一个程序将是HelloWorld。在Swift里，非常简单：


{% highlight swift %}
println("Hello,World")
{% endhighlight %}

如果你之前写过C或者Objc的代码，这个语法你应该不陌生，在Swift里，这行代码就是一个完整的程序，你不需要导入其他库来使用输入输出或者字符串处理。写在全局作用域的代码被用来作为程序的入口，所以你不需要一个main函数。你也不用在每一个语句后面写上分号。

这里我们将通过许多的例子来给你足够的知识让你能够开始用Swift写代码。遇到不能理解的地方也不要紧，这里的所有内容在这本书的剩余部分都会详细介绍。

<!--more-->

####    Simple Values （基础值）

用let来声明常量，var声明变量。常量的值不需要在编译期知道，但是你只能赋值一次，这意味着你用常量来定义一次，然后在很多地方使用。


{% highlight swift %}
var myVariable = 42
myVariable = 48
let myConstant = 42
{% endhighlight %}

常量和变量必须具有和你赋值给他们的值相同的类型，但是你并不需要总是明确的写出他们的类型。你只管提供值，让编译器决定类型。在上面的例子里，myVaribale将是整数类型，因为它的初始化值是整数。

如果一个初始化值不足以提供类型信息，那你就必须提供类型，写在变量的后面，用冒号隔开。


{% highlight swift %}
let implictInteger = 70
let implictDouble = 70.0
let explictDouble : Double = 70
{% endhighlight %}

值永远都不会隐式转换为其他类型。如果你需要转换一个值到另外一个类型，你需要显式转化。


{% highlight swift %}
let label = "The width is "
let width = 94
let widthLabel = label + String(width)
{% endhighlight %}

其实有更简单的办法在字符串中插入值，把值写在括号里，在括号前面写一个 \ 。


{% highlight swift %}
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples"
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
{% endhighlight %}

使用[ ]来创建数组和字典，在[ ]里写上索引或键来访问数字和字典里的元素。


{% highlight swift %}
var shoppingList = ["catfish", "water", "tulips", "blue paint"]
shoppingList[1] = "bottle of water"
var occupations = [
    "Malcolm" : "Captain" ,
    "Kaylee"  : "Mechanic",
]
occupations["Jayne"] = "Public Relations"
{% endhighlight %}


创建空的数字和字典，用初始化语法：


{% highlight swift %}
let emptyArray = [String]()
let emptyDictionary = [String:Float]()
{% endhighlight %}

####    Control Flow（控制流程）

用 if 和 switch 来做条件分支，用 for-in，for，while，do-while 来做循环。在条件分支和循环里的括号是可选的，但是包含代码的大括号是必须的。


{% highlight swift %}
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in individualScores {
    if score > 50 {
        teamScore += 3
    }else{
        teamScore += 1
    }
}
{% endhighlight %}


在 if 语句里，条件值必须是布尔表达式，这意味着 if score {} 是错误的，这里不会隐式的和 0 比较。


你可以用 if 和 let 一起处理那些可能不存在的值。这些值被表达为Optional，一个Optional的值要么包含一个值，要么包含 nil 来表示值不存在。 在值的类型后面加个 ？表示Optional。


{% highlight swift %}
var optionalString : String? = "Hello"
optionalString == nil
var optionalName : String? = "John"
var greeting = "Hello"
if let name = optionalName {
    greeting = "Hello,\(name)"
}
{% endhighlight %}

如果Optional的值为 nil，条件就是 false，大括号里的代码就被略过了，否则，Optional的值被解包(Unwrap)并赋值给了在 let 后面的常量，这样这个常量在下面的代码快里也是可见的。

switch 支持任何种类的数据和多种多样的比较奥做，它们不局限于整数和相等性比较。


{% highlight swift %}
let vegetable = "red pepper"
switch vegetable {
    case "celery":
        let vegerableComent = "Add some raisins and make ants on a log."
    case "cucumber", "watercress":
        let vegerableComment = "That would make a good tea sandwich."
    case let x where x.hasSuffix("pepper"):
        let vegetableComment = "Is it a spicy \(x) ?"
    default:
        let vegetableComment = "Everything tastes good in soup."
}
{% endhighlight %}

注意如何在一个模式中使用 let 来把匹配的值赋值给一个常量。

在执行完匹配的case里的代码，程序就离开了switch语句。代码不会继续执行下一个case，所以不需要显示的从每一个case里 break 出来。

你可以用 for-in 来遍历一个字典里的内容，你需要提供一对名字来匹配每一个键值对。字典是无序集合，所以键值对的顺序是任意的。


{% highlight swift %}
let interestingNumbers = [
    "Prime" : [2,3,5,7,11,13],
    "Fibonacci" : [1,1,2,3,5,8],
    "Square" : [1,4,9,16,25],
]
var largest = 0
for (kind,numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
     }
 }
{% endhighlight %}
 
 
 使用 while 来重复一段代码直到条件改变。条件也可以放在尾部，保证代码至少执行一次。
 
 
{% highlight swift %}
 var n = 2
 while n < 100 {
     n = n * 2
 }
 n
 var m = 2
 do{
     m = m * 2
 } while m < 100
 m
{% endhighlight %}
 
 你可以在循环中持有一个索引，可以通过 ..< 来创建一个区间， 或是显式的初始化，条件，增加。 下面两个循环做的事情是一样的


{% highlight swift %}
var firstLoop = 0
for i in 0..<4 {
    firstLoop += i
}
firstLoop
var secondLoop = 0
for var i = 0; i < 4; ++i {
    secondLoop += i
}
secondLoop
{% endhighlight %}

..< 不包含最大值，半开区间[ ), ...包含最大值，闭区间 [ ]。



####    Functions and Closures (函数与闭包)

用 func 来声明一个函数，在函数名后面加上括弧，在括弧里传入参数来调用函数。用 -> 区分参数名字和函数的返回类型。


{% highlight swift %}
func greet(name: String, day: String) -> String {
    return "Hello \(name), today is \(day)"
}
greet("Bob","Tuesday")
{% endhighlight %}

使用元组来表示一个组合值，比如从一个函数返回多个值。元组的元素可以用过名字或数字来引用。


{% highlight swift %}
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0
    for score in scores {
        if score > max {
            max = score
        else if score < min {
            min = score
        }
        sum += score
     }
     return (min, max, sum)
}
let statistics = calculateStatistics([5,3,100,3,9])
statistics.sum
statistics.2
{% endhighlight %}

函数也可以接受不定数量的参数，会收集在一个数组里。


{% highlight swift %}
func sumOf(numbers : Int...) -> Int {
    var sum = 0
    for number in numbers {
        sum += number
    }
    return sum
}
sumOf()
sumOf(42,597,12)
{% endhighlight %}


函数可以嵌套，被嵌套的函数可以访问外层函数里的变量。可以使用嵌套函数来组织比较复杂或冗长的代码。


{% highlight swift %}
func returnFifteen() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
returnFifteen()
{% endhighlight %}

函数是一等类型。这意味着函数可以作为其他函数的返回值。


{% highlight swift %}
func makeIncrementer() -> (Int -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}
var increment = makeIncrementer()
increment(7)
{% endhighlight %}

函数也可以接受其他函数作为参数。


{% highlight swift %}
func hasAnyMatches(list: [Int], condition: Int -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20,19,7,12]
hasAnyMatches(numbers, lessThanTen)
{% endhighlight %}

函数其实是一种特殊的闭包（一些代码可以在稍后被调用）。闭包里的代码可以访问那些在闭包被创建的作用域里可以见的变量和函数，即使闭包在另外的作用域被执行（嵌套函数的那个例子里就是这样）。你可以写一个没有名字的闭包，把代码直接包含在大括号里，使用 in 把参数，返回值和函数体区分开来。


{% highlight swift %}
numbers.map({
    (number: Int) -> Int in
    let result = 3 * number
    return result
})
{% endhighlight %}

你还有一些选项可以把闭包写的更精确。当一个闭包的类型是已知的，例如delegate的回调，你可以省略闭包的参数的类型，或者它的返回值，或者两个都省略。单个语句的闭包隐式的返回他们的唯一的语句的值。


{% highlight swift %}
let mappedNumbers = numbers.map({ number in 3 * number })
mappedNumbers
{% endhighlight %}

你可以在闭包里通过数字引用参数而不是名字（这种方式在非常短的闭包里非常有用）。一个闭包如果作为一个函数的最后一个参数，可以把闭包的代码写在函数调用的括弧后面。


{% highlight swift %}
let sortedNumber = sorted(numbers) { $0 > $1 }
sortedNumber
{% endhighlight %}

####    Objects and Classes (对象与类)

使用 class 加上类名来创建一个类。 在类里声明属性和声明一个常量，变量一样，只是在类的上下文里。同样，方法和函数的声明也一样的。


{% highlight swift %}
class Shape {
    var numberOfSides = 0
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides"
    }
}
{% endhighlight %}

在类名后面加上括弧来创建实例。用 . 来访问属性和方法。


{% highlight swift %}
var shape = Shape()
shape.numberOfSides = 7
var shapeDescription = shape.simpleDescription()
{% endhighlight %}

这个版本的Shape类缺少一个重要的东西，一个在实例被创建时的初始化函数。用 init 来创建一个。


{% highlight swift %}
class NamedShape {
    var numberOfSides = 0
    var name : String
    init(name:String){
        self.name = name
    }
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides"
    }
}
{% endhighlight %}

注意在初始化函数里，如何用 self 来区分属性和参数。 参数在创建一个类的实例像函数调用一样传递给了初始化函数。每一个属性都需要赋值，不管是在声明的时候还是在初始化函数里。

使用 deinit 来创建一个析构函数(deinitializer)在对象被deallocated的时候做一些清理工作。

子类在自己的类名后面标注它们的父类，用冒号隔开。Swift并没有一个强制要求的标准基类，你可以包含一个基类，也可以省略。

子类里重写父类的实现的方法需要标记为 override， 如果没有写 override 却重写了父类的方法，编译器会报错。如果写了 override 但并不是真的重写了一个方法，编译器也会报错。


{% highlight swift %}
class Square: NamedShape {
    var sideLength: Double
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
    }
    func area() ->  Double {
        return sideLength * sideLength
    }
    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)."
    }
}
let test = Square(sideLength: 5.2, name: "my test square")
test.area()
test.simpleDescription()
{% endhighlight %}

除了简单的被存储属性，属性还可以有 getter 和 setter。


{% highlight swift %}
class EquilateralTriangle: NamedShape {
    var sideLength: Double = 0.0
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 3
    }
    var perimeter: Double {
        get {
            return 3.0 * sideLength
        }
        set {
            sideLength = newValue / 3.0
        }
    }
    override func simpleDescription() -> String {
        return "An equilateral triangle with sides of length \(sideLength)."
    }
}
var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
triangle.perimeter
triangle.perimeter = 9.9
triangle.sideLength
{% endhighlight %}

在 perimeter 的 setter里，新的值默认的名字叫 newValue，你也可以显式的在set后面加上名字。

注意在 EquilateralTriangle 的 init有三个不同的步骤：

1.    设置子类声明的属性。
2.    调用父类的初始化函数。
3.    修改父类定义的属性，还有任何其他的初始化工作都可以在这一时刻进行。

如果你不需要计算一个属性，但是在它被改变之前和之后需要执行代码的话，可以使用 willSet 和 didSet。


{% highlight swift %}
class TriangleAndSquare {
    var triangle: EquilateralTriangle {
        willSet {
            square.sideLength = newValue.sideLength
        }
    }
    var square: Square {
        willSet {
            triangle.sideLength = newValue.sideLength
        }
    }
    init(size: Double, name: String) {
        square = Square(sideLength: size, name: name)
        triangle = EquilateralTriangle(sideLength: size, name: name)
    }
}
var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
triangleAndSquare.square.sideLength
triangleAndSquare.triangle.sideLength
triangleAndSquare.square = Square(sideLength: 50, name: "larger square")
triangleAndSquare.triangle.sideLength
{% endhighlight %}

类里的方法和函数有一个重要的区别。函数的参数名只用在函数里面，但是方法的参数名在调用方法的时候也需要用到(除了第一个)。默认情况下，一个方法在调用时和方法内部时用同一个参数名字，但是你也可以给出第二个名字，用在方法内部。


{% highlight swift %}
class Counter {
    var count: Int = 0
    func incrementBy(amount: Int, numberOfTimes times: Int) {
        count += amount * times
    }
}
var counter = Counter()
counter.incrementBy(2, numberOfTimes: 7)
{% endhighlight %}

当使用Optional时，你可以在方法，属性和下标的操作之前加上 ? 。如果在 ？ 之前的值是 nil， 所有在 ？ 之后的都被忽略并且整个表达式的值也是 nil。 否则，Optional的值被解包， ？ 之后的所有都处理在这个解包后的值。在这两种情况下，整个表达式的值都是Optional。


{% highlight swift %}
let optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
let sideLength = optionalSquare?.sideLength
{% endhighlight %}
####    Enumerations and Structures (枚举与结构)

使用 enum 来创建枚举，像 class 和其他所有有名类型一样，枚举也可以有方法。


{% highlight swift %}
enum Rank: Int {
    case Ace = 1
    case Two, Three, Four, Five, Six, Seven, Eight, Nine, Ten
    case Jack, Queen, King
    func simpleDescription() -> String {
        switch self {
        case .Ace:
            return "ace"
        case .Jack:
            return "jack"
        case .Queen:
            return "queen"
        case .King:
            return "king"
        default:
            return String(self.toRaw())
        }
    }
}
let ace = Rank.Ace
let aceRawValue = ace.toRaw()
{% endhighlight %}

在上面的例子里，枚举的原始类型(raw type)是 Int，所以你只需要给出第一个枚举的值，其他的会自动赋值。你可以使用字符串和浮点数来作为枚举的原始类型。

使用 toRaw 和 fromRaw 来在原始值和枚举值之前转化。


{% highlight swift %}
if let convertedRank = Rank.fromRaw(3) {
    let threeDescription = convertedRank.simpleDescription()
}
{% endhighlight %}

枚举的成员值是实际的值，而不是原始值的另外一种写法，事实上，如果没有合适的原始值，你可以不提共。


{% highlight swift %}
enum Suit {
    case Spades, Hearts, Diamonds, Clubs
    func simpleDescription() -> String {
        switch self {
        case .Spades:
            return "spades"
        case .Hearts:
            return "hearts"
        case .Diamonds:
            return "diamonds"
        case .Clubs:
            return "clubs"
        }
    }
}
let hearts = Suit.Hearts
let heartsDescription = hearts.simpleDescription()
{% endhighlight %}

注意 Hearts 被引用的时侯的两种方式，当赋值的时候， Suit.Hearts 全名被使用，因为不知道常量的类型；在 switch里，因为 self 的类型已知，所以使用了 .Hearts 简短的名字。你可以在任何类型已知的情况下使用简短的名字。

使用 struct 来创建一个结构体。结构体和类有很多一样的行为，包括方法和初始化方法。它们两个最重要的区别就是结构体在你代码里来回传递的时候总是被拷贝的，而类则是传递引用（reference）。


{% highlight swift %}
struct Card {
    var rank: Rank
    var suit: Suit
    func simpleDescription() -> String {
        return "The \(rank.simpleDescription()) of \(suit.simpleDescription())"
    }
}
let threeOfSpades = Card(rank: .Three, suit: .Spades)
let threeOfSpadesDescription = threeOfSpades.simpleDescription()
{% endhighlight %}

一个枚举实例的枚举成员可以有这个实例的关联值（assocaited value），同一个枚举成员的不同实例可以有不同的关联值。你在创建这个实例的时候提供关联值。关联值和原始值是不一样的，一个枚举成员的原始值对于所有的实例都一样，而且你是在定义枚举类型的时候提供原始值。

例如，向一个服务器请求日出和日落时间，服务器要么返回值，要么返回错误。


{% highlight swift %}
enum ServerResponse {
    case Result(String, String)
    case Error(String)
}
let success = ServerResponse.Result("6:00 am", "8:09 pm")
let failure = ServerResponse.Error("Out of cheese.")
switch success {
case let .Result(sunrise, sunset):
    let serverResponse = "Sunrise is at \(sunrise) and sunset is at \(sunset)."
case let .Error(error):
    let serverResponse = "Failure...  \(error)"
}
{% endhighlight %}

注意，日出和日落的值是如何从 ServerResponse 里匹配出来的。

####    Protocol and Extensions (协议和扩展)

使用 protocol 来声明一个协议。


{% highlight swift %}
protocol ExampleProtocol {
    var simpleDescription: String { get }
    mutating func adjust()
}
{% endhighlight %}

类，枚举和结构体都可以实现协议。


{% highlight swift %}
class SimpleClass: ExampleProtocol {
    var simpleDescription: String = "A very simple class."
    var anotherProperty: Int = 69105
    func adjust() {
        simpleDescription += "  Now 100% adjusted."
    }
}
var a = SimpleClass()
a.adjust()
let aDescription = a.simpleDescription
struct SimpleStructure: ExampleProtocol {
    var simpleDescription: String = "A simple structure"
    mutating func adjust() {
        simpleDescription += " (adjusted)"
    }
}
var b = SimpleStructure()
b.adjust()
let bDescription = b.simpleDescription
{% endhighlight %}

注意例子中使用 mutating 关键字来标识方法会修改结构体。类的方法不用标识为 mutating，因为类的方法总是可以修改这个类。

使用 extension 来给现有的类型增加功能，例如方法和计算的属性。你可以使用 extension 来给一个类型增加协议的实现。


{% highlight swift %}
extension Int: ExampleProtocol {
    var simpleDescription: String {
        return "The number \(self)"
    }
    mutating func adjust() {
        self += 42
    }
}
7.simpleDescription
{% endhighlight %}


你可以把协议名字当作任何其他有名字的类型来用，例如，创建一个对象的集合，对象有着不同的类型，但是都实现了同一个协议。当你使用类型为协议类型的值时，只有协议定义的方法是可以用的。


{% highlight swift %}
let protocolValue: ExampleProtocol = a
protocolValue.simpleDescription
// protocolValue.anotherProperty  // Uncomment to see the error
{% endhighlight %}

尽管 protocolValue 在运行时是 SimpleClass类别的，编译器认为它就是 ExampleProtocol类型。这意味着你不能偶然的访问到类和协议都有的方法和属性(not sure.)

####    Generics (泛型)

把名字写到尖括号里来创建一个泛型函数或者类型。


{% highlight swift %}
func repeat<ItemType>(item: ItemType, times: Int) -> [ItemType] {
    var result = [ItemType]()
    for i in 0..<times {
        result.append(item)
    }
    return result
}
repeat("knock", 4)
{% endhighlight %}

你可以定义泛型的函数，方法，也可以定义泛型的类，枚举，结构体。


{% highlight swift %}
enum OptionalValue<T> { // Reimplement the Swift standard library's optional type
    case None
    case Some(T)
}
var possibleInteger: OptionalValue<Int> = .None
possibleInteger = .Some(100)
{% endhighlight %}

在类型名字后面使用 where 来给出一系列要求，例如，要求这个类型必须实现某个协议，要求两个类型必须一样，或者要求需要某一个特定的父类。


{% highlight swift %}
func anyCommonElements <T, U where T: SequenceType, U: SequenceType, T.Generator.Element: Equatable, T.Generator.Element == U.Generator.Element> (lhs: T, rhs: U) -> Bool {
    for lhsItem in lhs {
        for rhsItem in rhs {
            if lhsItem == rhsItem {
                return true
            }
        }
    }
    return false
}
anyCommonElements([1, 2, 3], [3])
{% endhighlight %}

在上面的例子里，你可以省略 where 直接把协议或者类名写在一个冒号后面。 写 <T: Equatable> 和 <T where T : Equatable> 是一样的。







