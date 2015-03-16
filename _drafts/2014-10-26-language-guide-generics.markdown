---
layout: post
title: "Language Guide: Generics"
date: 2014-10-26 23:25:21 +0800
comments: true
categories: [Swift]
---

泛型可以帮助我们写出灵活的，可重用的函数和类型，他们可以使用任何类型来满足我们的要求。我们可以写出目的明确，抽象清晰的代码。

泛型是Swift最强大的几个特性之一，并且Swift的标准库很多都是用泛型来做的。事实上，我们可能没有意识到，但是我们已经一直在用泛型了。例如，Array和Dictionary类型都是泛型集合。我们可以创建一个元素为Int的数组，也可以创建一个元素为String的数组，或者其他任何Swift的类型。相同的，我们也可以创建不同类型的字典。

<!-- more -->

### 泛型所解决的问题

下面是交换2个Int的一个函数：

``` swift
func swapTwoInts(inout a: Int, inout b: Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

我们使用他们来交换2个整数。

``` swift
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
println("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// prints "someInt is now 107, and anotherInt is now 3"
```

swapTwoInts是可用的，但是他只能交换两个Int，如果我们需要交换两个String，或者Double，我们就需要再写更多的方法，例如swapTwoStrings和swapTwoDoubles。

``` swift
func swapTwoStrings(inout a: String, inout b: String) {
    let temporaryA = a
    a = b
    b = temporaryA
}
func swapTwoDoubles(inout a: Double, inout b: Double) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

我们可以发现，这三个函数的函数体是很相似的，唯一不同的就是处理的参数类型。

如果我们可以写出一个函数，他可以交换任意两个类型，那么我们的代码就会很简洁，而泛型就是为了这一目的而存在的语言特性。

    注意
    上面三个函数，有一个隐含的要求，就是a和b的类型是一样的，如果他们的类型不一样，也就不可能交换他们的值。

### 泛型函数

泛型函数可以使用任何值。

``` swift
func swapTwoValues<T>(inout a: T, inout b: T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

swapTwoValues的函数体和swapTwoInts的完全一样，但是，函数的第一行区别很大。

``` swift
func swapTwoInts(inout a: Int, inout b: Int)
func swapTwoValues<T>(inout a: T, inout b: T)
```

泛型的版本，有一个占位符类型（这里是 T），而不是特定的某个类型（例如Int，String）。占位符类型没有说任何关于类型的信息，但是却表示了a和b必须是同一类型。真正的类型会在调用时决定。

另一个不同是泛型函数的名字后面跟着占位符类型，包裹在一个尖括号里。尖括号告诉Swift T是一个类型名字的占位符，因为他是占位符，Swift不会去寻找真实的T类型。

``` swift
var someInt = 3
var anotherInt = 107
swapTwoValues(&someInt, &anotherInt)
// someInt is now 107, and anotherInt is now 3
var someString = "hello"
var anotherString = "world"
swapTwoValues(&someString, &anotherString)
// someString is now "world", and anotherString is now "hello"
```

我们可以给swapTwoValues传递Int或String或任何类型。

    注意
    swapTwoValues的名字来自于Swift的标准库的swap函数。如果我们需要，可以直接使用。

### 类型参数

在上面的例子里，T是一个类型参数。类型参数表示了一个占位符类型，紧跟着函数名，包裹在尖括号里。

我们表明一个类型参数之后，我们就可以定义这个类型的参数（例子里的a和b），或者返回值类型，或者在函数体里使用。不论如何，这个占位符类型都会被调用时的实际类型所取代。

我们可以提供多个类型参数，都写在尖括号里，逗号隔开。

### 命名类型参数

简单的情况下，泛型函数和泛型类型只会用到一个类型参数（例如上面的例子和Array），传统上就使用T来表示。然而我们可以使用任何符合规则的标示符。

如果我们在写比较复杂的泛型函数和泛型类型，他们需要多个类型参数，那么给类型参数一些有意义的名字就非常有意义了。例如，Swift的Dictionay类型，有两个类型参数，分别是Key和Value。

    注意
    类型参数的名字应该是首字母大写的驼峰格式，这样说明他是一个类型的占位符。

### 泛型类型

除了泛型函数，Swift还允许我们定义自己的泛型类型。这些自定义的类，结构体，枚举可以使用任何类型作为类型参数，就和Array，Dictionary一样。

这部分我们将实现一个泛型的集合，栈。栈是一个有序的集合，和数组很像。但存取方式有很大区别，数组允许在任意地方存取，而栈，只能从最顶部的元素存取。

    注意
    UINavigationController的push和pop，就是栈的一个很好的例子。在你需要后进先出得时候，就应该使用栈。

``` swift
struct IntStack {
    var items = [Int]()
    mutating func push(item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
}
```

这是一个非泛型的栈的定义。我们用一个数组来作为内部存数的容器，然后实现push和pop。

``` swift
struct Stack<T> {
    var items = [T]()
    mutating func push(item: T) {
        items.append(item)
    }
    mutating func pop() -> T {
        return items.removeLast()
    }
}
```

IntStack只能处理Int类型，我们把它改写为泛型之后，就可以处理任何类型。

注意，占位符类型T，在三个地方使用：
    *   创建一个items的属性，是T类型的一个空数组
    *   标明push函数接受的参数必须是T类型
    *   标明pop函数返回的类型是T类型

``` swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
stackOfStrings.push("cuatro")
// the stack now contains 4 strings
```

``` swift
let fromTheTop = stackOfStrings.pop()
// fromTheTop is equal to "cuatro", and the stack now contains 3 strings
```

### 扩展一个泛型类型

当我们扩展一个泛型类型时，我们不需要在扩展定义时给出类型参数列表。原始类型的类型参数列表在我们的扩展内自动可见的，类型参数的名字也和原始类型一致。

下面，我们扩展Stack类型，添加一个返回顶部元素的只读的计算属性。

``` swift
extension Stack {
    var topItem: T? {
        return items.isEmpty ? nil : items[items.count - 1]
    }
}
```

注意，没有这里扩展没有给出类型参数，而是直接使用了Stack原始定义时的T。

``` swift
if let topItem = stackOfStrings.topItem {
    println("The top item on the stack is \(topItem).")
}
// prints "The top item on the stack is tres."
```

### 类型限制

swapTwoValues和Stack可以使用任何类型，但是，有时候我们需要限定只有特殊的类型才可以作为参数传递给泛型函数和泛型类型。类型限制就表明了类型参数必须是继承自某个类，或是必须实现了某个协议或协议组。

例如，Swift的Dictionary类型就限制了可以作为键的类型，这个类型必须是可以哈希的。也就是说，必须可以给出一种方式来使得自己是唯一的。而字典使用这个哈希来检查是否已经有相同的key存在，如果没有这个限制，字典就无法知道是否改插入还是替换某一个键，也无法根据键来找到对应的值。

这个要求是强制在Dictionary的键的类型上，必须实现Hashable协议。Swift的所有基础类型（Int，String，Double，Bool）都实现了这一协议。

我们可以定义自己的类型限制。像Hashable这样的抽象帮我们定义了一些概念上的特性，而不是特定的类型。

####    类型限制语法

我们在类型参数列表里的参数后面加上冒号，跟着需要继承的类或需要实现的协议来定义类型限制。

``` swift
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}
```

上面的函数，定义了两个类型参数，T和U，他们两个分别需要继承自SomeClass和实现SomeProtocol。

####    类型限制实践

下面是一个非泛型的函数，来在一个数组里查找某个值第一次出现的索引。

``` swift
func findStringIndex(array: [String], valueToFind: String) -> Int? {
    for (index, value) in enumerate(array) {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
let strings = ["cat", "dog", "llama", "parakeet", "terrapin"]
if let foundIndex = findStringIndex(strings, "llama") {
    println("The index of llama is \(foundIndex)")
}
// prints "The index of llama is 2"
```

这种查找，不单对于String需要，其他类型也会需要。所以我们应该写成泛型的。

``` swift
func findIndex<T>(array: [T], valueToFind: T) -> Int? {
    for (index, value) in enumerate(array) {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

我们这么写后，并没有办法编译，因为在我们检查value == valueToFind的时候，不是所有的Swift类型都定义了 == 操作符。比如我们自己定义的类型，相等的意义并不是Swift可以给我们提供的。所以我们无法保证每个T都满足要求。

尽管如此，Swift标准科定义了一个Equatable的协议，它需要任何实现的类型提供 == 和 != 操作符用来比较。所有的Swift标准类型都实现了这一协议。

那么所有的实现了Equatable得类型都是在findIndex中使用，这样我们就需要限制我们的类型必须是实现了Equatable协议的类型。

``` swift
func findIndex<T: Equatable>(array: [T], valueToFind: T) -> Int? {
    for (index, value) in enumerate(array) {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
let doubleIndex = findIndex([3.14159, 0.1, 0.25], 9.3)
// doubleIndex is an optional Int with no value, because 9.3 is not in the array
let stringIndex = findIndex(["Mike", "Malcolm", "Andrea"], "Andrea")
// stringIndex is an optional Int containing a value of 2
```

### 关联的类型

当我们定义协议的时候，可以声明一个或多个关联的类型来作为协议定义的一部分。一个关联的类型就是给定一个类型的占位符，然后可以在协议定义中使用。真实的类型，直到协议被真实实现的时候才会确定。关联的类型由 typealias 关键字声明。

####    关联类型实践

``` swift
protocol Container {
    typealias ItemType
    mutating func append(item: ItemType)
    var count: Int { get }
    subscript(i: Int) -> ItemType { get }
}
```

我们定义了一个Container协议，里面关联了一个ItemType。

Container协议定义了一个方法来追加元素，一个属性来获取元素数量，一个角标来访问元素。

它没有说明，元素是如何存储的，也没有说明元素的类型是什么。一个实现这个协议的类型，只需要实现上面三点要求即可。

任何实现Container协议的类型必须可以说明它存储的元素类型。确切来讲，就是他必须保证只有正确的类型可以被添加到容器内，或者被访问。

为了描述这种要求，Container协议需要一种方法来说明容器元素的类型，但是它又不需要知道是什么类型。

为了达到这点要求，Container声明了一个关联类型ItemType。Container协议没有定义ItemType，而是把ItemType作为一种方式来表示容器内的元素类型，并在协议定义中使用它来确保协议的行为是正确的。

``` swift
struct IntStack: Container {
    // original IntStack implementation
    var items = [Int]()
    mutating func push(item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    // conformance to the Container protocol
    typealias ItemType = Int
    mutating func append(item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

这是一个非泛型的Container协议的实现。它把ItemType定义为了Int。

因为Swift强大的类型推导，我们其实可以不写 typealias ItemType = Int，因为通过append方法，就可以推导出容器元素的类型了。

``` swift
struct Stack<T>: Container {
    // original Stack<T> implementation
    var items = [T]()
    mutating func push(item: T) {
        items.append(item)
    }
    mutating func pop() -> T {
        return items.removeLast()
    }
    // conformance to the Container protocol
    mutating func append(item: T) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> T {
        return items[i]
    }
}
```

这一次，我们用泛型的方式来实现Container容器，因为我们用T来作为append方法的参数的类型,Swift会把ItemType关联到T。

####    扩展一个已有类型来给出关联类型

我们可以通过扩展类型来增加协议的实现。这也包括了还有关联类型的协议。

Swift的Array已经提供了append方法，count属性和角标。这些正好满足了Container协议。这样我们就可以简单的写一个扩展来让Array实现Container协议。

``` swift
extension Array: Container {}
```

Array已有的append方法和角标会告诉Swift如何正确的推导ItemType的类型。

### Where分句

类型限制，可以让我们定义关联到泛型函数和泛型类型的类型参数的要求。

有时候我们需要能给关联类型也定义要求。我们可以通过在类型参数列表上添加where分句来做到这一点。where分句使得我们可以要求一个关联的类型必须实现某个协议，或者某个类型参数和关联的类型必须一样。我们把where分句就写在类型参数列表的后面。

``` swift
func allItemsMatch<
    C1: Container, C2: Container
    where C1.ItemType == C2.ItemType,C1.ItemType: Equatable>
    (someContainer: C1,anotherContainer: C2) -> Bool {
         // check that both containers contain the same number of items
        if someContainer.count != anotherContainer.count {
            return false
        }
        // check each pair of items to see if they are equivalent
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }
         // all items match, so return true
        return true
}
```

allItemsMatch的函数有2个参数，他们都是实现了Container协议的容器，这里我们使用where分句添加了一些限制，使得整个参数的要求变成了：
    *   C1和C2都必须实现Container协议
    *   C1的ItemType和C2的ItemType必须一致
    *   C1的ItemType必须实现Equatable协议，因为C1和C2的ItemType一致，所以C2的ItemType也是实现了Equatable的。

第二，三个要求是由where分句增加的。

``` swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
 
var arrayOfStrings = ["uno", "dos", "tres"]
 
if allItemsMatch(stackOfStrings, arrayOfStrings) {
    println("All items match.")
} else {
    println("Not all items match.")
}
// prints "All items match."
```


