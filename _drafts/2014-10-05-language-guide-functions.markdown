---
layout: post
title: "Language Guide: Functions"
date: 2014-10-05 14:40:40 +0800
comments: true
categories: [Swift]
---

函数是用来完成某一任务的独立的一段代码。函数名标示了它用来做什么，也用来调用这个函数。

Swift统一的函数语法，既可以不要参数名字来表达简单的C语言的函数，也可以给出内部和外部参数名字来表达ObjC里复杂的函数。参数可以提供默认值，也可以做为输入输出参数，在函数执行后，改变传进来的值。

Swift里每一个函数都一个一个类型，这个类型由参数类型和返回值类型组成。我们可以把这个类型和Swift里其他类型同等对待。这样，可以非常方便的把函数作为参数或返回值。函数也可以定义在其他函数里。


<!--more-->

######	定义和调用函数

当你定义一个函数时，你可以可选的定义一个或多个有类型的参数作为输入，并且或者定义一个类型的值作为函数调用后的返回值。

每个函数都有一个函数名，描述了这个函数是做什么的。为了使用这个函数，你通过把输入参数传递给这个函数名来调用这个函数。一个函数的参数必须按照定义的顺序传递给函数。

``` swift
func sayHello(person: String) -> String {
	let greeting = "Hello, " + person + "!"
	return greeting
}
```

func来定义一个函数，用 ->来分割参数和返回值。

#####	函数参数和返回值

Swift函数的参数和返回值非常的灵活。

######	多参数函数

``` swift
func halfOpenRangeLength(start: Int, end: Int) -> Int {
	return end - start
}
```

######	无参数函数

``` swift
func sayHelloWorld() -> String{
	return "Hello,World"
}
```

######	无返回值函数

``` swift
func sayGoodbye(person: String) {
	println("Goodbye, \(person)")
}
```

	注意
	严格来讲，sayGoodbye还是有返回值的。函数在没有声明返回值的时候会返回一个Void类型的特殊值，也就是一个空的元组，()。

函数的返回值可以被忽略。

	注意
	函数的返回值可以被忽略，但是函数如果声明了返回值，那么它必须返回一个值，否则编译错误。

######	多返回值函数

可以使用元组来把多个值组合起来返回。

``` swift
func minMax(array: [Int]) -> (min:Int,max:Int){
	var currentMin = array[0]
	var currentMax = array[0]
	for value in array {
		if value > currentMax {
			currentMax = value
		else if value < currentMin {
			currentMin = value
		}
	}
	return (currentMin,currentMax)
}
```

######	optional的元组返回类型

如果函数返回的元组在有些时候不存在，那么可以把返回的元组声明为optional的。

	注意
	一个optional的元组(Int,Int)? 和一个包含optional值的元组(Int?,Int?)是不一样的。

上面的minMax没有做安全检查，如果数组没空，访问array[0]是有问题的。

``` swift
func minMax(array: [Int]) -> (min:Int,max:Int)? {
	if array.isEmpty { return nil }
	var currentMin = array[0]
	var currentMax = array[0]
	for value in array {
		if value > currentMax {
			currentMax = value
		else if value < currentMin {
			currentMin = value
		}
	}
	return (currentMin,currentMax)
}
```
使用optional绑定来检查返回值。

######	函数参数的名字

上面所有的函数都定义了参数的名字。

``` swift
func someFunction(parameterName :Int){
}
```

但是，这些名字都只能在函数内部使用，而不能在调用函数时使用。这样的参数名字一般叫做本地参数名。

######	外部参数名

有些时候，如果能够在调用函数的时候提供参数的名字，来说明每个参数的意思是很有用的。

你可以在本地参数名前面加一个外部参数名，用空格隔开。

``` swift
func someFunction(externalParameterName localParameterName :Int){
}
```

	注意
	如果你定义了外部参数名，那这个名字必须在调用时使用。

例如：

``` swift
func join(s1: String, s2: String, joiner: String) -> String {
	return s1 + joiner + s2
}
```

调用时:

``` swift
join("hello","world",",")
```

使用外部参数名：

``` swift
func join(string s1: String, toString s2: String, withJoiner joiner: String) -> String {
	return s1 + joiner + s2
}
```

调用时：

``` swift
join(string: “hello", toString: "world" withJoiner: ”,"）
```

######	外部参数名简写

如果你的外部参数名，和本地参数名的名字一样的话，你不需要写两遍，而是在参数名前面加 # 。

``` swift
func containsCharacter(#string: String, #characterToFind: Character) -> Bool {
    for character in string {
        if character == characterToFind {
            return true
        }
    }
    return false
}
```

######	默认参数值

你可以给任意一些参数提供默认值，如果有默认值，那么在调用时，你可以不传递参数。

	注意
	你应该把有默认参数值的参数放在参数列表的最后，这样确保调用时的没有默认值的参数顺序是对的。

例子：

``` swift
func join(string s1: String, toString s2: String,
    withJoiner joiner: String = " ") -> String {
        return s1 + joiner + s2
}
```

调用时，如果不给第三个参数

``` swift
join(string: "hello", toString: "world")
```

如果给了第三个参数

``` swift
join(string: "hello", toString: "world", withJoiner: "-")
```

######	有默认值的外部参数名

在大多数情况下，如果一个参数有默认值，它应该对应的有一个外部参数名。这样可以确保在调用时，实参传递给了正确的形参。

为了简化这个步骤，Swift为每一个有默认值的参数一个外部参数名，和内部参数名一致，就好像你写了 # 一样。

	注意
	你可以通过写一个 _ 作为外部参数名来不用这个特性，但是有默认值的参数对应有外部参数名是比较好的做法。

######	不定长的参数

一个不定长的参数，接受0个或多个某一类型的参数。在参数类型后面加 ... 来表示不定长参数。 

传入到不定长参数的实参会作为一个数组存在。

``` swift
func arithmeticMean(numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
```

	注意
	一个函数最多只能有一个不定长参数，而且必须是参数列表的最后一个。如果一个函数有默认值参数和不定长参数，先写默认值参数，再写不定长参数。

######	常量和变量参数

函数参数默认是常量，如果你在函数里改变参数的值，会造成编译错误。

但是，有时候能够使用一个可变的参数的拷贝会很方便。你可以声明参数为var来做到这一点。

``` swift
func alignRight(var string: String, count: Int, pad: Character) -> String {
    let amountToPad = count - countElements(string)
    if amountToPad < 1 {
        return string
    }
    let padString = String(pad)
    for _ in 1...amountToPad {
        string = padString + string
    }
    return string
}
```

	注意
	你对var的参数做的改变只在当次函数调用时有用。在函数外部不可见。var的参数也只在函数调用期间存在。

######	输入输出函数

变量参数只能在函数内起作用，如果你希望你对一个参数做的改变可以在函数调用结束后依然有效，你需要把它声明为输入输出参数。

在参数前面加上 inout 来标示一个输入输出参数。

你只能给 inout参数传递一个变量，而不能传递常量或字面量。你在实参前面加一个 & 来标示这个参数是作为 inout 参数传入的。

	注意
	inout 参数不能有默认值，也不能作为不定长参数。如果标记为 inout，也不能再标记let或者var。

``` swift
func swapTwoInts(inout a: Int, inout b: Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

调用时：

``` swift
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
println("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
```

	注意
	inout参数和函数返回值不一样。

#####	函数类型

每个函数都有函数类型，由参数类型和返回值类型组成。

``` swift
func addTwoInts(a: Int, b: Int) -> Int {
    return a + b
}
func multiplyTwoInts(a: Int, b: Int) -> Int {
    return a * b
}
```

上面两个函数的类型都是 (Int,Int) -> (Int)。读作一个函数类型，接受2个Int类型的参数，返回一个Int类型的值。

另一个例子：

``` swift
func printHello() {
	println("hello")
}
```

它的函数类型是 () -> ()，读作不接受参数，返回Void。

######	使用函数类型

使用函数类型和其他类型一模一样。

``` swift
var mathFunc: (Int,Int) -> Int = addTwoInts
```

######	函数类型作为参数类型

可以把函数类型作为参数类型，这让调用者可以传入一个函数来帮助实现我们的函数。

``` swift
func printMathResult(mathFunction: (Int, Int) -> Int, a: Int, b: Int) {
    println("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
```

######	函数类型作为返回值类型

``` swift
func stepForward(input: Int) -> Int {
    return input + 1
}
func stepBackward(input: Int) -> Int {
    return input - 1
}
func chooseStepFunction(backwards: Bool) -> (Int) -> Int {
    return backwards ? stepBackward : stepForward
}
var currentValue = 3
let moveNearerToZero = chooseStepFunction(currentValue > 0) //注：let而不是var
println("Counting to zero:")
// Counting to zero:
while currentValue != 0 {
    println("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
println("zero!")
```

#####	嵌套函数

目前见到的所有函数都是全局函数，被定义在一个全局作用域内。我们也可以在函数内定义其他函数。

嵌套函数默认为外部是不可见的。但是可以被包含函数调用。包含函数也可以返回内嵌函数，让外部可以在其他作用域被调用。

``` swift
func chooseStepFunction(backwards: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backwards ? stepBackward : stepForward
}
var currentValue = -4
let moveNearerToZero = chooseStepFunction(currentValue > 0)
// moveNearerToZero now refers to the nested stepForward() function
while currentValue != 0 {
    println("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
println("zero!")
```














