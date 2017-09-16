---
layout: post
title: "Using Swift with Cocoa and Objective-C: Interoperability-Interacting with C APIs"
date: 2014-11-17 23:19:56 +0800
comments: true
categories: [swift]
---

作为和ObjC互用性的一部分，Swift保留了一部分和C兼容的类型和特性。Swift也提供了一种和通用的C结构和模式的方法。

<!--more-->

### 基础类型

Swift提供了和C一样的基础整数类型。然而，Swift并不提供默认的这些类型和Swift类型的自动转换，所以谨慎使用。

    C Type              Swift Type
    bool                CBool
    char,signed char    CChar
    unsigned char       CUnsignedChar
    short               CShort
    unsigned short      CUnsignedShort
    int                 CInt
    unsigned int        CUnsignedInt
    long                CLong
    unsigned long       CUnsignedLong
    long long           CLongLong
    unsigned long long  CUnsignedLongLong
    wchar_t             CWideChar
    char16_t            CChar16
    char32_t            CChar32
    float               CFloat
    double              CDouble

### 枚举

Swift会把所有的使用NS_ENUM的C枚举导入成一个Swift的枚举类型。这意味着枚举值的前缀会被截掉。

``` objc
//ObjC
typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
   UITableViewCellStyleDefault,
   UITableViewCellStyleValue1,
   UITableViewCellStyleValue2,
   UITableViewCellStyleSubtitle
};
```

到了Swift就变成了：

``` swift
//Swift
enum UITableViewCellStyle: Int {
    case Default
    case Value1
    case Value2
    case Subtitle
}
```

当我们引用一个枚举值时，在值的前面加一个 .

``` swift
let cellStyle: UITableViewCellStyle = .Default
```

Swift也会导入被标记为NS_OPTIONS的options枚举值。这些枚举的值可以支持位操作符。

在ObjC里，我们使用0表示空的option。在Swift里，使用nil。

``` swift
let myString = myData.base64EncodedStringWithOptions(nil)
```

### 指针

在任何时候，Swift都会避免让我们直接访问指针。但是，仍然提供了一些指针类型供我们需要时使用。下面的Type是一个真实类型的占位符。

对于返回值，变量，参数，有下面的映射：

    C Syntax        Swift Syntax
    const Type *    UnsafePointer<Type>
    Type *          UnsafeMuatblePointer<Type>

对于类类型：

    C Syntax            Swift Syntax
    Type * const *      UnsafePointer<Type>
    Type * __strong *   UnsafeMutablePointer<Type>
    Type **             AutoreleasingUnsafeMutablePointer<Type>

####    常量指针

当一个函数声明如果接受一个UnsafePointer<Type>的参数，它可以接受一下几种：
    *   nil，作为空指针传入
    *   一个UnsafePointer<Type>，UnsafeMutablePointer<Type>，AutoreleasingUnsafeMutablePointer<Type> 值，并转换到UnsafePointer<Type>。
    *   一个in-out表达式，它的操作数是一个Type的左值。作为操作数的地址传入。
    *   一个[Type]值，作为一个数组开始的指针传入，生命周期也是函数调用。

``` swift
func takesAPointer(x: UnsafePointer<Float>) { /*...*/ }
```

我们可以调用：

``` swift
var x: Float = 0.0
var p: UnsafePointer<Float> = nil
takesAPointer(nil)
takesAPointer(p)
takesAPointer(&x)
takesAPointer([1.0, 2.0, 3.0])
```

当一个函数接受一个UnsafePointer<Void>的参数，他可以接受任何的Type在UnsafePointer<Type>里可以接受的参数。

``` swift
func takesAVoidPointer(x: UnsafePointer<Void>)  { /* ... */ }
```

我们可以调用：

``` swift
var x: Float = 0.0, y: Int = 0
var p: UnsafePointer<Float> = nil, q: UnsafePointer<Int> = nil
takesAVoidPointer(nil)
takesAVoidPointer(p)
takesAVoidPointer(q)
takesAVoidPointer(&x)
takesAVoidPointer(&y)
takesAVoidPointer([1.0, 2.0, 3.0] as [Float])
let intArray = [1, 2, 3]
takesAVoidPointer(intArray)
```

####    可变指针

当一个函数接受一个UnsafeMutablePointer<Type>的参数时，它可以接受下面的实参：
    *   nil作为空指针传入
    *   一个 UnsafeMutablePointer<Type> 值
    *   一个in-out表达式，它的操作数是一个Type的左值。作为操作数的地址传入
    *   一个[Type]值，作为一个数组开始的指针传入，生命周期也是函数调用

例如：

``` swift
func takesAMutablePointer(x: UnsafeMutablePointer<Float>) { /*...*/ }
```

下面的调用都可以：

``` swift
var x: Float = 0.0
var p: UnsafeMutablePointer<Float> = nil
var a: [Float] = [1.0, 2.0, 3.0]
takesAMutablePointer(nil)
takesAMutablePointer(p)
takesAMutablePointer(&x)
takesAMutablePointer(&a)
```

当一个函数接受一个UnsafeMutablePointer <Void>的参数，他可以接受任何的Type在UnsafeMutablePointer <Type>里可以接受的参数。

例如：

``` swift
func takesAMutableVoidPointer(x: UnsafeMutablePointer<Void>)  { /* ... */ }
```

下面的调用都可以：

``` swift
var x: Float = 0.0, y: Int = 0
var p: UnsafeMutablePointer<Float> = nil, q: UnsafeMutablePointer<Int> = nil
var a: [Float] = [1.0, 2.0, 3.0], b: [Int] = [1, 2, 3]
takesAMutableVoidPointer(nil)
takesAMutableVoidPointer(p)
takesAMutableVoidPointer(q)
takesAMutableVoidPointer(&x)
takesAMutableVoidPointer(&y)
takesAMutableVoidPointer(&a)
takesAMutableVoidPointer(&b)
```

####    自动释放的指针

当一个函数接受一个AutoreleasingUnsafeMutablePointer <Type>的参数时，它可以接受下面的实参：
    *   nil作为空指针传入
    *   一个 AutoreleasingUnsafeMutablePointer <Type> 值
    *   一个in-out表达式，它的操作数是基础类型被拷贝的临时的非拥有的buffer。这个buffer的地址被传递给被调用函数，然后在返回时，这个buffer的值被加载，保留(retained)，并且赋值给这个操作数


注意，没有数组。

例如：

``` swift
func takesAnAutoreleasingPointer(x: AutoreleasingUnsafeMutablePointer<NSDate?>) { /* ... */ }
```

下面的调用都可以：

``` swift
var x: NSDate? = nil
var p: AutoreleasingUnsafeMutablePointer<NSDate?> = nil
takesAnAutoreleasingPointer(nil)
takesAnAutoreleasingPointer(p)
takesAnAutoreleasingPointer(&x)
```

被指向的类型是不会被桥接的。例如，NSString** 转到Swift就成了AutoreleasingUnsafeMutablePointer<NSString?>而不是AutoreleasingUnsafeMutablePointer<String?>。

####    函数指针

C的函数指针在Swift里成了CFunctionPointer<Type>，而Type是一个Swift函数类型。例如，一个C里的函数指针是 int (*)(void)类型的，转到Swift就变成了CFunctionPointer<() -> Int32>。

### 全局常量

在C和ObjC定义的全局常量会被自动导入为Swift全局常量。

### 预编译指令

Swift编译器并不包含预编译器。相反，它使用一些编译期的属性，编译配置和语言特性来完成类似的功能。因此，预编译指令在Swift里并没有被实现。

####    简单的宏

通常我们在C和ObjC里用#define来定义的宏，在Swift都应该用全局常量代替。例如，#define FADE_ANIMATION_DURATION 0.35 在Swift里表示为let FADE_ANIMATION_DURATION = 0.35 更好。这样简单的宏会被导入为全局常量。

####    复杂的宏

在C和ObjC里使用的复杂的宏在Swift里没有替代品。复杂的宏是指那些没有定义常量的，包括使用参数的，类似于函数的宏。我们在C和ObjC里使用复杂的宏是为了避免类型检查的限制或者避免大量重复的代码。然而，宏会增加我们bebug和重构的复杂度。在Swift里，我们可以使用函数和泛型来达到同样的目的。因此，在C和ObjC的复杂的宏在Swift里不可用。

####    编译配置

Swift和ObjC代码通过不同的方式编译。Swift代码可以根据编译配置的值来条件编译。编译配置包含了字面的true和false，命令行flag，还有下面的平台检测函数。我们可以使用-D <#flag#>。

    函数                  参数
    os()                  OS X,iOS
    arch()                x86_64,arm,arm64,i386

    
    注意
    arch(arm)的结果对于arm64的设备返回是false。arch(i386)对于32位iOS模拟器返回true。

一个简单的条件编译：

``` swift
    #if build configuration
        statements
    #else
        statements
    #endif
```

我们也可以使用||，&&，和 ！来写出复杂的条件编译。

``` swift
    #if build configuration && !build configuration
        statements
    #elseif build configuration
        statements
    #else
        statements
    #endif
```

和C里的条件编译不一样，Swift里条件编译的代码部分必须是完整独立且语法正确的代码块。因为所有的Swift代码都会做语法检查，即使它在条件编译的时候不会编译。


