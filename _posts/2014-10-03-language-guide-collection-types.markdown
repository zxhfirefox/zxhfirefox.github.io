---
layout: post
title: "Language Guide: Collection Types"
date: 2014-10-03 14:37:37 +0800
comments: true
categories: [Swift]
---

Swift提供了两种容器类型，数组和字典。数组相同类型对象的有序列表。字典存储相同类型的无序的值的组合，每个值都可以通过一个标示符（键）来查询。

Swift里数组和字典对他们所包含的值的类型是知道的。这意味着，你不能插入不同类型的值。这也意味着，当你从他们中取值时，取到的值的类型也是确定的。

	注意
	数组和字典底层是通过泛型容器来实现的。
	
	
<!--more-->

#####	容器的可变性

如果你把一个容器附值给一个变量，它就是可变的，意味着你可以添加，删除，替换它里面的元素。相反，如果你把一个容器附值给一个常量，它就是不可变的，它的大小和内容就不能被改变。

	注意
	如果可能，应该尽可能的声明不可变容器。

#####	数组

数组有序的存储相同类型的值。相同的值可以出现多次。

Swift数组可以存储的值的类型是有限定的，不像ObjC的NSArray和NSMutableArray一样，他们不知道自己里面存的是什么类型的值。Swift数组对于它存储的值总是明确的，要么是明确写出类型，要么通过类型推断得到。这个类型不一定是类，可以是Int这样的类型。

######	数组类型简写语法

数组的类型，写全了是Array<SomeType>，也可以简写为[SomeType]。两者是一样的。

######	数组字面量

我们可以通过数组字面量来创建一个数组。数字字面量就是一系列值，用逗号隔开，包含在一组方括号里。

[value1, value2,value3]

``` swift
var shoppingList = ["apple","orange"]
```

######	访问和修改数组

通过数组的属性，方法，和角标可以访问数组的元素。

计算数组有多少元素，用count方法。

``` swift
println("the shopping list has \(shoppingList.count) items")
```

用isEmpty属性来判断count是否为0.

``` swift
if shoppingList.isEmpty {
	println("shopping list is empty")
}
```

通过append方法来给数组添加元素。

``` swift
shoppingList.append("banana")
```

或者，使用 += 来添加一个其他的数组的所有元素。

``` swift
shoppingList += ["book"]
shoppingList += ["pen", "butter"]
```

使用角标来获取数组的某个元素。

``` swift
var firstItem = shoppingList[0]
```

注意数组从0开始。

你也可以使用角标来修改数组的元素

``` swift
shoppingList[0] = "eggs"
```

还可以使用角标来批量修改数组某个范围的所有元素。

``` swift
shoppList[2...4] = ["display","computer"]
```

	注意
	不能通过角标来给数组添加元素，如果你的角标超出了数组元素的个数，就是运行时错误。

使用insert（atIndex：）方法来在特定的位置插入元素

``` swift
shoppingList.insert"flower", atIndex:0)
```

同样的，使用removeAtIndex来删除某个特定位置的元素

``` swift
let flower = shoppingList.removeAtIndex(0)
```

删除造成的任何空缺都会被处理掉，所以数组的第一个元素已经改变。

你可以使用removeLast来删除最后一个元素。

######	遍历一个数组

使用for-in来遍历整个数组

``` swift
for item in shoppList{
	println(item)
}
```

如果你需要每个元素的索引，那么使用全局幻术enumerate来取代数组，enumerate函数会返回一个索引和元素的二元组。

``` swift
for (index,item) in enumerate(shoppingList) {
	println("Item\(index+1): \(item)")
}
```

######	创建和初始化数组

用下面的语法来创建一个特定类型的空数组

``` swift
var someInts = [Int]()
```

另外，如果上下文已经给出了类型信息，那么可以直接用[]来创建数组。

``` swift
someInts.append(3)
someInts = []
```

Swift的数组还提供了可以创建确定长度，并给出元素初始值的初始化函数。

``` swift
var threeDoubles = [Double](count:3,repeatedValue:0.0)
```

用 + 可以把两个数组合并为一个数组。

#####	字典

字典是用来存储相同类型的值的容器，每个值都有一个唯一的key，作为这个值的标示。和数组不同，字典的元素是无序的。使用唯一的key来获取对应的值。

	注意
	Swift的字典对于类型也是明确的，和ObjC的NSDictionary和NSMutableDictionay不同。

######	字典类型简写

字典的类型，写全了是Dictionary<KeyType,ValueType>。可以简写为[KeyType : ValueType]，两者是一样的。

######	字典字面量

可以通过字典字面量来创建字典，和数组很相似。

[key1 : value1, key2 : value2, key3 : value3]

``` swift
var airports = ["TYO" : "Tokyo", "DUB" : "Dublin"]
```

######	访问和修改字典

通过方法，属性，或角标来访问和修改字典。字典内元素的个数用count计算。

``` swift
println("The airports dictionary has \(airports.count) items")
```

使用isEmpty来判断count是否为0.

``` swift
if airports.isEmpty {
	println("empty")
}
```

通过角标来添加元素。

``` swift
airports["LHR"] = "London"
```

也可以通过角标来改变元素。

``` swift
airports["LHR"] = "London Heathrow"
```

也可以使用updateValue（forKey：）来添加或更新值。和角标一样，如果没有旧的值，就添加，如果有，则更新。但是，和角标不一样，updateValue会返回旧的值，你可以通过这个判断是做了更新还是添加。

updateValue返回一个optional，所以你需要检查是否为nil。

你也可以用角标来获取某个key对应的值。但是因为这个值可能不存在，所以返回的是一个optional的值。使用的时候需要判断是否为nil。

``` swift
if let name = airports["DUB"] {
	println("the name is \(name)")
}else{
	println("no airport")
}
```

可以使用角标来删除某个key对应的元素。

``` swift
airports["APL"] = "Apple"
airports["APL"] = nil
```

另外，也可以使用removeValueForKey来删除键值对。如果有值，返回删除的值，如果没有，返回nil。

######	遍历字典

使用for-in来遍历字典，每次返回的时一个（key，value）的二元组。

``` swift
for (code, name) in airports {
	println("\(code) : \(name)")
}
```

你可以通过字典的keys或values属性来单独获得keys和values。

如果你需要把keys和values传递给一个需要数组的api，那么你需要用keys和values来创建一个数组。

``` swift
let codes = [String](airports.keys)
let names = [String](airports.values)
```

	注意
	因为字典是无序的，那么获取keys，values或者key-value对的时候的顺序是不确定的。

######	创建一个空的字典。

``` swift
var namesOfInts = [String:Int]()
```

在上下文已知类型的时候可以使用 [ : ]来创建空的字典。

``` swift
namesOfInts = ["ten":10]
namesOfInts = [:]
```

######	字典Key类型的哈希值

一个类型要想作为字典的key，必须是可以哈希的。一个哈希值就是对于所有对象的一个值，用来判断两个值是否相等。

Swift的所有基础类型，String，Int，Double，Bool都默认有哈希值。没有关联值的枚举成员值默认也有哈希值。

	注意
	你可以通过实现Hashable协议来让你自定义的值可以作为字典的key来使用。实现这个协议，必须定义一个Int类型的hashValue属性，还必须实现==操作符。


















