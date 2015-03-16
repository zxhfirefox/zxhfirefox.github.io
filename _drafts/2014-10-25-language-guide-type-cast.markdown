---
layout: post
title: "Language Guide: Type Casting"
date: 2014-10-25 10:23:07 +0800
comments: true
categories: [swift]
---

类型转换是一种检查实例类型的方法，并且或者把这个实例作为它的类别的父类或子类来使用。

Swift里类型转换用is和as操作符。这两个操作符提供了简单的方式来检查一个值的类型或者把一个值转换到另一个类型。

我们也可以用类型转换来检查一个类型是否实现了一个协议。

<!-- more -->

### 定义一个类继承树

我们需要在类继承里检查一个实例是否是某个类的实例，并把它转换到同一个继承树里的其他类。

``` swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}
class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}
class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}
```

我们定义了三个类，一个父类，两个子类。

``` swift
let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
]
// the type of "library" is inferred to be [MediaItem]
```

我们又定义了一个数组，里面包含了一些实例。注意，这个数组的类型是[MediaItem]。当我们遍历数组时，得到的对象也是MediaItem,我们需要向下转换到具体的类。

### 检查类型

使用 is 操作符来检查实例是否是某个特定类的实例。如果是就返回true，如果不是就返回false。

``` swift
var movieCount = 0
var songCount = 0
for item in library {
    if item is Movie {
        ++movieCount
    } else if item is Song {
        ++songCount
    }
}
println("Media library contains \(movieCount) movies and \(songCount) songs")
// prints "Media library contains 2 movies and 3 songs"
```

上面的代码，我们遍历整个数组，判断每个实例的类型，然后分别计算出有多少电影，多少歌曲。

### 向下转换（Downcasting）

一个类型的变量或常量，事实上有可能指向一个这个类的子类。我们可以尝试使用as操作符来尝试向下转换。

因为向下转换可能失败，所以转换操作符有两种形式。as？，返回一个optional。as，尝试转换并强制解包。

当我们不确定实例的类型能否转换成功时，使用as？来做。如果失败了，会返回一个nil。

只有当我们确认实例的类型能够转换成功时，才使用as来做。如果使用了as，却转换失败，会造成运行时错误。

``` swift
for item in library {
    if let movie = item as? Movie {
        println("Movie: '\(movie.name)', dir. \(movie.director)")
    } else if let song = item as? Song {
        println("Song: '\(song.name)', by \(song.artist)")
    }
}
// Movie: 'Casablanca', dir. Michael Curtiz
// Song: 'Blue Suede Shoes', by Elvis Presley
// Movie: 'Citizen Kane', dir. Orson Welles
// Song: 'The One And Only', by Chesney Hawkes
// Song: 'Never Gonna Give You Up', by Rick Astley
```

上面的代码中，我们尝试把每个实例向下转换到具体的类型。使用as？，然后使用optional绑定来使用真实的值。

    注意
    转换并不会修改实例或它的值。底层的实例没有改变，只是简单的被当做另一个类型来使用。

### Any和AnyObject类型转到

Swift提供了两种比较宽泛的类型：

*   AnyObject可以表示任何类类型的实例
*   Any可以表示任何类型，包括函数类型

    
        注意
        仅当需要时在使用Any和AnyObject，尽可能的明确出实例的类型。

####    AnyObject

当使用Cocoa的API时，我们经常接收到一个数组类型是[AnyObject]的数组，这是因为ObjC没有指定类型的数组。然而，我们事实上通过API的信息是比较确定数组里元素的类型的。

在这种情况下，我们可以使用as来直接把数组元素向下转换到特定的类型来使用。

``` swift
let someObjects: [AnyObject] = [
    Movie(name: "2001: A Space Odyssey", director: "Stanley Kubrick"),
    Movie(name: "Moon", director: "Duncan Jones"),
    Movie(name: "Alien", director: "Ridley Scott")
]
```

因为数组里全是Moive类型的实例，所以我们可以直接使用as。

``` swift
for object in someObjects {
    let movie = object as Movie
    println("Movie: '\(movie.name)', dir. \(movie.director)")
}
// Movie: '2001: A Space Odyssey', dir. Stanley Kubrick
// Movie: 'Moon', dir. Duncan Jones
// Movie: 'Alien', dir. Ridley Scott
```

我们甚至可以直接把数组向下转换到[Movie]来操作：

``` swift
for movie in someObjects as [Movie] {
    println("Movie: '\(movie.name)', dir. \(movie.director)")
}
// Movie: '2001: A Space Odyssey', dir. Stanley Kubrick
// Movie: 'Moon', dir. Duncan Jones
// Movie: 'Alien', dir. Ridley Scott
```

####    Any

下面的例子里，我们使用Any来操作一个混合类型的数组，可以包括函数类型，和非类类型。

``` swift
var things = [Any]()
things.append(0)
things.append(0.0)
things.append(42)
things.append(3.14159)
things.append("hello")
things.append((3.0, 5.0))
things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
things.append({ (name: String) -> String in "Hello, \(name)" })
```

数组thing包含了两个Int，两个Double，一个String，一个元组，一个Movie，一个闭包。

我们可以使用is和as用一个switch来处理这个数组：

``` swift
for thing in things {
    switch thing {
    case 0 as Int:
        println("zero as an Int")
    case 0 as Double:
        println("zero as a Double")
    case let someInt as Int:
        println("an integer value of \(someInt)")
    case let someDouble as Double where someDouble > 0:
        println("a positive double value of \(someDouble)")
    case is Double:
        println("some other double value that I don't want to print")
    case let someString as String:
        println("a string value of \"\(someString)\"")
    case let (x, y) as (Double, Double):
        println("an (x, y) point at \(x), \(y)")
    case let movie as Movie:
        println("a movie called '\(movie.name)', dir. \(movie.director)")
    case let stringConverter as String -> String:
        println(stringConverter("Michael"))
    default:
        println("something else")
    }
}
// zero as an Int
// zero as a Double
// an integer value of 42
// a positive double value of 3.14159
// a string value of "hello"
// an (x, y) point at 3.0, 5.0
// a movie called 'Ghostbusters', dir. Ivan Reitman
// Hello, Michael
```

    注意
    在switch里我们使用as而不是as？来检查和转换类型。在switch里这样做总是安全的。



    






















