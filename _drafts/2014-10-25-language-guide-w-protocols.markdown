---
layout: post
title: "Language Guide: Protocols"
date: 2014-10-25 10:26:20 +0800
comments: true
categories: [Swift]
---


一个协议定义了一系列满足某些特定需要的方法，属性和其他要求的一个蓝图。协议本身并不提供这些要求的具体实现，它只负责描述这些要求。然后，协议可以被类，结构体，枚举来实现提供这些要求的真正实现。

协议可以要求实现协议的类型有特殊的实例属性，实例方法，类型方法，操作符和角标。

<!-- more -->

### 协议语法

定义协议和定义类，结构体，枚举很相似。

``` swift
protocol SomeProtocol {
    // protocol definition goes here
}
```

当类型实现协议时，把协议写在类型名字后面，由冒号隔开，多个协议之间由逗号分开。

``` swift
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // structure definition goes here
}
```

如果类有父类，要把父类写在协议的前面。

``` swift
class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
    // class definition goes here
}
```

### 属性要求

协议可以要求实现的类型提供实例属性或类属性。协议并不表明是存储属性还是计算属性，它只给出类型和名字，同时说明是只读的还是可读可写的。

如果一个协议规定的属性是可读可写的，那么实现类型不就能通过常量存储属性或只读计算属性来满足。如果协议规定的属性是只读的，那么实现类型可以通过任何属性来实现，就算你在实现时同时给出了setter也是可以的。

属性要求总是写成变量形式的。getter和setter用 set 和 get 标示。

``` swift
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}
```

类型属性总是用class 来标示，即使实现的类型是值类型，具体实现里用的是 static。

``` swift
protocol AnotherProtocol {
    class var someTypeProperty: Int { get set }
}
```

下面的例子:

``` swift
protocol FullyNamed {
    var fullName: String { get }
}
```

只定义了一个属性要求。

``` swift
struct Person: FullyNamed {
    var fullName: String
}
let john = Person(fullName: "John Appleseed")
// john.fullName is "John Appleseed"
```

Person实现了协议，通过一个存储的属性。

``` swift
class Starship: FullyNamed {
    var prefix: String?
    var name: String
    init(name: String, prefix: String? = nil) {
        self.name = name
        self.prefix = prefix
    }
    var fullName: String {
        return (prefix != nil ? prefix! + " " : "") + name
    }
}
var ncc1701 = Starship(name: "Enterprise", prefix: "USS")
// ncc1701.fullName is "USS Enterprise"
```

Starship也实现了这个协议，通过一个计算属性。

### 方法要求

协议可以要求实现类型去实现特定的实例方法和类型方法。这些方法在协议里的定义和他们实现时一模一样，只是没有大括号和函数体。变长参数也是可以的，规则和普通方法一样。

    注意
    协议定义的方法和普通方法语法一样，但是不能允许给出参数默认值。

和类型属性要求一样，在类型方法要求前面添加 class 关键字。即使是值类型去实现这个方法。

``` swift
protocol SomeProtocol {
    class func someTypeMethod()
}
protocol RandomNumberGenerator {
    func random() -> Double
}
```

上面是两个协议，一个定义了一个类型方法，一个定义了一个实例方法。

``` swift
class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c) % m)
        return lastRandom / m
    }
}
let generator = LinearCongruentialGenerator()
println("Here's a random number: \(generator.random())")
// prints "Here's a random number: 0.37464991998171"
println("And another one: \(generator.random())")
// prints "And another one: 0.729023776863283"
```

### 可变方法要求

如果我们定义协议实例方法需要修改实例本身，我们也需要在协议定义中，给方法加上 mutating 关键字。这样使得结构体和枚举可以实现协议。

    注意
    如果我们在协议定义时，给出 mutating，当我们用一个类实现协议时，就不用写 mutating，但是如果是结构体和枚举，是必须要写得。

``` swift
protocol Togglable {
    mutating func toggle()
}
```

我们定义了一个协议，包含一个mutating的方法。

``` swift
enum OnOffSwitch: Togglable {
    case Off, On
    mutating func toggle() {
        switch self {
        case Off:
            self = On
        case On:
            self = Off
        }
    }
}
var lightSwitch = OnOffSwitch.Off
lightSwitch.toggle()
// lightSwitch is now equal to .On
```

我们在枚举OnOffSwitch上实现了协议。

### 初始化函数要求

协议可以定义特殊的初始化函数要求。

``` swift
protocol SomeProtocol {
    init(someParameter: Int)
}
```

####    类实现

我们可以把协议要求的初始化函数实现为指定初始化函数或便利初始化函数，但是都需要加上 required。

``` swift
class SomeClass: SomeProtocol {
    required init(someParameter: Int) {
        // initializer implementation goes here
    }
}
```

使用 required 保证了我们给所有子类提供了显式的或是继承的初始化函数。

    注意
    如果类是 final，则我们可以不写 required的。

如果一个子类重写了一个父类的指定初始化函数，并且实现了一个协议的初始化函数，required 和 override 则都需要写。

``` swift
protocol SomeProtocol {
    init()
}
class SomeSuperClass {
    init() {
        // initializer implementation goes here
    }
}
class SomeSubClass: SomeSuperClass, SomeProtocol {
    // "required" from SomeProtocol conformance; "override" from SomeSuperClass
    required override init() {
        // initializer implementation goes here
    }
}
```

####    可失败的初始化函数要求

协议可以定义可失败的初始化函数要求。

一个可失败的初始化要求可以被一个可失败的或者不可失败的初始化函数实现。一个不可失败的初始化函数可以被一个不可失败的或是一个自动解包的可失败初始化函数实现。

### 协议也是类型

协议不会提供它定义的任何要求的实现。然而，协议却可以作为一个类型来使用。

因为它也是类型，所以我们可以把它用在任何一个类型允许的地方。例如：
    *   作为参数或返回值
    *   作为常量，变量或属性
    *   作为字典，数组或其他集合的元素的类型

    注意
    因为协议是类型，所以首字母应该大写

``` swift
class Dice {
    let sides: Int
    let generator: RandomNumberGenerator
    init(sides: Int, generator: RandomNumberGenerator) {
        self.sides = sides
        self.generator = generator
    }
    func roll() -> Int {
        return Int(generator.random() * Double(sides)) + 1
    }
}
```

我们定义了Dice类，它有一个RandomNumberGenerator的属性。

``` swift
var d6 = Dice(sides: 6, generator: LinearCongruentialGenerator())
for _ in 1...5 {
    println("Random dice roll is \(d6.roll())")
}
// Random dice roll is 3
// Random dice roll is 5
// Random dice roll is 4
// Random dice roll is 5
// Random dice roll is 4
```

### 代理

代理，是一种设计模式，它可以让一个类或结构体把自己的一部分责任代理给其他类型的实例。这个设计模式通过把代理过去的责任封装到一个协议里，然后实现了协议的类型就可以作为代理来提供这部分功能。代理可以用来相应某些特定的时间，或者从不知道底层类型的数据获取数据。

``` swift
protocol DiceGame {
    var dice: Dice { get }
    func play()
}
protocol DiceGameDelegate {
    func gameDidStart(game: DiceGame)
    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
    func gameDidEnd(game: DiceGame)
}
```

我们定义了2个协议，DiceGame和GiceGameDelegate。

``` swift
class SnakesAndLadders: DiceGame {
    let finalSquare = 25
    let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
    var square = 0
    var board: [Int]
    init() {
        board = [Int](count: finalSquare + 1, repeatedValue: 0)
        board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
        board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    }
    var delegate: DiceGameDelegate?
    func play() {
        square = 0
        delegate?.gameDidStart(self)
        gameLoop: while square != finalSquare {
            let diceRoll = dice.roll()
            delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)
            switch square + diceRoll {
            case finalSquare:
                break gameLoop
            case let newSquare where newSquare > finalSquare:
                continue gameLoop
            default:
                square += diceRoll
                square += board[square]
            }
        }
        delegate?.gameDidEnd(self)
    }
}
```

``` swift
class DiceGameTracker: DiceGameDelegate {
    var numberOfTurns = 0
    func gameDidStart(game: DiceGame) {
        numberOfTurns = 0
        if game is SnakesAndLadders {
            println("Started a new game of Snakes and Ladders")
        }
        println("The game is using a \(game.dice.sides)-sided dice")
    }
    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int) {
        ++numberOfTurns
        println("Rolled a \(diceRoll)")
    }
    func gameDidEnd(game: DiceGame) {
        println("The game lasted for \(numberOfTurns) turns")
    }
}
```

``` swift
let tracker = DiceGameTracker()
let game = SnakesAndLadders()
game.delegate = tracker
game.play()
// Started a new game of Snakes and Ladders
// The game is using a 6-sided dice
// Rolled a 3
// Rolled a 5
// Rolled a 4
// Rolled a 5
// The game lasted for 4 turns
```

### 通过扩展来实现协议

我们可以通过扩展一个类型来实现协议，即使我们拿不到这个类型的源代码。扩展可以添加属性，方法，角标，也可以添加协议的实现。

    注意
    已有的实例也会具有新扩展实现的协议。

``` swift
protocol TextRepresentable {
    func asText() -> String
}
```

``` swift
extension Dice: TextRepresentable {
    func asText() -> String {
        return "A \(sides)-sided dice"
    }
}
```

我们扩展Dice来实现了TextRepresentable协议。

``` swift
let d12 = Dice(sides: 12, generator: LinearCongruentialGenerator())
println(d12.asText())
// prints "A 12-sided dice"
```

我们就可以使用asText方法了。

####    通过扩展来声明协议实现

如果一个类型已经实现了协议的所有要求，但是没有说明它实现了协议。我们可以通过一个扩展来说明。

``` swift
struct Hamster {
    var name: String
    func asText() -> String {
        return "A hamster named \(name)"
    }
}
extension Hamster: TextRepresentable {}
```

``` swift
let simonTheHamster = Hamster(name: "Simon")
let somethingTextRepresentable: TextRepresentable = simonTheHamster
println(somethingTextRepresentable.asText())
// prints "A hamster named Simon"
```

这样，Hamster实例可以被当做TextRepresentable类型。

    注意
    类型不是简单的满足协议的要求就算实现协议了，他们必须声明他们实现了协议。

### 协议类型集合

协议可以作为集合元素的类型。

``` swift
let things: [TextRepresentable] = [game, d12, simonTheHamster]
for thing in things {
    println(thing.asText())
}
// A game of Snakes and Ladders with 25 squares
// A 12-sided dice
// A hamster named Simon
```

注意thing的类型是TextRepresentable，而不是具体的其他类型。然而，因为它的类型是TextRepresentable，TextRepresentable能做的任何事情，它们都可以做。

### 协议继承

协议可以从一个或多个协议继承，而且还可以在这些基础上添加新的要求。协议的继承语法和类的继承语法是一样的。

``` swift
protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
    // protocol definition goes here
}
```

``` swift
protocol PrettyTextRepresentable: TextRepresentable {
    func asPrettyText() -> String
}
```

这里我们定义了一个新的协议，继承自TextRepresentable。

``` swift
extension SnakesAndLadders: PrettyTextRepresentable {
    func asPrettyText() -> String {
        var output = asText() + ":\n"
        for index in 1...finalSquare {
            switch board[index] {
            case let ladder where ladder > 0:
                output += "▲ "
            case let snake where snake < 0:
                output += "▼ "
            default:
                output += "○ "
            }
        }
        return output
    }
}
println(game.asPrettyText())
// A game of Snakes and Ladders with 25 squares:
// ○ ○ ▲ ○ ○ ▲ ○ ○ ▲ ▲ ○ ○ ○ ▼ ○ ○ ○ ○ ▼ ○ ○ ▼ ○ ▼ ○
```

### 只给类的协议

我们可以限制实现协议的类型只能是类，而不能是结构体和枚举。通过在协议继承列表的最前面加一个 class 来限制实现协议的类型只能是类。

``` swift
protocol SomeClassOnlyProtocol: class, SomeInheritedProtocol {
    // class-only protocol definition goes here
}
```

    注意
    仅当协议的实现需要区分值和引用的不同语意时使用只给类的协议。

### 协议组合

有时候要求一个类型同时实现多个协议是有用的。我们可以把多个协议组合成一个协议。写成protocol<SomeProtocl, AnotherProtocol>。我们可以在尖括号里写任意多得协议。

``` swift
protocol Named {
    var name: String { get }
}
protocol Aged {
    var age: Int { get }
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}
func wishHappyBirthday(celebrator: protocol<Named, Aged>) {
    println("Happy birthday \(celebrator.name) - you're \(celebrator.age)!")
}
let birthdayPerson = Person(name: "Malcolm", age: 21)
wishHappyBirthday(birthdayPerson)
// prints "Happy birthday Malcolm - you're 21!"
```

上面的例子里，我们定义2个协议，由一个结构体实现2个协议。定义了一个函数接受一个组合协议的类型。

    注意
    协议组合并没有定义一个新的，永久的类型。相反，是临时的定义了一个局部的组合了其他协议的协议。

### 检查协议实现

我们可以使用 is 和 as 操作符来检查协议实现和把实例转换到特定协议类型。检查协议实现和检查类型是一样的。
    *   如果实例实现了协议，is 返回true，如果没有，返回 false。
    *   as？ 返回一个optional的协议类型，如果实例没有实现协议，返回nil。
    *   as 返回一个一个特定的协议类型，如果实例没有实现协议，运行时错误。

``` swift
@objc protocol HasArea {
    var area: Double { get }
}
```

    注意
    如果我们要检查一个协议是否被实例实现，我们必须把协议标记上 @objc。这个标记意味着这个协议可以在ObjC代码中使用。即使你打算在ObjC中使用，你如果想对实例做检查，协议也必须标记为 @objc。
    
    另外，标记为@objc的协议只能被类实现，而不能被结构体和枚举实现。

``` swift
class Circle: HasArea {
    let pi = 3.1415927
    var radius: Double
    var area: Double { return pi * radius * radius }
    init(radius: Double) { self.radius = radius }
}
class Country: HasArea {
    var area: Double
    init(area: Double) { self.area = area }
}
class Animal {
    var legs: Int
    init(legs: Int) { self.legs = legs }
}
let objects: [AnyObject] = [
    Circle(radius: 2.0),
    Country(area: 243_610),
    Animal(legs: 4)
]
for object in objects {
    if let objectWithArea = object as? HasArea {
        println("Area is \(objectWithArea.area)")
    } else {
        println("Something that doesn't have an area")
    }
}
// Area is 12.5663708
// Area is 243610.0
// Something that doesn't have an area
```

### 可选的协议要求

我们可以在协议里定义可选的要求。这些要求可以不被实现的类型实现。可选的要求前面用 optional来标示。

一个可选的协议要求可以使用optional链来调用，来检查一个类型实现了协议，但没有实现可选的要求。

我们在可选的要求后面加？来调用。可选的属性和方法，总会返回一个optional的值。来表示是否成功访问或调用，来反映这个可选的要求是否被实现。

    注意
    可选的协议要求只能在@objc的协议里。

``` swift
@objc protocol CounterDataSource {
    optional func incrementForCount(count: Int) -> Int
    optional var fixedIncrement: Int { get }
}
```

    注意
    严格来说，我们可以定义一个类，实现CounterDataSource，但不给出任何实现。



``` swift
@objc class Counter {
    var count = 0
    var dataSource: CounterDataSource?
    func increment() {
        if let amount = dataSource?.incrementForCount?(count) {
            count += amount
        } else if let amount = dataSource?.fixedIncrement? {
            count += amount
        }
    }
}
```

``` swift
class ThreeSource: CounterDataSource {
    let fixedIncrement = 3
}
var counter = Counter()
counter.dataSource = ThreeSource()
for _ in 1...4 {
    counter.increment()
    println(counter.count)
}
// 3
// 6
// 9
// 12
class TowardsZeroSource: CounterDataSource {
    func incrementForCount(count: Int) -> Int {
        if count == 0 {
            return 0
        } else if count < 0 {
            return 1
        } else {
            return -1
        }
    }
}
counter.count = -4
counter.dataSource = TowardsZeroSource()
for _ in 1...5 {
    counter.increment()
    println(counter.count)
}
// -3
// -2
// -1
// 0
// 0
```





























