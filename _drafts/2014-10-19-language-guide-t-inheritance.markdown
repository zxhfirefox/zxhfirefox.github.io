---
layout: post
title: "Language guide: Inheritance"
date: 2014-10-19 00:38:17 +0800
comments: true
categories: [Swift]
---

一个类可以从其他类继承方法，属性和其他特性。当一个类继承另一个类的时候，我们把继承的类叫子类，把被继承的类叫父类。继承是Swift里类区别于其他类型非常重要的一个区别。

在Swift里，类可以调用和访问父类的方法，属性，角标，也可以通过他们自己重写这些方法，属性，角标来实现自己的行为。Swift会通过检查重写定义是否和父类匹配来帮助你检查重写的正确性。

类还可以给父类的属性添加观察者，来使得自己在父类属性变化的时候得到通知。观察者可以添加给任何属性，不论是存储的还是计算的。

<!--more-->

#####   定义一个基类

任何类，如果它没有从其他类继承，那它就是一个基类。

    注意
    Swift的类并不是从一个全局基类继承的。

``` swift
class Vehicle {
    var currentSpeed = 0.0
    var descriptioin: String {
        return "traveling at \(currentSpeed) miles per hour"
    }
    func makeNoise(){
    }
}
```

上面的例子定义了一个Vehicle类。有1个存储属性，1个计算属性，1个实例方法。

``` swift
let someVehicle = Vehicle()
println("Vehicle: \(someVehicle.description)")
```

创建了一个实例，并调用了实例方法。

#####   子类

子类是从一个父类继承的类。子类从父类继承特性，你也可以改进这些特性，也可以添加新的特性。

定义子类的语法如下：

``` swift
class SomeSubClass : SomeSuperClass {
}
```

我们定义一个Vehicle的子类：

``` swift
class Bicycle : Vehicle {
    var hasBasket = false
}
```

Bicycle类继承自Vehicle类，包含Vehicle所有的特性，并添加了自己新的属性hasBasket。

Bicycle也可以被其他类继承。

``` swift
class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}
```

Tandem继承了所有Bicyle的特性，也继承了所有Vehicle的特性。

#####   重写

一个子类为一个从父类继承的方法，属性，角标提供自己的实现，叫做重写。

使用 override关键字来标示重写。这样做可以明确标示出你在重写而不是误操作。如果错误的使用了 override关键字，编译器会报错。

######  访问父类的方法，属性和角标

当你自己重写一个方法，属性或角标时，你或许需要访问父类的实现。例如，你可能只是在父类的实现基础上做一些改进。

对于这种情况，你可以使用 super 来访问父类的方法，属性，角标的实现。例如： super.someMethod(), super.someProperty, super[someIndex]。

######   重写方法

``` swift
class Train: Vehicle {
    override func makeNoise(){
        println("Choo Choo")
    }
}
```

######  重写属性

你可以重写一个父类的属性，或是给一个父类的属性添加观察者。

####### 重写属性的getter和setter

你可以重写任何一个继承的属性，不论是存储的还是计算的。对于子类来说，属性是计算的还是存储的时不知道的，子类只知道他继承了一个特定类型的属性以及它的名字。所有你必须正确的子类里给出类型和名字。

你可以把父类一个只读的属性在子类实现为可读可写的属性，但是你不能把父类一个可读可写的属性重写为一个只读的属性。

    注意
    如果你在重写属性时给出了setter，那你必须也得给出getter。如果你不想修改getter的返回值，可以直接返回super.someProperty。


``` swift
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.descripton + " in gear \(gear)"
    }
}
```

####### 重写属性观察者

你可以通过重写属性来给父类的属性添加观察者。

    注意
    你不能给父类的常量存储属性或只读的计算属性添加观察者。因为这些属性的值不能改变，所以也没有willSet和didSet。
    
    另外，你不即能给一个属性重写getter和setter，还给它添加观察者。

``` swift
class AudomaticCar : Car {
    override var currentSpees: Double {
        didSet {
            gear = Int(currentSpeed / 10.0) + 1
        }
    }
}
```

每当你修改一个AudomaticCar的currentSpeed属性，都会触发这个观察者来给gear设定适当的值。

``` swift
let automatic = AutomaticCar()
automatic.currentSpeed = 35.0
println("AutomaticCar: \(automatic.description)")
```

#####   防止重写

你可以把一个属性，方法，角标标记为 final 来禁止子类重写。

任何尝试重写一个 final 的方法，属性，角标都会造成编译错误。

你可以把整个类标记为 final 来禁止这个类被继承。














