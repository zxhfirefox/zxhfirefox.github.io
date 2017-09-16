---
layout: post
title: "Using Swift with Cocoa and Objective-C: Interoperability-Working With Cocoa Data Types"
date: 2014-11-17 23:17:29 +0800
comments: true
categories: [swift]
---

作为与ObjC互用性的一部分，Swift提供了方便和高效的方法来处理Cocoa的数据类型。

Swift自动的在Swift和ObjC之间互相转换一些类型。还有一些类型是Swift和ObjC通用的。这两种数据类型一般叫做桥接数据类型(bridged data types)。例如，我们可以把Array传递给一个需要NSArray的方法。我们也可以在桥接类型之间互相转换，用 as 或是直接给出变量或常量的值。

Swift也提供了一些方便的包裹来使得我们可以方便，统一和Foundation的数据类型交互。

<!--more-->

### 字符串

Swift会自动在String和NSString之间转换。这意味着在任何时候你需要一个NSString的时候，你都可以直接给一个String。这样我们即可以使用Swift的字符串插入(string interpolation)，也可以使用Swift和NSString提供的众多API。所以，我们基本上不需要在我们的代码里使用NSString。事实上，当Swift导入ObjC的API时，它把所有的NSString都替换成了String，而当ObjC使用Swift时，所有的String都替换成了NSString。

要打开字符串桥接(bridging)，只需要导入Foundation。

``` swift
import Foundation
let greeting = "hello, world!"
let capitalizedGreeting = greeting.capitalizedString
// capitalizedGreeting: String = Hello, World!
```

如果我们确实需要使用NSString对象，我们可以把一个String转换到NSString。因为String和NSString总是可以互相转换，所以我们不需要使用 as?，而是直接使用 as。

``` swift
import Foundation
let myString: NSString = "123"
if let integerValue = (myString as String).toInt() {
    println("\(myString) is the integer \(integerValue)")
}
```

####    本地化

在ObjC里，我们使用NSLocalizedString系列的宏来本地化字符串。这一系列宏包括了NSLocalizedString，NSLocalizedStringFromTable，NSLocalizedStringFromTableInBundle，NSLocalizedStringWithDefaultValue。在Swift里，我们使用一个函数来处理所有这些：NSLocalizedString(key:tableName:bundle:value:comment:)。这个函数的tableName，bundle，value有默认值。

### 数字

Swift自动在特定的数字类型之间互相桥接。例如Int，Float和NSNumber。

``` swift
let n = 42
let m: NSNumber = n
```

我们也可以在需要NSNumber的地方传入一个Int。注意，因为NSNumber可以包含很多类型的值，所以我们不能随便传递其他类型的值。

下面的类型都会自动桥接到NSNumber：
    *   Int
    *   UInt
    *   Float
    *   Double
    *   Bool

### 集合类

Swift自动把NSArray和NSDictionary和他们Swift对应的相互转换。这样我们就即可以使用Swift的优秀算法和语法，也可以使用Foundation和Swift的集合互相交互。

####    数组

Swift自动在Array和NSArray之间互相转换。当我们把NSArray转换到Swift数组时，结果的类型是[AnyObject]。一个AnyObject的对象和OjbC或Swift的对象都兼容，或者可以被桥接到另外一个。我们可以把任何NSArray对象桥接到Swift数组，因为所有的ObjC对象都和AnyObject兼容。因为这个，所以Swift编译器在导入ObjC的API时会把所有NSArray替换成[AnyObject]。

在我们把NSArray桥接到Swift数组后，我们可以进一步把它向下转换到特定的类型。不像把NSArray转换到[AnyObject]，把AnyObject转换到特定类型不是一定成功的。编译器知道运行时才能确定数组里的元素是否能成功转换到特定类型。所以，把[AnyObject]转换到[SomeType]返回一个optional的值。

``` swift
let swiftArray = foundationArray as [AnyObject]
if let downcastedSwiftArray = swiftArray as? [UIView] {
    // downcastedSwiftArray contains only UIView objects
}
```

我们也可以在for-in循环里直接转换：

``` swift
for aView in foundationArray as [UIView] {
    // aView is of type UIView
}
```

    注意
    这里是强制转换，如果不成功，会造成运行时错误。

当我们把一个Swift数组转换到NSArray时，Swift数组的元素必须是AnyObject兼容的类型。例如，一个数组[Int],包含Int，但是Int不是类，所以和AnyObject不兼容，但是，Int会自动桥接到NSNumber，所以最后Int是AnyObject兼容的。因此，我们可以把一个[Int]的数组转换到NSArray。如果一个Swift数组里的元素不是AnyObject兼容的，那么转换时会造成运行时错误。

我们也可以直接从Swift数组字面量来创建一个NSArray，这种方式也遵循上面的规则。

``` swift
let schoolSupplies: NSArray = ["Pencil", "Eraser", "Notebook"]
// schoolSupplies is an NSArray object containing NSString objects
```

当我们在ObjC中使用Swift的类或协议时，导入器会把所有的Swift数组类型转换到NSArray。如果给Swift的API提供了一个NSArray，但是元素类型不对，会造成运行时错误，如果一个Swift的API返回一个Swift的数组，却不能转换到NSArray，也会造成运行时错误。

####    字典

除了数组，Swift还会在Dictionary和NSDictionary之间互相转换。当我们把一个NSDictionary转换到Dictionary的时候，结果的类型是[NSObject: AnyObject]。我们可以把任何的NSDictionary转换到Dictionary，因为所有的ObjC的类都是AnyObject兼容的。注意，一个对象是AnyObject兼容的意思是他是一个ObjC或Swift的类，或者它可以被转到一个这样的。所有的NSDictionary都可以转到Dictionary，所以Swift编译器在导入ObjC的API时把所有的NSDictionary都转到了[NSObject: AnyObject]。相似的，当我们在ObjC中使用Swift的类或协议时，导入器也会把所有的Swift字典转换到NSDictionary。

在把一个NSDictionary转到到Swift的字典后，我们可以进一步向下转换到一个更具体的类型。就和向下转换数组一样，向下转换字典不保证一定成功。所以向下转换[NSObject: AnyObject]返回的时一个optional。

当我们做反向转换时，就是把Swift转到ObjC。key和value都必须是类的实例，或是可以转到一个类的实例。

我们也可以使用Swift的字典字面量来创建NSDctionary，和上面的规则一样。

### Foundation数据类型

Swift定义了一个方便的封装让我们来使用Foundation的数据类型。我们使用这一层封装来使用类似于NSSize和NSPoint的类型，语法和Swift其他部分的语法自然且统一。

``` swift
let size = NSSize(width: 20, height: 40)
```

这层封装让我们调用方法也很自然：

``` swift
let rect = CGRect(x: 50, y: 50, width: 100, height: 100)
let width = rect.width    // equivalent of CGRectGetWidth(rect)
let maxY = rect.maxY      // equivalent of CGRectGetMaxY(rect)
```

Swift把NSUInteger和NSInteger都转到Int。Foundation里的这两个类型都转到了Int。在Swift里使用Int是保持一致性，但是需要的时候我们也可以用UInt。

### Foundation函数

NSLog函数在Swift依然可用，打印到系统console。

``` swift
NSLog("%.7f",pi)
```

Swift也还有print和println函数。这些函数也很简单，强大，而且因为Swift的字符串插入而更通用。他们不会打印到系统的console但是可以用于更通用的打印需求。

NSAssert，在Swift不能使用，相反，我们使用assert函数。

### Core Foundation

Core Foundation类型会被自动导入为完整的Swift类。由于内存管理注解已经给出，Swift会自动管理Core Foundation的对象的内存，包括我们自己创建的Core Foundation对象。在Swift里，我们可以使用Foundation和Core Foundation互相桥接的对象。我们也可以把Core Foundation的一些对象先转换到Foundation对象，再转换到Swift标准库的对象。

####    重新映射的类型

当Swift导入Core Foundation的对象的时候，编译器会重新映射他们的名字。编译器去掉了名字后面的Ref，因为Swift类本来就是引用类型。

Core Foundation的CFTypeRef完全的被重新映射到AnyObject。所有我们使用CFTypeRef的地方，我们都应该使用AnyObject。

####    内存被管理的对象

从有注解的API返回的Core Foundation对象的内存由Swift管理，我们不需要去调用CFRetain，CFRealease或者CFAutorelease。如果我们自己的C函数或ObjC方法放回了Core Foundation对象，我们必须要函数或方法注解为CF_RETURNS_RETAINED或者CF_RETURNS_NOT_RETAINED。这样，编译器就能自动为我们插入正确的内存管理函数。如果我们仅仅使用有注解的API，那下面的部分就可以略过，否则，我们需要继续学习内存未被管理的对象。

####    内存未被管理的对象

当Swift导入的API没有被注解，那么编译器就无法插入正确的内存管理代码。Swift会把这些返回的Core Foundation对象封装到一个Unmanaged<T>的结构体。所有的非直接返回Core Foundation对象的也都是内存未被管理的。

``` swift
//ObjC
CFStringRef StringByAddingTwoStrings(CFStringRef string1, CFStringRef string2)
```

Swift会把它导入为：

``` swift
func StringByAddingTwoStrings(CFString!, CFString!) -> Unmanaged<CFString>!
```

当我们碰到了一个内存尾部管理的对象，我们在使用之前应该把它转换为内存被管理的对象。这样，我们就不用管理内存了。Unmanaged<T>有两个方法来把未被管理的对象转换到被管理的对象takeUnretainedValue()和takeRetainedValue()。两个方法都返回了原始的对象，我们根据我们调用的API是否返回一个unretained或是retained的对象。

假设上面的例子里的函数在返回时没有retain返回值。在使用之前，我们就该调用takeUnretainedValue()来拿到原始值。

``` swift
let memoryManagedResult = StringByAddingTwoStrings(str1, str2).takeUnretainedValue()
// memoryManagedResult is a memory managed CFString
```

我们也可以在一个内存未被管理的对象上调用retain()，release()，autorealease()，但是这样并不推荐。

