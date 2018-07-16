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

我们会发现，通过借用数组的方法可以很方便的处理字符串，但是我们可以借用数组的非变更方法，但不能借用数组的可变更方法

![Image text](https://github.com/cwzp990/notes/blob/master/images/string3.png)

看到报错了么，其实JS自己先把数组转换成了字符串

![Image text](https://github.com/cwzp990/notes/blob/master/images/string4.png)

数组的非变更方法是不改变原数组，并返回一个新数组的。那么每次调用必定产生一个新数组，并遍历上下文对象，把对应索引上的值赋给新数组,可惜我们无法“借用”数组的可变更成员函数，因为字符串是不可变的

一个变通的方法是先将字符串转换为数组，待处理完后再将结果转换回字符串

var c = a.split("").revise().join("")

#### 2.3 数字

JS只有一种数值类型，number，包括“整数”和带小数点的十进制数

注意，这里的整数并不是真正意义上的整数，JS的整数就是没有小数的十进制数，所以 42.0 = 42

JS的数字类型是基于IEEE754标准来实现的，该标准也称为“浮点数”，JS使用的是“双精度”格式（即64位二进制）

在es6之前，JS十六进制用0x表示，八进制用0表示，而在es6严格模式下，0o表示十六进制，0b表示八进制

二进制最大的问题在于：

0.1 + 0.2 === 0.3 // false

es6中，可以用机器精度来比较两个数是否相等

```

function equal(n1, n2) {
    return Math.abs(n1 - n2) < Number.EPSILON;
}

```

undefined类型只有一个值，即undefined，null类型也只有一个值，即null。undefined指从未赋值，null指曾赋过值，但是目前没有值。null是一个特殊关键字，而undefined不是。

#### 2.4 特殊的情况

1. 不是数字的数字

如果数字运算的操作数不是数字类型，就无法返回一个有效的数字，这种情况下返回值为NAN（not a number，容易引起歧义，称为无效数值更准确些）

NAN是一个警戒值，用于指出数字类型中的错误情况，即“执行数学运算没有成功，这是失败后返回的结果”

NaN === NaN // false

需要用isNaN来判断一个值是否是NaN

但是，它有一个缺陷，就是检查参数是不是NaN，也不是一个数字，例如：

var a = 'foo'

window.isNaN(a)     // true

Number.isNaN(a)     // false

2. 无穷数

在JS里， 1/0会得到Infinity，-1/0会得到-Infinity

计算结果一旦溢出为无穷数，就无法再得到有穷数。换句话说，就是你可以从有穷走向无穷，但无法从无穷回到有穷

Infinty / Infinty = NaN         1 / Infinty = 0         -1 / Infinity = -0

3. 零值

0 / -3 = -0             0 * -3 = -0

注意： 加法和减法并不能得到-0

根据规范，对-0进行字符串化会返回0，而反过来将-0从字符串转换为数字，能得到-0

0 === -0        // true

#### 2.5 特殊等式

NaN 和 -0在相等比较时的表现有些特别，在es6中，我们可以用Object.is来判断两个值是否绝对相等

Object.is(2 / 'foo', NaN)       // true

Object.is(-0, 0)        // false
