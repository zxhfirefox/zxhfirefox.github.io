---
layout: post
title: "Language Guide: The Basics"
date: 2014-09-27 15:54:49 +0800
comments: true
categories: [Swift]
---


Swift是一门iOS和OS X app开发的新语言，尽管如此，如果你用过C或者ObjC，Swift里很多部分你都不会陌生的。

Swift提供了C和ObjC所有的基础类型，包括Int来表示整数，Double和Float表示浮点数，Bool表示布尔值，String表示文本数据。Swift也提供了两个重要的集合类型，Array和Dictionary。

和C一样，Swift通过一个名字来标识变量，用变量来存储和引用相应的值。Swift也大量使用不可变的变量，也就是常量，但是要比C里的常量更加优秀。在你需要表示一些不可变的值的时候，使用常量让代码更安全，更清晰。

除了相似的类型，Swift也提供了在ObjC中没有的类型，例如元组。元组让你可以创建和传递一组值。你可以使用元组来把一组值作为一个组合的值从函数返回。

Swift也提出了Opional类型，用来表示一个没有的值。Optional表示要么这里有个值，就是x，要么这里就没有值。Optional有点像在ObjC中使用 nil,但是Optional可以用于任何类型，而不单是类。Optional比 nil指针更安全，而且更具有表达力，它也是Swift中许多特性的核心。

Optional是Swift是一个类型安全的语言的一个例子。Swift帮助你来清晰你的代码可以使用的类型。如果你的代码期望一个字符串，那么类型系统会禁止你传入一个整数。这样你就可以在开发阶段发现许多的错误。

<!--more-->

####	常量和变量

常量和变量把一个名字（例如 maximumNumberOfLoginAttempts 或 welcomeMessage）和一个特定类型的值（例如 10 或者 "Hello"）关联起来。常量的值，一旦设置之后就不能改变，而变量的值是可以改变的。

#####	声明变量和常量

常量和变量必须在使用前被声明。用 let 来声明常量， var 来声明变量。下面这个例子：

``` swift
let maximumNumberOfLoginAttempts = 10
var currentLoginAttempt = 0
```

这段代码被理解为：

“声明一个常量，叫做 maximumNumberOfLoginAttempts，它的值是10。然后声明一个变量，叫做 currentLoginAttempt，它的初始值是 0.“

这个例子里，最大登录尝试次数是常量，而当前登录尝试次数是变量。

你可以在一行代码里声明多个常量或变量，用 , 分开：

``` swift
var x = 0.0, y = 0.0, z = 0.0
```


	注意
	如果一个值不会改变，那么就把它声明为常量。只把那些会改变的值生命为变量。
	
	
#####	类型标注

你可以在声明变量或常量时提供一个类型标注，来清晰的说明这个常量或变量可以存储的类型。在变量或常量的名字后面加一个冒号，一个空格，然后在跟上类型的名字。

下面这个例子里，表示了一个字符串类型的变量 welcomeMessage:

``` swift
var welcomeMessage: String
```

声明里的冒号，可以理解为"...的类型..."，所以，上面的代码可以理解为，声明一个变量，它的名字叫 welcomeMessage， 它的类型是 String。

“它的类型是 String”的意思是可以存储任何字符串值。

welcomeMessage 现在就可以被附值给任何字符串值。

``` swift
welcomeMessage = "Hello"
```

你可以在一行代码里声明多个同一类型的变量，由逗号分开，只需要在最后一个变量的后面加上类型标注。

``` swift
var red, green, blue: Double
```


	注意
	实际上你很少需要去写类型标注。如果你给变量或常量一个初始化值，Swift可以推断出这个值的类型。
	
	
#####	常量和变量命名

常量和变量的名字可以包含几乎所有的字符，包含unicode字符：

``` swift
let 你好 = "你好，世界"
```

常量和变量的名字不能包含空格字符，数学符号，箭头，私有或错误的Unicode字符，画线或画方框字符。也不能用数字开头，但是数字可以在其他任何地方。

一旦你声明了一个特定类型的常量或变量，你就不能再声明同样名字的常量或变量，也不能用它来存储不同类型的值。也不能从一个变量改到常量，反之亦然。

	注意
	如果你需要定义一个常量或变量，它的名字和Swift的关键字一样，那你需要用 ` 来包含这个名字。但是除非必要，你不应该使用这样的名字。

一个改变一个已有的变量的值为另一个类型匹配的值。下面的例子：

``` swift
var friendlyWelcome = "Hello!"
firendlyWelcome = "Bonjour!"
```


和变量不同，常量的值一旦设置就无法改变，尝试改变的话，编译就会出错。

``` swift
let languageName = "Swift"
languageName = "Swift++" //compile error
```

#####	打印常量和变量

你可以用 println 函数来打印一个常量或变量的值：

``` swift
println(friendlyWelcome)
```

println 是一个全局函数，可以用来打印值到一个合适的输出，它会在最后打印一个换行符。在Xcode里，println 会打印到 console里。（还有一个函数，print，和println一样，但是不会打印换行符）

println 打印你传给它的任意字符串。

``` swift
println("This is a string")
```

println 函数可以打印更复杂的log信息，和Cocoa的NSLog一样。这些信息可以包含变量或常量的值。

Swift 使用字符串插入来把常量或变量的名字作为占位符插入到一个长字符串里，并让Swift用变量或常量到值来代替这个名字。把名字放到圆括号里，在圆括号前面加一个反斜杠：

``` swift
println("The current value of friendlyWelcome is \(friendlyWelcome)"
```

	注意
	你在字符串插入时可用的选项在字符串插入中有详细介绍

#####	注释

用注释来在代码中加入一段不会执行的文本，作为注解或给自己的提醒。注释在编译时会被忽略。

Swift注释和C的注释很相似。 单行注释由两个斜杠开始 (//):

``` swift
// a comment
```

多行注释开始于一个斜杠和一个星号 (/\*)，结束于一个星号和一个斜杠 (*/)

``` swift
/* comment
with multiple lines */
```

和C不一样，Swift里多行注释可以嵌套在其他多行注释里。

``` swift 
/* first comment
/* senond comment nested in first comment */
end of first comment */
```

可以嵌套的多行注释让你在注释代码的时候非常方便。

#####	分号

Swift不像其他很多语言，要求你在每一个语句后面都加上分号，不过如果你愿意，你可以加。但是如果你在一行代码里写了多个语句，分号就是必须的：

``` swift
let cat = "cat"; println(cat)
```

#####	整数

整数是没有小数部分的数字，例如42和－32.整数可以有符号的（正数，0，负数），也可以是无符号的（正数，0）。

Swift提供了8，16，32，64位的有符号和无符号整数。这些整数都有和C很相似的规则，8位无符号整数的类型是 UInt8，32位有符号整数是 Int32。和Swift里其他类型一样，所有这些整数类型都是首字母大写的。

######	整数边界

你可以通过 min 和 max 属性来访问每一个整数类型的最小值和最大值。

``` swift
let minValue = UInt8.min
let maxValue = UInt8.max
```

这些属性的值都是对应不同数字类型的，所以可以在类型匹配的表达式中使用。

######	Int

绝大多数时间，你不需要选择特定大小的整数类型，Swift提供了一个额外的整数类型 Int，它和当前系统单个字的大小一致：
*	在32位系统上，Int就是Int32
* 	在64位系统上，Int就是Int64

除非你需要特定长度的整数，就使用 Int 就好了。这样代码会更一致，更好操作。即使在32位系统上，Int的范围 －2,147,483,648到2,147,483,647在很多情况也足够大了。

######	UIint

Swift也提供无符号大整数，UInt，它也和当前系统单个字的大小一致。
*	在32位系统上，Int就是UInt32
* 	在64位系统上，Int就是UInt64

	注意
	仅仅在需要的时候使用 UInt，一致的使用Int可以帮助代码一致，减少不同类型之间转换和类型推断。

#####	浮点数

浮点数是有小数部分的数字，例如3.1415, 0.1, -2.714。

浮点数比Int类型表示和存储的数字范围大出许多。Swift提供了两类浮点数：
*	Double 表示了64位的浮点数。
* 	Float 表示了32位的浮点数。

	注意
	Double至少有15个小数位，Float至少有6个小数位。

#####	类型安全和类型推断

Swift是类型安全的语言。一个类型安全的语言鼓励你在代码中清晰的表述你需要的类型。例如如果你的代码需要一个String，那就不能传递一个Int。

因为Swift是类型安全的，它会在编译时把任何类型不匹配的都标示为错误。这样你就可以在开发阶段捕获和修复这些错误。

类型检查帮助你使用不同类型的时候避免错误。然而，这不代表你必须把每一个变量或常量的类型都写出来。如果你给出值的类型，Swift会通过类型推断来推导出相应的类型。类型推导可以在编译的时候，根据你提供的值来推导出对应的类型。

因为有了类型推导，Swift比C和ObjC而言，很少需要你给出类型。常量和变量都还是有类型的，但绝大多数工作都已经为你做好了。

类型推断，在你声明变量或常量的同时给予一个初始值的时候，非常方便。你在声明变量和常量时，直接赋给字面量（字面量是你在代码中直接出现的值，例如42和3.14）

比如，如果你给一个常量附值42，而没有给出类型，Swift会把它推断成为一个Int。

``` swift
let meaningOfLife = 42
```

同样，如果你给你常量附值一个浮点数，Swift会把它推断为Double。

``` swift
let pi = 3.14159
```

Swift在推断浮点数时总是优先选择Double。

如果你混合整数和浮点数在同一个表达式中，那么Double会成为表达式的值的类型。

``` swift
let anotherPi = 3 + 0.14159
```

字面量 3 并没有显式类型，所以Double成为了最后的推导结果。

#####	数字字面量

整数字面量可以写为：
*	一个十进制数，没有前缀。
* 	一个二进制数，0b前缀。
*  	一个八进制数，0o前缀。
* 	一个十六进制数，0x前缀。

下面的数字都表示了十进制的 17:

``` swift
let dec = 17
let bin = 0b10001
let oct = 0o21
let hex = 0x11
```

浮点数可以表示为十进制（无前缀）和十六进制（0x前缀）。它们必须在小数点两边都有数字。它们也可以有一个指数，十进制用大写或小写的e，十六进制用大写或小写的p。

有指数的十进制数，需要乘以以10为基的指数：
*	1.25e2，就是1.25\*10*10，就是125.0
* 	1.25e－2，就是1.25/100，就是0.0125

有指数的十六进制数，需要乘以以2为基的指数：
*	0xFp2，就是15\*2*2,就是60.0
* 	0xFp-2，就是15/4，就是3.75

下面这些字面量都是十进制的 12.1875:

``` swift
let dec = 12.1875
let exp = 1.21875e1
let hex = 0xC.3p0
```

数字字面量可以添加额外的格式来让它们更易读。整数和浮点数都可以加额外的0和包含下划线来提高可读性。这些格式不会改变字面量的值：

``` swift 
let pedding = 000123.456
let oneMillion = 1_000_000
let justOverOneMillion = 1_000_000.000_000_1
```

#####	数字类型转换

在一般情况下使用Int类型，即使这个值可能不会为负的。使用默认整数类型使得字面量也可以很好匹配。

在一些特殊情况下，例如数据来自外部，为了提供性能，优化内存使用或其他优化，可以使用特殊大小的整数类型。使用这些整数类型可以有效的捕获边界溢出。

######	整数转换

不同的整数类型所能存储的整数范围是不一样的。一个Int8类型只能存－128到127，而UInt8只能存0到255。如果尝试把范围外的值赋给一个变量或常量，会报编译错误。

``` swift
let cannotBeNegative: UInt8 = -1
let tooBig: Int8 = Int8.max + 1
```

因为每一个数字类型都能存不同范围的值，你必须在每种情况下来做类型转换。这种转换可以避免转换错误，并且使得代码里类型转换是明确的。

要把一个数字类型转到另一个类型，你用现有的值去初始化一个需要类型的变量或常量。下面的例子里：

``` swift
let twoThousand: UInt16 = 2_000
let one: UInt8 = 1
let twoThousandAndOne = twoThousand + UInt16(one)
```

常量one和twoThousand不是同一类型的，所以不能做加法，需要把one转到UInt16来做。

SomeType(ofInitialValue)是调用一个类型的初始化函数（initializer），并传给它一个初始值的默认方式。在底层，UInt16有一个初始化函数，接受一个UInt8值来初始化这个UInt16值。这里你不能传递任何类型，而必须是UInt16提供了初始化函数的类型。我们将在扩展中讲解如何扩展现有的类型来提供能够接受新类型的初始化函数。

######	整数和浮点数转换

在整数和浮点数之前转换必须是显式的：

``` swift
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = Double(three) + pointOneFourOneFiveNine
```

这里，three被用来创建一个Double值，这样加号两边都是同一类型。如果没有这个转换，加法运算式不允许的。

浮点数到整数的转换也必须式显式的：

``` swift
let integerPi = Int(pi)
```

像这样用浮点数初始化整数，浮点数总是被截断的。这意味着4.75变成4，－3.9变成－3.

	注意
	变量和常量的组合和字面量的组合不一样。字面量3和字面量0.14159可以直接相加而不用类型转换。结果的类型会在计算完毕后由编译器推导出来。

#####	类型别名

类型别名允许给现有类型定义一个别名。使用typealias来定义。

当你需要使用一个更加有意义的名字来替换现有的类型的时候，类型别名是很有用的。例如：

``` swift
typealias AuidoSample = UInt16
```

一旦你定义了一个类型别名，那你就可以把它当作原来的类型使用。

``` swift
var maxAmplitudeFound = AudioSample.min
```

这里，AudioSample是UInt16的别名。所以可以调用min来获取UInt16的最小值。

#####	布尔值

Swift提供一个布尔类型，叫Bool。布尔值可以理解为逻辑值，因为它们只能是true或者false。Swift提供了2个布尔常量，true和false。

``` swift
let orangesAreOrange = true
let turnipsAreDelicious = false
```

orangesAreOrange和turnipsAreDelicious都被推断为Bool类型。和上面的Int和Double一样，你不需要写出类型，而只需要用true或false来初始化它们。类型推导使得代码更简练，更可读。

布尔值在用在条件判断的时候非常有用，例如if：

``` swift
if turnipsAreDelicious {
	println("Mmm,tasty turnips!")
}else{
	println("Eww,turnips are horrible.")
}
```

条件语句将在流程控制中详细讲解。

Swift的类型安全不允许把非布尔值作为布尔值使用。下面的会编译错误：

``` swift
let i = 1
if i {
}
```

然而，下面的是可以的：

``` swift
let i = 1
if i == 1{
}
```

i == 1 的结果是布尔类型，所以下面的代码是对的。操作符在基本操作符中介绍。

和其他类型安全的例子一样，这样做可以避免不小心的错误，并使得代码的意图总是很清楚。

#####	元组

元组把多个值组合成为一个单独的值。元组里的值可以是任何的值，而且可以不一样。

下面的例子里，（404,"Not Found")是一个表示http状态码的元组，一个http状态码是你访问一个web服务器返回的一个特定值，404表示你请求的网页不存在。

``` swift
let http404Error = (404,"Not Found")
```

这个元组，组合了一个整数和一个字符串，一个数字和一个人类可读的描述。它可以理解为一个类型是(Type,String)的元组。

你可以从任何类型的组合来创建元组，它们可以包含任意多不同的类型。

你可以把元组多值拆解出来：

``` swift
let (statusCode, statusMessage) = http404Error
println("code is \(statusCode)")
println("message is \(statusMessage)")
```

如果你只需要某些元组的值，那在你拆解元组的时候，用 _ 来忽略你不需要的值：

``` swift
let (justCode,_) = http404Error
println("code is \(justCode)")
```

另外，你可以通过索引值来访问的元组值，从0开始：

``` swift
println("code is \(http404Error.0)")
println("message is \(http404Error.1)")
```

你也可以在创建元组时，给每一个单独的元素定一个名字：

``` swift
let http200Status = (statusCode: 200, description: "OK")
```

如果元组的元素有名字，那你可以直接通过这些名字来访问这些元素：

``` swift
println("code is \(http200Status.statusCode)")
println("message is \(http200Status.description)")
```

元组在函数需要返回多个值的时候非常有用。比如一个函数用来获取网页可以返回一个(Int,String)的元组，这样可以把更多有用的信息返回给调用者。

	注意
	元组只是在把相关的值组合起来时很方便，但是不应该被用来创建复杂的数据结构。复杂的数据结构应该用类或结构体来表示。

#####	Optionals

当一个值可能不存在的时候，你就需要Optionals。一个Optional的意思是
*	如果有值，那么它就是这个值
* 	如果没有值，那就是根本没有值

	注意
	optional的概念在C和ObjC中不存在。最相似的是ObjC的函数可以返回nil表示值的不存在。然而，ObjC的nil只能用于对象，不能用于基础类型，结构体和枚举，对于这些类型，ObjC返回某个特定的值来表示值不存在。这里意味着调用者知道这个特殊值的意思是没有值。Swift的Optional允许你来表示对于任何类型的值不存在的情况，不需要其他特殊常量。

下面这个例子说明 optional是非常有用的。String有个toInt的方法，它尝试把String转换到一个Int。然而，不是所有的字符串都能转换到整数，"123“可以转换到123，但是"hello"却不行。

``` swift
let possibleNumber = "123"
let convertedNumber = possibleNumber.toInt()
```

因为toInt可能失败，所以它返回一个optional的Int，而不是Int。一个optional的Int,写成Int?，而不是Int。问号的意思是这个值可能存在，也可能不存在。

######	nil

你给一个optional附值为 nil， 可以把一个optional的值设置为无值的状态。

``` swift
var serverResponseCode: Int? = 404
serverResponseCode = nil
```


	注意
	nil不能用于非optional的常量或变量。如果你需要这种不存在的状态，你需要把变量和常量声明为optioanl的。

如果你声明一个optional的值，而没有给初始值，那它会附值为 nil。

``` swift
var surveyAnswer: String?
```


	注意
	Swift的nil和ObjC的nil不一样，在ObjC里，nil表示一个不存在的对象。而在Swift里，nil不是指针，它是某个类型的值不存在的表现。任何类型的optional都可以设置为nil，不仅仅是对象。

######	if语句和强制解包（unwrap）

你可以用if语句，拿optioanl和nil比较来知道这个optional是否包含一个值。

如果一个optional包含值，那么它就不等于nil：

``` swift
if convertedNumber != nil {
	prinln("has a number")
}
```

一旦你确定optional确实包含一个值，那么你可以在optional的名字后面加一个感叹号 ！来访问它所包含的值。这个感叹号的意思是说，“我知道里面有值，请使用这个值”，这就叫做强制解包：

``` swift
if convertedNumber != nil {
	prinln("has an integer \(convertedNumber!)")
}
```


	注意
	如果用 ！尝试访问一个不存在值的optional，将造成运行时错误。所以必须在确认optional有值的情况下使用！

######	optional绑定

使用optional绑定来查看一个optional是否还有值，如果有，则把它绑定到一个临时的常量或变量上。optional绑定可以用在if和while语句中来查看otional的值，并把值拿出来给后续使用。

我们可以重写上面的例子：

``` swift
if let actualNumber = possibleNumber.toInt() {
	println("actualNumber \(actualNumber)")
}else{
	println("not a number")
}
```

这段代码的意思是 “如果possibleNumber.toInt()返回的optional还有一个值，把这个值附值给actualNumber“。

如果这个转变成功了，这个actualNumber就可以在if的第一个分支里使用，并且被optional的值初始化了，所以就不用 ！来访问真正的值了。

optional绑定里，你可以使用常量或变量。如果你打算在if的第一个分支里改变actualNumber的值，你可以下 if var actualNumber，这样optional的值就成为一个变量而不是常量。（[zxh]，这里改变的也是actualNumber，而不是optional本身，如果在绑定一次，值其实没有彼岸花）。

######	自动解包的Optional

如上所述，optional暗示了一个常量或变量可能是没有值的。optional可以通过if语句来查看是否有值，也可以通过optional绑定而被有条件的解包来使用真正的值。

有些时候，从程序结构的角度来说，如果一个optional如果被设置之后，它将永远都会有个值。在这种情况下，如果能够不用在每次需要访问这个值的时候去检查并且解包的话，是非常有意义的，因为我们可以确认它肯定有值。

这一类optional被定义为自动解包的optional。你在optional的类型后面，不写？而写一个！来标示它是一个自动解包的optional。

当一个optional的值可以确认在第一次定义之后永远都有一个值，使用自动解包的optional是有意义的。它的主要应用场景是在类初始化的时候。

在底层，自动解包的optional还是个optional。但是可以被当作非optional来用，不需要每次都解包。下面的例子展示了自动解包的optional和普通optional的区别：

``` swift
let posiibleString: String? = "an optional string"
let forcedString: String = possibleString!
let assumedString: String! = "an implicitly unwrapped optional string"
let implicitString: String = assumedString
```

你可以认为自动解包的optional在每次被访问的时候都自动的被解包了。而不用每次访问都在后面加！，只需要在声明的时候在类型后面加！。

	注意
	如果你在一个自动解包的optional不包含值的时候访问了它，这会是个运行时错误。这和普通optional的情况一模一样。

你也可以把自动解包的optional当作普通的optional来用：

``` swift
if assumedString != nil {
	println(assumedString)
}
```

	注意
	如果一个optional在某个时间会没有值，那就不要使用自动解包的optional。对于在声明周期中有可能为nil的时候总是用普通的optional。

#####	断言

optionals让你可以检查一个可能存在也可能不存在的值，并在不存在值的时候写出优雅的处理方式。但是有些时候，如果一个值不存在，程序就无法继续进行。这时候，你应该使用断言来结束代码的执行来调试出现值不存在的原因。

######	使用断言来调试

一个断言是在运行时对一个条件是否为true的检查。从字面意思说就是，断言一个条件为true。在执行任何代码之前，使用断言来确定某个特定的条件为true。如果为true，代码执行，如果为false，代码停止执行，程序退出。

如果你在开发阶段触发了断言，那么就能清楚的看到这个断言的位置，并可以查看造成断言的原因。断言也允许你提供一些调试信息。

使用全局函数 assert 来写一个断言。传给她一个条件表达式，和当条件表达式是false的时候的一个提示信息。

``` swift
let age = -3
assert(age >= 0, "a age can not be less than 0")
```

这个例子了，如果age >= 0为true，后续代码执行，如果为false，则程序退出。

断言信息不是必须的。

``` swift
assert(age >= 0)
```


######	何时使用断言

当一个条件有可能是false，但是你的代码又要求它必须是true的时候使用断言。比较好的地方如下：

*	把一个脚标传入到一个自定义的脚标实现，这个值可能越界。
* 	给函数传值，但是这个值不符合函数的要求。
*  	optional为nil，但是你需要它不是nil。

	
		注意
		断言会使程序退出，并不代表需要你设计你的代码让不符合条件的情况不会发生。然而，在开发阶段，断言是发现这些可能造成不符合条件的情况是非常有效的办法。
		
































