---
layout: post
title: "Language Guide: Subscripts"
date: 2014-10-19 00:37:12 +0800
comments: true
categories: [Swift]
---

类，结构体，枚举可以定义角标。角标是用来访问集合，列表或序列的元素的一种简写。使用角标可以访问和修改元素。例如，你访问数组的元素使用someArray[index]，访问字典的元素使用someDictionary[key]。

我们可以给一个类型添加多个角标，选择哪个角标是由传进来的索引的类型决定。角标不限于一个维度，你可以定义为角标定义多个输入。


<!--more-->

#####   角标语法

角标允许你在实例后面使用方括号来通过特定的值来查询这个实例。他们的语法和方法，属性的很相似。使用subscirpt关键字定义角标，并给出参数和返回值。角标和方法不同，角标可以是可读可写，也可以是只读，这种行为是通过一个类似于计算属性的 getter和 setter来表达。

``` swift
subscript(index: Int) -> Int {
    get {
    }
    set(newValue){
    }
}
```

newValue的类型和角标返回的类型一致。和计算属性一样，你不提供newValue的话，一个默认的newValue会给你。

和只读的计算属性一样，你可以不写get。

``` swift
subscript(index: Int) -> Int {
}
```

例如：

``` swift
struct TimesTable {
    let multiplier: Int
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}
let threeTimesTable = TimesTable(multiplier: 3)
println("six times three is \(threeTimesTable[6])")
```

#####   使用角标

角标的确切含义取决于它使用的上下文。一般用来访问集合，列表或序列的元素。你可以使用角标实现任何适用于你自己类或结构体的方式。

例如Swift的Dictionary实现了角标，使得我们可以使用角标来访问字典里的元素。

    注意
    Swift的Dictionary实现了通过角标获取对应key的value。但是是optional的，因为对应的key可能没有值存在。

#####   角标选项

角标可以接受任何数量，任何类型的参数。角标也可以返回任何值。角标可以使用变量参数，变长参数，但是不能使用in-out参数，也不能有默认值参数。

一个类或结构体可以提供它需要的任意多的角标实现，使用哪个角标实现，由传入的参数类型来决定。这样子多个角标实现的方式叫做角标重载。

最常用的角标是获取一个单个的参数。

``` swift
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(count: rows * columns, repeatedValue: 0.0)
    }
    func indexIsValidForRow(row: Int, column: Int) -> Bool {
        return row >= 0 && row < rows && column >= 0 && column < columns
    }
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValidForRow(row, column: column), "Index out of range")
            return grid[(row * columns) + column]
        }
        set {
            assert(indexIsValidForRow(row, column: column), "Index out of range")
            grid[(row * columns) + column] = newValue
        }
    }
}
```

上面的例子里，Matrix定义了一个需要两个参数的角标来提供元素的访问。

``` swift
var matrix = Matrix(rows: 2, columns: 2)
matrix[0, 1] = 1.5
matrix[1, 0] = 3.2
```

注意grid数组的初始化方式，和角标里作为越界检查的断言。






















