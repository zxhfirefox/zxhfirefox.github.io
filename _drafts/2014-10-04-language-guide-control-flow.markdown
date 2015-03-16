---
layout: post
title: "Language Guide: Control Flow"
date: 2014-10-04 14:39:29 +0800
comments: true
categories: [Swift]
---

Swift提供了和类C语言相似的流程控制语句。包括for和while来做循环，if和switch来做分支，break和continue来做打断。

除了支持for，Swift还加入了for-in来简化字符串，数组，字典，区间和其他序列的遍历。

Swift的switch也比C的要强大许多，case不会默认的执行下一个case，避免了忘写break带来的bug。case也可以匹配其他的模式，包括字符串，区间，并且可以cast到某个特定类型。匹配的值可以绑定到临时变量，这样在case里面就可以使用，复杂的case可以通过添加where来表达。

_注：这里很多的内容都是和C，ObjC一样的，所以很多地方都省略了，重点翻译了Swift里的特性和一些需要特别注意的地方。_


<!--more-->

#####	For循环

两种：

*	for-in
* 	for

######	for-in

使用for-in来循环遍历一个序列的元素。

``` swift
for index in 1...5 {
	println("\(index) times 5 is \(index * 5)")
}
```

上面的循环里，index是常量，每次遍历被赋予新值。

如果不需要每次遍历给出的值，可以用 _ 来接受遍历给出值。

######	for

和C一样的for循环。

``` swift
for var index = 0; index < 3; ++index {
	println("index is \(index)")
}
```

index只在for循环语句内可见。如果想在for语句后访问index，必须在for语句之前声明。

注意如果在for循环外使用index的值，那么++index和index++是不一样的。

#####	while循环

两种：

*	while
* 	do-while

######	while

######	do-while

#####	条件语句

if和switch

######	if

######	switch

switch语句必须是穷举的，这意味着，switch的所有case的组合必须覆盖了条件值可以取值的所有范围。

switch语句，不会在执行完一个case之后继续执行下一个case。

条件值，可以用范围来区分case。

``` swift
let count = 3_000_000_000_000
let things = "stars in the Milky Way"
var naturalCount: String
switch count {
case 0:
	naturalCount = "no"
case 1...3:
	naturalCount = "a few"
case 4...9:
	naturalCount = "several"
case 10...99:
	naturalCount = "tens of"
case 100...999:
	naturalCount = "hundreds of "
case 1000...999_999:
	naturalCount = "thousands of"
default:
	naturalCount = "millions of "
}
```

条件值，也可是元组。元组的每个元素都可以匹配，使用 _ 来匹配任意值。

``` swift
let somePoint = (1,1)
switch somePoint {
case (0,0):
	println("origin")
case (\_,0):
	println("x axis")
case (0,_):
	println("y axis")
case (-2...2,-2...2):
	println(" in a square")
defualt:
	println("out of square")
}
```

上面的例子里，和C不一样，Swift允许多个case包含相同的值，（0，0）满足4个分支。然而，如果有多个匹配，那么只执行第一个匹配。

switch的case可以把匹配的值绑定到临时变量，以便在case的代码中使用。

``` swift
let point = (2,0)
switch point {
case (let x,0):
	println("on x axis with x = \(x)")
case (0,let y):
	println("on y axis with y = \(y)")
case let (x,y):
	println("x = \(x), y = \(y)")
}
```

上面的x，y是常量，如果声明为var，也只能在case语句内改变。

switch的case可以使用where来表达复杂的case条件。

``` swift
let point = (1,-1)
switch point {
case let(x,y) where x == y:
	println("point on the line x == y")
case let(x,y) where x == -y:
	println("point on the line x == -y")
case let(x,y):
	println("arbitrary point")
}
```

##### 控制转换语句

*	continue
* 	break
*  	fallthrough
* 	return

######	continue

######	break

######	fallthrough

Swift里，switch的case默认不会fallthrough，如果需要，那么用fallthrough。

######	标签语句

嵌套switch或者循环在其他的switch和循环里，能够写出非常复杂的控制语句。然后循环和switch都使用break来终止执行。那么，如果能够明确的说明终止到哪一个循环或switch语句是很有用的。对于continue也有这样的使用场景。

为了达到这个目标，我们可以在循环或switch语句上加上标签，然后在break和continue的时候写上标签，来告诉程序跳转到哪里继续执行。
























