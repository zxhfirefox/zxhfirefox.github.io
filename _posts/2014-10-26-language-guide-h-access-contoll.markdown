---
layout: post
title: "Language Guide: Access Controll"
date: 2014-10-26 23:27:07 +0800
comments: true
categories: [Swift]
---

访问控制限制了我们从其他源文件和模块的代码访问我们代码能力。这一特性可以让你隐藏你实现的细节，并且给出一个预先定义的接口来暴露可以被访问的代码。

我们可以把访问控制添加到类型级别（类，结构体，枚举），也可以添加到单独的属性，方法，初始化函数和角标。协议可以被限制到特性的上下文，就像全局常量，变量和函数。

除了增加不同程度的访问限制，Swift通过特定环境给出特定默认访问级别来减少了需要明确给出访问级别的代码。事实上，如果我们在写一个单目标（single-target）的app，我们或许永远都不需要明确给出访问限制。

    注意
    我们代码里可以被添加访问限制的内容（属性，类型，方法等等）在下文中都被引用为“个体”（entity）。

<!-- more -->

### 模块和源文件

Swift的访问控制模型是基于模块和源文件的概念。

一个模块（module）是代码分发的一个单一单元，一个框架或一个app可以作为一个单一单元编译，它可以被其他模块使用import来导入。

在Xcode里的每一个编译目标都在Swift里作为分开的模块对待。如果我们把我们app的部分代码分开组织成一个独立的框架（或许是为了封装，然后可以在多个app里使用），那么所有在这个框架内的定义都会作为这个独立模块的一部分，然后就可以被导入到这个app和其他app内使用。

一个源文件是在一个模块里的一个单独的Swift源代码文件。尽管我们一般在一个源文件里只定义一个类型，但是单个源文件可以定义多个类型。

### 访问级别

Swift提供了三种访问级别。这些级别和个体（entity）定义的源文件有关，也和这些源文件属于的模块有关。
    *   public，允许在他们自己定义的模块里的任何源文件都可以使用，并且那些导入了这个模块的任何模块也能使用。通常，我们使用public来提供框架的公开API。
    *   internal，允许在他们自己定义的模块里的任何源文件都可以使用，但是不能被模块外的源文件使用。通常，我们使用interval来提供app或框架内部使用的结构。
    *   private，限制了只有在同一个源文件内可以使用。使用private来保护代码的实现细节。

public是最高的访问级别，private是最低的访问级别

####    访问级别指引原则

Swift的访问级别有一个总领的原则：没有个体的访问级别可以定义成其他的更低的访问级别。

例如：
    *   一个public的变量不能被定义成一个internal或private类型。因为这个类型或许不能在所有地方被访问到。
    *   一个函数不能有比他们参数和返回值更高的访问街边，因为函数可能在他的组成类型不可用的时候被调用。

####    默认访问级别

所有的个体（除了极少数），在你不明确给出访问级别时，默认的访问级别都是internal。这样，很多情况下你都不用给出访问级别。

####    单目标app的访问级别

当我们在写一个单目标的app时，所有app内的代码应该只能在app内访问。默认的访问级别internal已经满足这一要求。所以，我们不用特别给出访问级别。但是，我们或许在某些地方需要把一些实现定义为private。

####    框架的访问级别

当我们开发框架时，我们需要把框架的公开API定义成public的。

    注意
    任何的框架内的实现都还是默认的internal，或是private。我们仅仅把公开的API设定为public

### 访问控制语法

我们使用public，internal，private关键字来表示访问控制。

``` swift
public class SomePublicClass {}
internal class SomeInternalClass {}
private class SomePrivateClass {}
 
public var somePublicVariable = 0
internal let someInternalConstant = 0
private func somePrivateFunction() {}
```

如果我们不需要特别给出，使用默认的internal时，我们就可以不写。

``` swift
class SomeInternalClass {}              // implicitly internal
var someInternalConstant = 0            // implicitly internal
```

### 自定义类型

当我们需要给自定义类型设置访问控制级别时，在定义时的时候给出即可，这样这些类型就可以在访问控制允许的地方被使用。

类型的访问控制级别也会影响它的成员（属性，方法，初始化函数，角标）的默认访问级别。如果类型的访问级别是private，那么所有成员都是private。如果类型的访问级别是internal或public，成员的默认访问级别就是internal。

    注意
    如上所述，public的类型的方法默认是internal的，不是public的。如果我们希望某个成员是public，需要明确指出。

``` swift
public class SomePublicClass {          // explicitly public class
    public var somePublicProperty = 0    // explicitly public class member
    var someInternalProperty = 0         // implicitly internal class member
    private func somePrivateMethod() {}  // explicitly private class member
}
class SomeInternalClass {               // implicitly internal class
    var someInternalProperty = 0         // implicitly internal class member
    private func somePrivateMethod() {}  // explicitly private class member
}
private class SomePrivateClass {        // explicitly private class
    var somePrivateProperty = 0          // implicitly private class member
    func somePrivateMethod() {}          // implicitly private class member
}
```

####    元组类型

元组的访问级别是它的所有元素中访问级别最低的元素的访问级别。例如，如果一个元组含有2个不同类型的元素，一个internal，一个private，那么这个元组的访问级别就是private。

    注意
    元组的访问级别并不和类，结构体，枚举，函数一样是单独定义的。它的访问级别实在使用时推断出来的，不能显式的定义。

####    函数类型

函数的访问级别是通过计算它的参数和返回值类型的最低访问级别决定的。如果函数计算后的访问级别和上下文默认的不一致，我们需要明确给出函数的访问级别。

``` swift
func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // function implementation goes here
}
```

这个全局函数，没有明确给出访问级别，如果我们认为他是internal的，那就错了，事实上，这段代码无法编译。

这个函数的返回类型是一个元组，元组的元素一个是internal的类，一个是private的类。那么元组的访问级别就是private。

因为元组的访问级别是private的，我们需要把函数标示为private才能编译通过。

``` swift
private func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // function implementation goes here
}
```

我们不能把这个函数标记为public或是默认的internal。因为这个函数使用者无法使用private的返回值。

####    枚举类型

枚举的各种情况自动和枚举的访问级别一致。我们没法给枚举的case单独给出访问级别。

``` swift
public enum CompassPoint {
    case North
    case South
    case East
    case West
}
```

上面定义这个枚举和他的case都是公开的。

####    原始值和关联值

枚举使用任何原始值和关联值的访问级别都必须枚举的访问级别要高。我们不能在一个internal的枚举里使用private的原始值或关联值。

####    嵌套类型

定义在private里的嵌套类型默认是private的。

定义在public里的嵌套类型默认是internal的。

如果我们希望一个在public里的嵌套类型是public的，我们明确给出。

### 子类

我们可以继承从当前上下文可以访问的任何类。子类的访问级别不会高于父类的，例如，我们无法继承自一个internal的，自身是public得类。

另外，我们可以重写任何的在上下文可以访问的父类的成员（方法，属性，初始化函数，角标）。

重写可以让子类的元素比父类更加可被访问。下面的例子里，public的类A，定义了private的方法someMethod，但是，子类B把someMethod重写成了internal。

``` swift
public class A {
    private func someMethod() {}
}
internal class B: A {
    override internal func someMethod() {}
}
```

甚至于子类的成员可以去调用父类的更低的访问许可的成员，只要这个父类的调用在上下文里是允许的（意思是，在一个源文件里，调用父类的private犯法，或是在一个模块里，调用父类的internal方法）。

``` swift
public class A {
    private func someMethod() {}
}
internal class B: A {
    override internal func someMethod() {
        super.someMethod()
    }
}
```

因为类A和B定义在一个源文件里，所以B的someMethod可以调用A的private的someMethod。

### 常量，变量，属性和角标

一个常量，变量，属性不能比他们的类型更加的公开。例如，我们不能给一个public的属性写一个private类型。同时，角标也不能比索引类型或返回类型更加公开。

如果一个常量，变量，属性或角标使用了private的类型，那么他们自己也必须是private的。

``` swift
private var privateInstance = SomePrivateClass()
```

####    Getter和Setter

常量，变量，属性和角标的Getter和Setter自动的和他们属于的常量，变量，属性和角标的访问级别一样。

我们可以给变量，属性，角标比他们的对应的getter更低的setter来限制写。通过在var和subscript前面写internal(set)和private(set)。

    注意
    注意规则既可以在存储属性上使用，也可以在计算属性上使用。即使我们没有给存储属性明确的给出一个getter和setter。Swift会自动生成他们。

``` swift
struct TrackedString {
    private(set) var numberOfEdits = 0
    var value: String = "" {
        didSet {
            numberOfEdits++
        }
    }
}
```

TrackedString结构体的存储属性numberOfEdits被设置为private(set)。而value和TrackedString本身都是默认的internal。numberOfEdits的getter依然是默认的internal。

``` swift
var stringToEdit = TrackedString()
stringToEdit.value = "This string will be tracked."
stringToEdit.value += " This edit will increment numberOfEdits."
stringToEdit.value += " So will this one."
println("The number of edits is \(stringToEdit.numberOfEdits)")
// prints "The number of edits is 3"
```

事实上，如果需要我们可以显式给出getter和setter的访问级别。

``` swift
public struct TrackedString {
    public private(set) var numberOfEdits = 0
    public var value: String = "" {
        didSet {
            numberOfEdits++
        }
    }
    public init() {}
}
```

### 初始化函数

自定义的初始化函数的访问级别必须少于或等于他们的类型。除了需要的初始化函数，一个需要的初始化函数必须和它的类型有着同样的访问级别。

和函数和方法一样，初始化函数的参数的访问级别不能比初始化函数更低。

####    默认初始化函数

Swift会给任何的结构体和基类提供一个没有任何参数的默认初始化函数，只要这些结构体或基类的所有属性都有默认值，并且自己没有定义任何的初始化函数。

默认初始化函数的访问级别和类型的一致，除非类型是public的。如果类型是public的，默认初始化函数就是internal的。如果我们需要一个public的，我们需要自己定义。

### 协议

我们可以给协议也设置访问级别，这样可以限制实现协议的类型。

协议里所有的要求的访问级别和协议一致。

    注意
    如果我们把协议定义为public，那协议的所有要求都必须是public的实现。这一点和其他类型不一样，一个public的类型可能有internal的成员。

####    协议继承

如果我们继承一个协议，那么新的协议至少要和已有协议一样的访问限制。我们不能继承一个internal的协议而定义成public的。

####    协议实现

类型可以实现比自己访问级别更低的协议。例如，我们定义一个public类型，然后可以实现一些internal的协议。

类型实现协议后的上下文是类型访问级别和协议访问级别的最小值。例如，如果类型是public，协议是internal，那么类型的协议实现就是internal。

如果我们写或者扩展一个类型来实现一个协议，我们必须确保类型对于协议要求的实现至少要和类型对于协议实现的访问级别一致。例如，如果一个public类型要实现一个internal的协议，那这个类型对于协议要求的实现至少要是internal的。

    注意
    在Swift，和ObjC一样，协议实现是全局的，我们不可能在一个程序里，让一个类型通过两种不同的方式实现一个协议。

### 扩展

我们可以在当一个类，结构体，枚举可以被访问的时候扩展他们。在扩展里添加的任何成员都和原始成员一样有默认的访问级别。例如，如果我们扩展一个public类型，任何新添加的成员的默认访问级别都是internal。

但是，我们可以给扩展设置一个访问级别来修改所有扩展里添加的成员的访问级别。这个新设置的访问级别依然可以被单个成员的访问级别所覆盖。

####    在扩展中实现协议

如果一个扩展是用来实现协议，那么我们不能在这样扩展中重新给出扩展的访问级别。相反，协议本身的访问级别被用来给扩展里的新成员提供默认访问级别。

### 泛型

泛型函数和泛型类型的访问级别是他们本身和类型参数的访问级别的最小值。

### 类型别名

对于访问级别，任何的类型别名都被认为是独立的类型。一个类型别名可以有比原始类型更低或相等的访问级别。例如，一个私有类型别名可以作为一个private，internal，public的类型的别名，但是一个public的类型别名不能做为一个internal或private的类型的别名。

    注意
    这一规则在实现协议时使用的关联类型同样适用。













