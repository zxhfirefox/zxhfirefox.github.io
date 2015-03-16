---
layout: post
title: "Language Guide: Strings and Characters"
date: 2014-10-02 14:36:24 +0800
comments: true
categories: [Swift]
---

字符串是字符的有序集合，例如，"hello"或者"albatross"。Swift里字符串表示为Swift类型，它也表示一系列Character类型。

Swift的String和Character提供了一套高效的，支持Unicode的处理文本的方法。字符串创建和操作的语法的是简单的，可读性很好，而且和C有着一样的字面语法。连接2个字符串用+把他们加起来就可以了。字符串的可变版本和Swift里其他值一样，由let和var确定。

虽然语法很简单，但是Swift的string类型是一种高效的，现代的字符串实现。每一个字符串都是由编码无关的Unicode字符组合起来的，提供了许多种编码后字符串的访问。

你也可以通过字符串插入，来把字符串，常量，变量插入到更长的字符串里，这样，在创建字符串，打印和存储的时候都非常方便。

	注意
	Swift的String类型和Foundation的NSString类型是无缝桥接的。如果你在使用Foundation的API，那么所有NSSting的API也可以操作String类型，你可以把一个String类型传递给任何需要NSString类型参数的函数。

<!--more-->

#####	字符串字面量

你可以把预先定义好的字符串作为字符串字面量加入到你的代码中，字符串字面量是一个固定长度的字符的组合，然后包裹在“”中。

``` swift
let someString = "some string literal"
```

这里，someString会是String类型，因为他被附值了一个字符串字面量。

#####	初始化一个空字符串

如果需要创建一个空的字符串，可以用字符串字面量或者用字符串实例的初始化方法：

``` swift
var emptyString = ""
var anotherEmptyString = String()
```

使用isEmpty来检查一个字符串是否为空字符串：

``` swift
if emptyString.isEmpty {
	println("Empty")
}
```

#####	字符串可变性

你通过声明一个字符串为变量或常量来说明字符串是否可变。

``` swift
var variableString = "Horse"
variableString += "and carriage"

let constantString = "Highlander"
constantString  += "and carriage" //compile error
```

	注意
	这种方式和ObjC的方式不一样。

#####	字符串是值类型

Swift的字符串是值类型的。如果你创建一个新的字符串，这个字符串值在传递给函数或方法时，或者附值给常量或变量的时候会被拷贝。在每一种情况下，已有字符串都被拷贝一份，新拷贝的值会被传递或是附值，而不是原有的字符串。

	注意
	这种行为和Cocoa里的NSString是不一样的。在Cocoa里，你附值或传递NSString时，如果你不是明确说明，那传递和附值的都是一个引用。


Swift的这种默认拷贝的字符串行为确保了你在函数里使用的字符串是一个新的副本，你可以确认你传到函数里的字符串不会改变。

在底层，Swift做了优化，只有在真正需要拷贝的时候，拷贝才会发生，这意味着你的程序的性能并不会因为字符串是指类型而受到影响。

#####	使用Character

Swift的String类型表示了一系列有序的Character。你可以通过一个for-in循环访问String里的Character。

``` swift
for character in "zxh" {
	println(character)
}
```

另外，你也可以把一个单个字符的字符串常量附值给一个Character类型的常量和变量，你需要提供类型信息。

``` swift
let yenSin: Character = "$"
```

#####	连接字符串和字符

字符串可以用+来连接起来。

``` swift
let str1 = "hello"
let str2 = "there"
var welcome = str1 + str2
```

你可以使用+= 来连接字符串。

``` swift
var instruction = "look over"
instruction += str2
```

使用String的append方法来把一个Character加在字符串后面。

``` swift
exclamationMark: Character = "!"
welcome.append(exclamationMark)
```

	注意
	你不能把String和Character追加到Character，因为Character只包含一个字符。

#####	字符串插入

字符串插入是一种可以把变量，常量，字面量，表达式插入到字符串字面量里来形成一个新的字符串的方式。每一个你插入的内容都由括弧包含，再加上一个 \ 在前面。

``` swift
let mutiplier = 3
let message = "\(mutiplier) times 2.5 is \(mutiplier * 2.5)"
```

在上面的例子里，当字符串插入处理message时，mutiplier的真实值会带入计算出真正的字符串。

	注意
	在字符串插入里写的表达式不能包含未被转意的 " 和 \ ，也不能包含换行和回车

#####	Unicode

Unicode是国际上用来编码，表示和处理常用书写系统里文本的标准。你可以表示几乎所有语言的所有字符，并可以读取或者写入文件或网页。Swift的String和Character都是兼容Unicode的。

######	Unicode标量

在底层，Swift的String是从Unicode标量创建而来。一个Unicode标量是唯一的21位的数字，标示了一个字符或者修改器，例如U+0061表示"a"，U+1F425表示" "。

	注意
	一个Unicode标量是从U+0000到U+D7FF和从U+E000到U+10FFFF的所有Unicode code point。Unicode标量不包含Unicode surrogate pair，也就是从U+D800到U+DFFF。

注意并不是所有的21位的Unicode标量都表示一个字符，有一些被保留以备后用。被附值给字符的标量一般都有名字，例如拉丁小写字母a。

######	字符串字面量里的特殊Unicode

String字面量可以包含下面的特殊Unicode：
*	转意字符 \0, \\, \t, \n, \r, \" \'
* 	任何的Unicode标量，写作\u{n}，n表示从1到8个十六进制字符。

下面的例子展示了String里加入特殊字符。

``` swift
let wisewords = "\" Imagination is more important than knowledge\""
let dollarSign = "\u{24}"
let balckHeart = "\u{2665}"
```

######	扩展的书写位组

Swift里每一个Character都表示一个书写位。一个书写位是一个或一系列Unicode标量组合起来表示一个人类可读的字符。

例如，字母 e可以表示为U+00E9，也可以表示为U+0065和U+0301的组合。

扩展的书写位组可以表示许多复杂的字符为单一的Character值。

。。。

#####	计算字符个数

要得到一个字符串的里Character个数，使用countElements函数。

注意因为Swift里Character是扩展的书写位，所以你修改字符串不一定就会改变Character的长度。例如，你开始写了cafe，然后加上了语气符号，但是Character的长度并没有改变。

``` swift
var word = "cafe"
println("the number of character is \(countElements(word))")
word += \u{301}
println("the number of character is \(countElements(word))")
```

	注意
	扩展书写位组可以包含一个或多个Unicode标量，这意味着不同的字符，同一个字符的不同表示方式，可能需要占用的内存。因此，Character在Swift里并不是每个都占用大小一样的内存。因此字符的个数也不用通过遍历字符串来获得。如果你在处理很长的字符串，注意countElements需要遍历整个字符串来得到结果。
	另外countElements的结果和NSString的length不总是一样的。因为NSString是基于16位编码的。为了表示这个，NSString的length对应着String的utf16Count。

#####	比较字符串

Swift提供了三种比较字符串的方式，字符相同，前缀相同，后缀相同。

######	字符相同

字符相同，通过 == 和 !=来做比较。

两个字符串或字符，如果他们的扩展书写位标准的相同，就认为相同。扩展书写位标准的相同是说他们具有相同的语言意思和外形，即使他们底层是不一样的Uincode标量。

例如，U+00E9和U+0065U+0301是相等的。

相反，U+0041和U+0410是不相等的。

	注意
	Swift里字符串和字符的比较不依赖于地理位置。

######	前缀和后缀相等

查看一个字符串时候含有特定的前缀或后缀，使用hasPrefix和hasSuffix方法。

	注意
	hasPrefix和hasSuffix是一个字符一个字符的比较扩展书写位标准相等性。

######	字符串的Unicode表示

当Unicode字符串写入文件时，这些Unicode标量需要编码。例如uft8，utf16，utf32.

Swift提供了几种访问字符串Unicode表示的方法，可以使用for-in来按照Character的方式访问。另外字符串还有三种表现方式

*	utf8，
* 	uft16,
*  	unicodeScalars














