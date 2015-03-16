---
layout: post
title: "Language Guide: Closures"
date: 2014-10-06 14:41:52 +0800
comments: true
categories: [Swift]
---


闭包是一段独立的功能，可以在代码之间传递和使用。Swift里的闭包和C，ObjC里的block，以及其他语言里的lambda表达式是相似的。

闭包可以捕获和存储定义在定义闭包的上下文里的任何变量和常量的引用。这被叫做遮盖(closing over)这些常量和变量，正说明了闭包的概念。Swift会帮你处理所有这些捕获时的内存管理。

在上一章函数里，全局和嵌套函数，其实是闭包的特殊情况。闭包有三种形式：

*	全局函数是闭包，它有名字，但是不会捕获任何值。
* 	嵌套函数是闭包，它有名字，并且会捕获包含函数里值。
*  	闭包表达式是无名闭包，有简单的语法，从包裹他们的上下文里捕获值。

Swift的闭包表达式有着清晰，简洁的风格，并被优化过，鼓励在一般情况下写出简洁，不杂乱的语法。这些优化包括：

*	从上下文推断参数和返回值类型。
* 	从单个表达式的闭包自动返回值。
*  	参数名缩写。
*  	尾部闭包语法。

<!--more-->

#####	闭包表达式

嵌套函数，是在大函数里定义有名字的，独立的代码块的很方便的方法。然后，有时候我们需要写出更简短的，不需要全部定义和名字的类似函数的结构，特别是你在把函数当做参数和返回值的时候。

闭包表达式是一种通过简洁，集中的语法来写内连(inline)闭包的方式。闭包表达式经过优化在不损失清晰性的前提写出简短的代码。

######	sorted函数

Swift标准库里的sorted函数用来排序一个已知类型的数组，基于你传递给它的排序闭包。一旦配需结束，它返回一个排好序的数组。原来的数组并没有被sorted函数改变。

下面是我们要排序的数组：

``` swift
let names = ["Chris","Alex","Ewa","Barry","Daniella"]
```

sorted的函数接受2个参数，一个是要排序的数组，另一个是一个闭包，接受两个参数，他们的类型需要和数组元素的类型一致，返回一个Bool值，来表示如果第一个参数应该排在第二个参数之前，那么就是true，反之则是false。

这个例子我们排的是String，所以闭包的类型应该是(String,String) -> Bool。

一种方式，我们写出排序的函数，然后把它传递给sorted函数。

``` swift
func backwards(s1:String, s2:String) -> Bool {
	return s1 > s2
}
var reversed = sorted(names,backwards)
```

如果s1大于s2，返回true，意思是如果s1大于s2，那么s1应该排在s2的前面。对于字符串里的字符来说，大于就是在字母表里排在后面。也就是说'B'大于'A'。这样就给出了字母表反序的排列。

然而，这是一个相对比较长的写法，关键部分其实只有 a > b，我们应该用闭包表达式来写。

######	闭包表达式

闭包表达式的语法如下：

	{ (parameters) -> (return type) in 
		statements
	}
	

闭包表达式的语法，可以使用常量参数，变量参数和inout参数。不能有默认值。不定长参数可以使用，但是必须在最后一个。元组可以作为参数和返回值使用。

``` swift
reversed = sorted(names, { (s1: String, s2: String) -> Bool in
	return s1 > s2
})
```

注意，这里的参数和返回值和之前通过定义函数来做的时候是一样的。然而，在闭包表达式里，参数和返回值是包裹在大括号里的，而不是在外面。

闭包表达式body的开始由 in 来标记。这个关键字说明了闭包的参数和返回值声明的结束，闭包的body的开始。

因为这个闭包的body很短，我们可以把它写在一行里。

``` swift
reversed = sorted(names, { (s1: String, s2: String) -> Bool in return s1 > s2 } )
```

######	从上下文推断类型

因为这个排序闭包作为参数传递给一个函数，Swift可以通过sorted的第二个参数的类型来推断出这个闭包的类型。sorted函数的这个参数需要一个(String,String)->Bool类型的闭包，这意味着我们其实不用在闭包表达式里写出这个类型，因为所有的类型都可以推断出来，那->和参数名字周围的括弧可以省略掉。

``` swift
reversed = sorted(names,{s1,s2 in return s1 > s2})
```

当我们把一个内连的闭包表达式传递给一个函数时，我们总是能够推断出他的参数和返回值类型，也就是说，这种情况下，你永远都不需要把闭包的完整格式写出来。

尽管如此，你仍热可以把类型明确的写出来，事实上，如果这样可以增加代码的可读性，就应该写出来。在上面sorted的例子里，闭包的目的就是排序，而且认为它就是在处理String类型也不会有问题，因为它是用来帮助排序一个字符串数组的。

######	从单个表达式的闭包自动返回值

单个表达式的闭包可以自动返回唯一的表达式的值，所以不需要写return。

``` swift
reversed = sorted(names,{s1,s2 in s1 > s2})
```

######	参数名缩写

Swift自动为内连闭包提供参数的缩写，他们是$0,$1,$2 ...。

如果我们是用缩写的参数名，那么我们就不用写出参数列表，而这些缩写的参数的类型也会被推断出来。in 也可以被省略掉。

``` swift
reversed = sorted(names, { $0 > $1})
```

######	操作符函数

其实，上面的例子我们可以写的更短。Swift的String类型定义了 > 作为一个函数，接受2个String，返回一个Bool。这个正好满足我们sorted函数的要求。因此，我们可以直接传入 >，Swift会推断出你想要它做的事情。

``` swift
reversed = sorted(names, >)
```

#####	尾部闭包

如果你把一个闭包做为函数参数的最后一个传递给一个函数，你可以把这个闭包用尾部闭包的语法来写。尾部闭包的写法是把这个闭包写在括弧的后面，而不是包裹在括弧里。

``` swift
func someFunctionThatTakesAClosure(closure: ()->() ){
// body here
}
someFunctionThatTakesAClosure({
// body here
})
someFunctionThatTakesAClosure() {
// body here
}
```

	注意
	如果比个闭包表达式是一个函数的唯一一个参数，你通过尾部闭包来提供这个参数。那么你可以不写（）。

上面的sorted的例子也可以用尾部闭包来写。

``` swift
reversed = sorted(names) { $0 > $1 }
```

尾部闭包在处理闭包函数体很大的时候非常有用。例如，Swift的Array有个map函数，接受一个闭包作为唯一的参数。这个闭包会在每个数组元素上调用，然会一个新的值，然后map函数会把所有这些作为一个新的数组返回。

``` swift
let digitNames = [
	0: "Zero", 1: "One", 2: "Two", 3: "Three", 4: "Four",
	5: "Five", 6: "Six", 7: "Seven", 8: "Eight", 9: "Nine"
	]
let numbers = [16,58,510]
```

``` swift
let strings = numbers.map {
	(var number) -> String in
	var output = ""
	while number > 0 {
		output = digitNames[number % 10]! + output // 注意 !
		number /= 10
	}
	return output
}
```

map方法调用上面的闭包。你不需要给出number的类型。

这个例子，number参数被定义成了var，所以你可以改变它的值。并且明确给出了闭包的返回值类型是一个String。

	注意
	在通过角标访问digitNames的时候，后面的那个！，因为角标访问字典回复的时个optional，我们应该判断是否为nil。不过这个例子里，不可能出现 nil，所以没有判断直接解包。

这个例子很好的展示了使用尾部闭包来把闭包的功能紧凑的挨着调用函数而不用把闭包放在函数的括弧里。

#####	捕获值

闭包从定义它自己的上下文里捕获常量和变量的值。然后，闭包就可以在闭包体内引用和修改这些值，即使最开始定义这些值的作用域已经不存在了。

最简单的闭包就是嵌套函数。一个嵌套函数可以捕获包裹函数的参数，以及所有的定义在包裹函数内的常量和变量。

``` swift
func makeIncrementor (forIncrement amount: Int) -> () -> Int {
	var runningTotal = 0
    func incrementor() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementor
}
```

上面例子，makeIncrementor的返回值是一个类型为() -> Int的函数。

makeIncrementor定义了一个runningTotal，来存储当前makeIncrementor的runningTotal，初始值是0。

makeIncrementor的唯一参数amount是一个Int值，每次调用incrementor，都会把这个amount加到runningTotal上面。

makeIncrementor还定义了一个嵌套函数incrementor，它来做真正的增加操作。

如果我们单独来看incrementor函数，它是很奇怪的。

他没有定义任何参数，但是却引用了runningTotal和amount。它是把这个值捕获了。

incrementor并没有修改amount，所以他只是拷贝的一份。然而，对于runningTotal，incrementor每次调用都会修改它的值。所以incrementor事实上是捕获了一份引用，而不是简单的拷贝值。捕获引用可以确保runningTotal在makeIncrementor调用结束后不消失，这样下次调用incrementor的时候就会存在。

	注意
	Swift会决定什么值需要拷贝，什么需要捕获引用。你不需要做任何事。Swift也负责所有的内存管理。

``` swift
let incrementByTen = makeIncrementor(forIncrement:10)
incrementByTen() // 10
incrementByTen() // 20
incrementByTen() // 30
```

如果你定义一个新的incrementor，他会存储它自己的，新的一个runningTotal。调用之前定义的incrementByTen并不会干扰这个新的。

``` swift
let incrementBySeven = makeIncrementor(forIncrement: 7)
incrementBySeven() // 7
incrementByTen()  // 40
```

	注意
	如果你把闭包附值给了一个类实例的属性，并且这个闭包捕获了这个类实例或它的成员，这将是一个循环引用。你需要使用捕获列表来处理。

#####	闭包是引用类型

上面的例子里，incrementBySeven和incrementByTen是常量。但是这些常量引用的闭包依旧可以修改他们捕获的runningTotal的值。这是因为函数和闭包是引用类型。

无论何时你把一个函数或闭包附值给一个常量或变量，你都是把它的引用附值给了常量或变量。上面的例子，是说incrementByTen的引用是个常量，(注：就是它不能在引用其他闭包）而不是闭包本身是常量。

这也意味着，同一个闭包可以被多个变量或常量引用。


















