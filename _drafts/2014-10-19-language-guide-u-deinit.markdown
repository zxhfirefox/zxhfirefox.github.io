---
layout: post
title: "Language Guide: Deinitialization"
date: 2014-10-19 00:40:13 +0800
comments: true
categories: [Swift]
---


一个反初始化函数在类实例被销毁之前调用。我们用 deinit关键字来写反初始化函数。反初始化函数只有类类型有。

#####   反初始化函数原理

Swift自动把不在使用的实例销毁，并释放资源。Swift使用ARC来做内存管理。一般来讲，我们不需要手动的释放资源。不过，如果我们自己处理了一些资源，我们可能就需要自己做一些清理工作。

每个类，最多只有一个 deinit 方法。 没有任何参数。

``` swift
deinit {
}
```

<!--more-->

反初始化函数自动在对象销毁前调用。你不能自己手动调用，父类的反初始化函数会被继承，而且在子类的实现末尾自动调用。即使子类不提供反初始化函数，父类的也会的调用。

因为在销毁之前调用反初始化函数，所以在反初始化函数里，所偶的属性都还可以访问。

#####   反初始化函数实战

``` swift
struct Bank {
    static var coinsInBank = 10_000
    static func vendCoins(var numberOfCoinsToVend: Int) -> Int {
        numberOfCoinsToVend = min(numberOfCoinsToVend, coinsInBank)
        coinsInBank -= numberOfCoinsToVend
        return numberOfCoinsToVend
    }
    static func receiveCoins(coins: Int) {
        coinsInBank += coins
    }
}
```

``` swift
class Player {
    var coinsInPurse: Int
    init(coins: Int) {
        coinsInPurse = Bank.vendCoins(coins)
    }
    func winCoins(coins: Int) {
        coinsInPurse += Bank.vendCoins(coins)
    }
    deinit {
        Bank.receiveCoins(coinsInPurse)
    }
}
```

``` swift
var playerOne: Player? = Player(coins: 100)
println("A new player has joined the game with \(playerOne!.coinsInPurse) coins")
// prints "A new player has joined the game with 100 coins"
println("There are now \(Bank.coinsInBank) coins left in the bank")
// prints "There are now 9900 coins left in the bank"
```

``` swift
playerOne!.winCoins(2_000)
println("PlayerOne won 2000 coins & now has \(playerOne!.coinsInPurse) coins")
// prints "PlayerOne won 2000 coins & now has 2100 coins"
println("The bank now only has \(Bank.coinsInBank) coins left")
// prints "The bank now only has 7900 coins left"
```

``` swift
playerOne = nil
println("PlayerOne has left the game")
// prints "PlayerOne has left the game"
println("The bank now has \(Bank.coinsInBank) coins")
// prints "The bank now has 10000 coins"
```

