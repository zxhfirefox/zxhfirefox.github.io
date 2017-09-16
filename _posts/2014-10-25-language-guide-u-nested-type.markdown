---
layout: post
title: "Language Guide: Nested Type"
date: 2014-10-25 10:24:19 +0800
comments: true
categories: [Swift]
---

枚举通常被定义来帮助实现一个类和结构体。同样的，有时候为了在一个更复杂的上下文里定义和使用某一些特殊的类型是很方便的。为了达到这一点，Swift允许定义嵌套类型，这样你就可以在需要枚举，类，结构体来支持的复杂的类型里定义他们。

定义嵌套类型，只需要把类型定义写在大括号里面。你需要嵌套几层就嵌套几层。

<!--more-->

#####	嵌套类型举例

下面的例子定义了一个结构体，BlackjackCard，它代表了一种扑克牌。这个结构里有2个嵌套的类型，叫Suit和Rank。

在 Blackjack里，Ace有2个值，1或11.这个值有一个叫Values的结构体表示，这个结构体嵌套在Rank里。

``` swift
struct BlackjackCard{
	enum Suit: Character{
		case Spades="s",Hearts="h",Diamonds="d",Clubs="c"
	}
	enum Rank: Int {
		case Two = 2, Three, Four, Five, Six, Seven, Eight, Nine, Ten
		case Jack, Queen, King, Ace
		struct Values{
			let first: Int,second: Int?
		}
		var values: Values{
			switch self{
			case .Ace:
				return Values(first:1,second:11)
			case .Jack, .Queen, .King:
				return Values(first:10,second:nil)
			default:
				return Values(first:self.toRaw(),second:nil)
			}
		}
	}
	let rank: Rank,suit: Suit
	var description: String {
		var output = "suit is \(suit.toRaw(),"
		output += " value is \(rank.values.first)"
		if let second = rank.values.second {
			output += " or \(second)"
		}
		return output
	}
}
```

Suit枚举定义了扑克牌里常见的四种花色。

Rank枚举定义了扑克牌的大小。

Rank还嵌套了一个类型Values来表示扑克牌一般只有一个值，除了Ace有两个值。而Values里有一个Int的first和一个optional的Int的sencond。

Rank还定义了一个计算的属性values，返回一个Values类型的值。

BlackjackCard自己有2个属性，suit和rank。还有一个计算的属性，description。

因为BlackjackCard没有自定义初始化函数，所以它有一个默认的成员初始化函数。

``` swift
let theAceOfSpades = BlackjackCard(rank: .Ace,suit: .Spades)
println(theAceOfSpades)
```

尽管Rank和Suit是嵌套在BlackjackCard里的类型，他们的类型依然可以从上下文里推断出来。

#####	引用嵌套类型

如果需要在定义上下文之外的地方使用嵌套类型，需要把外部类型的名字加在嵌套类型的名字的前面：

``` swift
let heartsSymbol = BlackjackCard.Suit.Hearts.toRaw()
```




	

