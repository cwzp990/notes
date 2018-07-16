# notes
这里是我最近阅读的关于JS经典书籍的笔记，有一些是别人的理解，拿到一起，方便阅读，如有侵权，请告知我，我立即删除！

## 1. 你不知道的JavaScript 中卷

### chapter 1 类型

我们都知道，JS是弱类型语言，这使得它不像c、Java那样，在大型程序里，类型检测将变得十分困难。因此微软才开发出了TypeScript语言。
在JS官方定义中，有以下六种类型：undefined、null、boolean、string、number和object。
然而，几乎所有的JS程序都会涉及到强制类型转换，稍不留神，就会出现意想不到的结果。

在JS中，我们可以用typeof运算符来查看值的类型

![Image text](https://github.com/cwzp990/notes/blob/master/images/type1.png)

我们会发现，null有些特殊，他是object，正确返回的结果应该是null，但这个“bug”已经由来已久，这牵涉到太多的web系统，“修复”它会产生更多的bug，令许多系统无法正常工作。

typeof function a(a, b){} 会输出 "function"，可见function也是JS的一个内置类型

a.length // 2

typeof [] 是否会输出array呢，答案是错误的，它会返回object

#### 1. 1 值和类型

JS中，变量是没有类型的，只有值才有，变量可以随时持有任何类型的值。
换个角度来说，就是JS不做“类型强制”，也就是说，语言引擎不要求变量总是持有与其初始值同类型的值。
一个变量可以现在被赋值为字符串类型值，随后又被赋值为数字类型值。

typeof typeof 42;   // "string"

#### 1.2 undefined（未限定的、为阐明的）和 undeclared（未申报的）

![Image text](https://github.com/cwzp990/notes/blob/master/images/type2.png)

这里，浏览器对于undeclared的处理容易引起误会，叫做b is not found更为合适

浏览器对下面这类情况处理更让人抓狂：

![Image text](https://github.com/cwzp990/notes/blob/master/images/type3.png)

其实它的作用是一个安全防护机制，还有一种就是不用通过typeof的安全防护机制的方法，就是检查所有全局变量是否是全局对象的属性，浏览器中的全局对象是window，访问window.a不会抛出referenceError错误

### chapter 2 值

#### 2.1 数组

和其他强类型语言不同，在JS中，数组可以容纳任何类型的值，可以是字符串、数字、对象，甚至是其他数组

有时，需要将类数组（一组通过数字索引的值）转换为真正的数组，这一般通过数组工具函数：

常用的是 Array.prototype.slice.call，es6新增了Array.from

#### 2.2 字符串

字符串和数组很类似，它们都是类数组，都有length属性以及indexof和concat方法

JS中，字符串是不可变的，而数组是可变的

![Image text](https://github.com/cwzp990/notes/blob/master/images/string1.png)

许多数组函数用来处理字符串很方便，虽然字符串没有这些函数，但是我们可以通过借用来处理字符串

![Image text](https://github.com/cwzp990/notes/blob/master/images/string2.png)
