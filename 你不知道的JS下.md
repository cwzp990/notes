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

#### 2.6 值和引用

JS中，变量不可能成为指向另一个变量的引用，JS中，引用指向的是值。如果一个值有10个引用，这些引用指向的都是同一个值，它们相互之间没有引用/指向关系。

简单值都是通过值复制的方式来赋值、传递，包括null、undefined、字符串、数字、布尔和es6中的symbol

复合值（对象、数组）通过引用复制的方式来传递

![Image text](https://github.com/cwzp990/notes/blob/master/images/val1.png)

我们可以发现，变量a持有该值的一个复本，b持有它的另一个复本，b改变时，a也跟着改变

而c和d分别指向同一个复合值的两个不同引用。注意，这里的c和d仅仅是指向值[1,2,3]，并非持有，所以d改变c也跟着改变

![Image text](https://github.com/cwzp990/notes/blob/master/images/val2.png)

和上一个类似，我们像函数传递a时，实际上是将引用a的一个复本赋值给x，而a仍指向[1,2,3],我们可以通过引用x来更改数组的值（push(4)之后变为[1,2,3,4]），但x = [4,5,6]并不影响a的指向，所以a仍指向[1,2,3,4]

![Image text](https://github.com/cwzp990/notes/blob/master/images/val3.png)

我们无法自行决定使用值复制还是引用复制，一切由值的类型来决定

![Image text](https://github.com/cwzp990/notes/blob/master/images/val4.png)

标量一旦创建，值就无法更改

![Image text](https://github.com/cwzp990/notes/blob/master/images/val5.png)

### chapter 3 原生函数

JS中，常用的原生函数有： String()、Number()、Boolean()、Array()、Object()、Function()、RegExp()、Date()、Error()、Symbol()

![Image text](https://github.com/cwzp990/notes/blob/master/images/fn1.png)

注意，typeof在这里返回的是对象类型的子类型，即new String("abc")创建的是字符串"abc"的封装对象，而非基本类型值"abc"

所有typeof返回值为"onject"的对象（如数组）都包含一个内部属性class，这个属性无法直接访问，一般通过object.prototype.toString...来查看，如

Object.prototype.toString.call([1,2,3])             // [object Array]

Object.prototype.toString.call(/regex-literal/i)    // [object RegExp]

Object.prototype.toString.call(null)                // [object Null]

Object.prototype.toString.call(undefined)           // [object undefined]

#### 3.1 封装对象

由于基本类型值没有.length和.toString这样的属性和方法，需要通过封装对象才能访问，此时JS会自动为基本类型值包装一个封装对象

var a = "abc"

a.length;           // 3
a.toUpperCase();    // "ABC"

![Image text](https://github.com/cwzp990/notes/blob/master/images/fn2.png)

#### 3.2 拆封

valueof()函数用来返回对象的原始值
![Image text](https://github.com/cwzp990/notes/blob/master/images/fn3.png)

#### 3.3 原生函数作为构造函数

关于数组、对象、函数和正则，我们常常用字面量的形式来创建他们，实际上，使用字符串形式和构造函数形式是一样的，除非十分必要，应该避免使用构造函数来创建他们。

![Image text](https://github.com/cwzp990/notes/blob/master/images/fn4.png)

Array构造函数只带一个数字参数时，该参数会被作为数组的预设长度，而非数组里的一个元素。另外，数组没有预设长度这一概念，这样创建出来的是一个空数组，只不过它的length为1，毫无意义。

![Image text](https://github.com/cwzp990/notes/blob/master/images/fn5.png)

a.map()之所以执行失败，是因为数组中并不存在任何单元，所以map无从遍历。

![Image text](https://github.com/cwzp990/notes/blob/master/images/fn6.png)

永远不要创建和使用空单元数组！

#### 3.4 原生原型

原生构造函数都有自己的.prototype对象，如Array.prototype、String.prototype，这些对象包含其对应子类型所持有的行为特征

例如，将字符串值封装为字符串对象之后，就能访问String.prototype中定义的方法

String.prototype.indexOf()                      // 在字符串中找到指定子字符串的位置

String.prototype.charAt()                       // 获得字符串指定位置上的字符

String.prototype.subStr()/substring()/slice     // 获得字符串指定部分

String.prototype.toUpperCase()/toLowerCase      // 将字符串转换为大写或小写

String.prototype.trim()                         // 去掉字符串前后空格，返回新的字符串

以上 方法不改变源字符串的值，而是返回一个新字符串

借助原型代理，所有字符串都可以访问这些方法

#### 3.5 将原型作为默认值

![Image text](https://github.com/cwzp990/notes/blob/master/images/fn7.png)

Function.prototype是一个空函数，RegExp.prototype是一个空的正则，而Array.prototype是一个空的数组

### chapter 4 强制类型转换

#### 4.1 值类型转换

类型转换发生在静态语言的编译阶段，而强制类型转换则发生在动态类型语言的运行时。

var a = 42;

var b = a + '';                 // 隐式类型转换

var c = String(a);              // 显式类型转换

#### 4.2 抽象值操作

1. ToString

基本类型值字符串化规则为：null->'null', undefined->'undefined',true->'true',对普通对象来说，除非自定义，否则toString()返回内部属性class的值，如[object, object]

如果对象有自己的toString()方法，则会调用该方法并使用其返回值，如数组的toString()方法：

var a = [1,2,3];

a.toString();       // '1,2,3'

2. JSON字符串化

JSON.stringify()在将JSON对象序列化为字符串时也用到了toString，JSON字符串化和toString()的效果基本相同，只不过序列化的结果总是字符串

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform1.png)

所有安全的JSON值都可以使用JSON.stringify()字符串化，那么不安全的值为：undefined、function、symbol和包含循环引用（对象之间相互引用，形成一个无限循环），JSON.stringify()在遇到他们会自动忽略他们，在数组中，则会返回null

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform2.png)

如果对象中定义了toJSON()方法，JSON字符串化时会首先调用该方法，然后用它的返回值来进行序列化

如果要对含有非法JSON值的对象做字符串化，或者对象中的某些值无法被序列化时，就需要定义toJSON()方法来返回一个安全的JSON值

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform3.png)

我们在a里面创建了一个循环引用，这时进行序列化操作时会报错

也就是说，toJSON应该返回一个能够被字符串化的安全的JSON值，而不是返回一个JSON字符串

JSON.stringify()传递一个可选参数replace，它可以是数组或者函数，用来指定对象序列化过程中，哪些属性应该被处理，哪些应该被排除。

如果replace是一个数组，那么它必须是一个字符串数组，其中包含序列化要处理的对象的属性的名称：

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform4.png)

如果replace是一个对象，它会对其本身调用一次，然后对对象中的每个属性各调用一次，每次传递两个参数，键和值，如果要忽略某个键就返回undefined，否则返回指定的值

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform5.png)

这里k在第一次调用是undefined（对对象本身调用这次），if语句将属性'c'排除掉，由于字符串化是递归的，因此数组[1,2,3]中的每个元素都会通过v传给replace，即1，2，3，参数k是他们的索引值，即0，1，2

JSON.stringify()还有一个可选参数space，用来指定输出缩进格式

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform6.png)

3. ToNumber

有时我们需要将非数字值当作数字来使用，比如数学运算。其中true->1, false->0, undefined->NaN, null->0

对象包括数组会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字

它会首先检查该值有无valueOf()方法，有 并且返回基本类型的值，就使用该值进行强制类型转换，如果没有就使用ToString()的返回值来进行强制类型转换,如果这两个方法均不返回基本类型值，则抛出TypeError错误

4. ToBoolean

JS中的值可以分为以下两类：

1.可以强制类型转换为false的值；2.其他（强制类型转换为true的值）

以下是第一种强制转换为false的情况

undefined null false 0 -0和NaN ''

假值列表以外的值都是真值

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform7.png)

虽然在JS代码中会出现假值对象，但它实际上并不属于JS语言的范畴

浏览器在某些特定环境下，在常规JS语法基础上自己创建了一些外来值，这些就是“假值对象”。假值对象和普通对象并无二致，但将他们强制类型转换为布尔值为false

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform8.png)

我们会发现，上面的值均为true，也就是说，除了假值列表里的，其余的均为true

#### 4.3 显式强制类型转换

1. 字符串和数字之间的转换

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform9.png)

2. 奇特的~运算符

字符运算符只适用于32位整数，运算符会强制操作数使用32位格式。这是通过抽象操作ToInt32来实现的。

ToInt32首先执行ToNumber强制类型转换，然后再执行ToInt32

~它首先将值强制类型转换位32位数字，然后执行字位操作符“非”（对每一个字位进行反转）

~42 // -(42+1) = -43

我们可以这么理解。那么，这又有什么关系呢？-1在代码中代表一个哨位值，比如在indexOf()方法中，如果找到就返回字符串所在的位置(从0开始)，否则返回-1，表示不存在。如果直接这样写，会在代码中暴露了底层的实际细节。我们可以这样写

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform10.png)

3. 字位截除

它的效果和Math.floor()相似，但也有不同

~~中的第一个~执行ToInt32并反转字位，然后第二个~再进行一次字位反转，即将所有字位反转回原值，最后得到的仍然是ToInt32的结果

注：~~只适用于32位数字，它对负数的处理与Math.floor()不同

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform11.png)

#### 4.4 显式解析数字字符串

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform12.png)

parseInt针对的是字符串值，true、function(){}和[1,2,3]是没作用的

es5之前，parseInt()有一个坑，如果没有第二个参数来指定转换的基数，parseInt会根据字符串的第一个来自行决定基数，如果第一个是x/X，则转换为16进制，如果第一个字符是0，则转换为8进制。es5之后默认为10进制。

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform13.png)

上述代码实际上是parseInt("Infinity", 19)...第二个字符是n，和"42px"一样，n以后的被忽略了。在JS中，基数的有效值是0~9，a~i，i以19为基数时，值为18

#### 4.5 显式转换为布尔值

我们之前提到，使用Boolean会显式地将值强制转换为布尔值，!!也一样。

### 4.6 隐式强制类型转换

1. 隐式的简化

字符串和数字之间的隐式强制类型转换

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform14.png)

某一个或两个都是字符串，则执行的是字符串拼接的操作。然而：


因为数组的valueOf()操作无法得到简单类型的值，于是它转而调用toString(),因此上式实际上是'1,2' + '3,4' = '1,23,4'

所有，我们经常会看到这种用法 数字 + '' ---> 字符串

一般情况下，a + '' 和 String(a)效果类似，但是

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform16.png)

从这里我们可以发现，前者先调用valueOf方法再调用toString方法，而后者直接调用toString方法

字符串转换为数字：

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform17.png)

对象的 - 操作和 + 类似

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform18.png)

2. || 和 && 

这两个运算返回的不是布尔值，而是这两个操作符中的一个，即两个操作数中的一个，然后返回它的值

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform19.png)

|| 和 && 会对第一个操作数（a和c）进行条件判断，如果不是布尔值，就先进行强制类型转换。再继续下面的逻辑：

对于 || 来说，如果第一个为true，就返回第一个操作数的值，如果为false，就返回第二个操作数的值，&&相反

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform20.png)

只有第一个值为真，第二个函数才会调用，否则会“短路”~

![Image text](https://github.com/cwzp990/notes/blob/master/images/transform21.png)

这里if条件里返回的是"foo"，但是if语句自带强制类型转换，所有返回了true

### 4.7 == 和 ===

前者允许在相等比较中进行强制类型转换，而===不允许

1. 如果两个值类型相等，就仅比较它们是否相等，如42等于42，"abc"等于"abc"

注意 NaN不等于NaN，+0不等于-0

2. 对象的宽松相等 ==
两个对象指向同一个值时，即视为相等，不发生强制类型转换

3. == 在比较两个不同类型的值时，会发生隐式类型转换，将其中之一或者两者都转换为相同类型后在做比较

字符串和数字的比较

![Image text](https://github.com/cwzp990/notes/blob/master/images/equal1.png)

如果x是数字，y是字符串，返回 x == ToNumber(y)
如果x是字符串，y是数字，返回 ToNumber(x) == y
即转换成数字在比较

其他类型和布尔类型比较
![Image text](https://github.com/cwzp990/notes/blob/master/images/equal2.png)
如果x是布尔值，则返回ToNumebr(x) == y
如果y是布尔值，则返回x == ToNumber(y)
即布尔值换成数字在比较
永远不要这样比较，没有意义！！！

null和undefined之间的比较
![Image text](https://github.com/cwzp990/notes/blob/master/images/equal3.png)
如果x为null，y为undefined，则结果为true
如果x为undefined，y为null，则结果为true
也就是说，在==中，null和undefined是一回事，可以相互进行隐式强制类型转换

对象和非对象之间的比
![Image text](https://github.com/cwzp990/notes/blob/master/images/equal4.png)
如果x是字符串或数字，y是对象，则返回 x == ToPrimitive(y)的结果
如果x是对象，x是字符串或数字，则返回 ToPrimitive(x) == y的结果

总结：如果两边的值有true或者false，千万不要用 ==
如果两边的值有[]、''、0，尽量不要用 ==

### 4.8 抽象关系比较
![Image text](https://github.com/cwzp990/notes/blob/master/images/equal4.png)

### chapter 6 异步

#### 1. 分块的程序

```

var data = Ajax('http://xxxxxxx')
console.log(data)

```
我们发现这里的ajax通常不会包含ajax结果

标准的ajax请求不是同步完成的，这意味着ajax还没有返回任何值可以赋给变量data，如果data能够阻塞到响应返回，那么data = ..赋值就会正确工作

以前，我们通过回调来获得data，如：

```

ajax('http://xxxxxx',function cb(data){

    console.log(data)

})

```

![Image text](https://github.com/cwzp990/notes/blob/master/images/async1.png)

#### 2. 事件循环

直到es6，JS才真正内建有直接的异步概念

JS引擎只是在需要的时候，在给定的任意时刻执行程序中的单个代码块

JS引擎并不是独立运行的，它运行在宿主环境中，如web浏览器、node，所有这些环境都有一个共同点，即它们都提供了一种机制来处理程序中多个块的执行，且执行每块时调用JS引擎，这种机制就叫事件循环

也就是说，JS并没有时间的概念，只是一个按需执行JS任意代码片段的环境，事件（JS代码的执行）调度总是由包含它的环境进行

```
// JS实际上在这里通知了宿主环境，我现在已暂停执行，你一旦拿到了数据，就调用这个函数
// 浏览器就会设置侦听来自网络的响应，拿到要给你的数据之后，就会把回调函数插入到事件循环，以此实现对这个回调的调度执行
ajax('http://xxxxxx',function cb(data){

    console.log(data)

})

```

```

// eventloop是一个用作队列的数组
// 先进先出
var eventloop = []
var event

// 永远执行
while(true) {

    if (eventloop.length > 0) {
        // 拿到队列中的下一个事件
        event = eventloop.shift()
        //执行
        try{
            event()
        }
        catch(err) {
            reportError(err)
        }
    }

}

```

setTimeout并没有把你的回调函数挂在事件循环队列中，他所做的是设定一个定时器，当定时器到时后，环境会把你的回调函数放在事件循环中，这样，在未来某个时刻的tick会摘下并执行这个回调

程序通常会被分成了很多小块，在事件循环队列中一个接一个地执行，和你不相关的其他事件也可能会插入到队列中

#### 3. 并行线程

异步是关于现在和将来的时间间隙，而并行是关于能够同时发生的事情

```

var a = 20;

function foo() {
    a = a + 1;
}

function bar() {
    a = a * 2;
}

ajax('http://xxxxx', foo)
ajax('http://xxxxx', bar)

```

根据JS单线程运行特性，foo在前，结果为42，bar在前，结果为41

线程和进程的概念：
https://www.cnblogs.com/dreamroute/p/5207813.html

https://www.zhihu.com/question/25532384

#### 3. 完整运行

由于JS单线程的特性，foo()中的代码具有原子性，也就是说，一旦foo()开始运行，它的所有代码都会在bar()中的任意代码运行之前完成，或者相反，这就是完美运行特性

即上述代码只有两种情况，这取决于这两个函数哪个先运行，如果存在多线程，且foo()、bar()中的语句可以交替运行的话，可能输出的数目会增加不少

假设我们这里有三段代码，块1是同步的（现在运行），块2和块3是异步的（将来运行），那么块2和块3哪个先运行都是有可能的，同一段代码有两种结果意味着存在不确定性。但是这种不确定性是在函数（事件）顺序级别上，而不是多线程情况下的语句顺序级别。

在JS的特性中，这种函数顺序的不确定性就是通常所说的竞态条件，foo()和bar()相互竞争，看谁先运行

#### 4. 并发

我们以下拉刷新功能举例

第一个进程在用户向下滚动页面触发onscroll事件响应这些事件（发起ajax请求要求新的内容），第二个进程接受ajax响应（把内容展示到页面）

显然，如果用户操作够快的话，在等待第一个响应返回并处理的时候可能看到两个或多个onscroll事件被触发，因此将得到快速触发彼此交替的onscroll事件和ajax响应事件

理想情况：
![Image text](https://github.com/cwzp990/notes/blob/master/images/async2.png)

实际情况：
![Image text](https://github.com/cwzp990/notes/blob/master/images/async3.png)

1. 非交互

```

var res = {}

function  foo(results) {
    res.foo = results;
}

function bar(results) {
    res.bar = results;
}

ajax('http://xxxxx', foo)
ajax('http://xxxxx', bar)

```

foo和bar是两个并发执行的进程，按照什么顺序执行是不确定的，但是我们构建程序的方式使得无论按哪种顺序执行都无所谓。因为它们是独立运行的，不会互相影响

2. 交互

并发的进程需要相互交流，通过作用域或DOM间接交互

```

var res = [];

function response() {
    res.push(data)
}

// 我们需要加入判断来避免竞态条件引起的不确定性
// 这个场景推出的方法也可以应用于多个并发函数调用通过共享DOM彼此之间交互的情况
function response(data) {
    if (data.url === 'http://1') {
        res[0] = data
    } else (data.url === 'http://2') {
        res[1] = data
    }
}

ajax('http://xxxxx', response)--->res[0]存放结果
ajax('http://xxxxx', response)--->res[1]存放结果

```

有时候得到的结果可能和预期的相反，这要视哪个调用先完成决定

```

var a,b

function foo(x) {
    a = x * 2;
    baz();
}

function bar(y) {
    b = y * 2;
    // if (a && b) {baz()}
    baz();
}

function baz() {
    console.log(a + b);
}

ajax('http://xxxxx1', foo);
ajax('http://xxxxx2', bar);

```

上述例子中，无论foo()和bar哪一个先被触发，总会是baz()过早运行（a或者b仍处于未定义状态），但对baz()的第二次调用就没有问题，因为这时候a和b都已经可用了。

3. 协作

这里的重点不再是通过共享作用域中的值进行交互，这里的目标是取到一个长期运行的“进程”，并将其分割成多个步骤或多批任务，使得其他并发“进程”有机会将自己的运算插入到事件循环队列中交替运行

```

var res = [];

// 从ajax调用中取得结果数组
function response(data) {
    // 添加到已有的res数组

    // 添加标志
    var trunk = data.splice(0, 1000);

    res = res.concat(
        data.map(fynction(val){
            // 创建一个新的变换数组把所有data值加倍
            return val * 2;
        })
    )

    // 还有剩下的需要处理么？
    if (data.length > 0) {
        // 异步调度下一次批处理
        setTimeout(function(){
            response(data)
        }, 0)
    }
}

ajax('http://xxxxxx1', response)
ajax('http://xxxxxx2', response)

```

这样的“进程”运行时，页面上的其他代码都不能运行，包括不能有其他的response()调用或UI刷新，甚至是滚动、输入、按钮点击这样的用户事件

我们把数据集合放在最多包含1000条项目的块中，这样，我们就确保了进程运行的时间很短，即视，这意味着需要更多的后续进程，因为事件循环队列交替运行会提高站点/app的响应（性能）

这里需要注意，我们没有协调这些进程的顺序，所以结果的顺序是不可预测的

这里使用setTimeout进行异步调度，基本上它的意思是“把这个函数插入到当前事件循环队列的结尾处”

#### 4. 任务

任务队列是挂在事件循环队列的每个tick之后的一个队列。在事件循环的每个tick中，可能出现的异步动作不会导致一个完整的新事件添加到事件循环队列中，而会在当前tick的任务队列末尾添加一个项目（一个任务），好像在说“这里还有一件事将来要做，但要确保在其他任何事情发生之前就完成它”

事件循环队列类似一个游乐园游戏，玩过了一个项目之后，需要重新排队才能再玩一次，而任务队列可以插队继续玩

一个任务可能引起更多的任务被添加到同一个队列的末尾，所以从理论上来说，任务循环可能无限循环（一个任务总是添加到另一个任务，以此类推），进而导致程序的饿死，无法转移到下一个事件循环tick

```

console.log(a)

setTimeout(function(){
    console.log(b)
}, 0)

schedule(function(){
    console.log(c)
    schedule(function(){
        console.log(d)
    })
})

```

打印顺序应该是acdb

#### 5. 语句顺序

代码中语句的顺序和JS引擎执行语句的顺序并不一定一致

### chapter 7 回调

#### 1. continuation

// A
ajax('...', function(){
    // C
})
// B

// A
setTimeout(function(){
    // C
}, 1000)
// B

回调函数难以理解是因为我们的思考方式是一步一步的，但是从同步转换到异步之后，回调函数并不是按照一步一步的方式来表达的

嵌套回调与链式回调

listen("click", function handle(evt){
    setTimeout(function request(){
        ajax('http://xxxxx', function response(text){
            if (text === 'hello') {
                handle()
            } else if (text === 'world') {
                request()
            }
        })
    }, 500)
})

在我们引入第三方不受信任的库时，用到了它的回调函数，我们需要进行很多不必要的逻辑来避免回调带来的风险

省点回调

```

function success(data) {
    console.log(data)
}

function failure(err) {
    console.log(err)
}

ajax('http://xxxxx', success, failure)

```

es6中的promise就是使用的这种分离回调设计

```

function response(err, data) {
    if (err) {
        console.log(err)
    } else {
        console.log(data)
    }
}

ajax('http://xxxxx', response)

```

这段代码打印出0（同步回调调用）还是1（异步回调调用）呢？这要是情况而定

如果你不确定关注的api会不会永远异步执行怎么办呢，可以创建一个类似于这个“验证”版本的工具

```

function async(fn) {
    var orig_fn = fn;
    intv = setTimeout(function(){
        intv = null;
        if (fn) fn();
    }, 0)
}

fn = null;

return function() {
    // 触发太快，在定时器intv触发指示异步转换发生之前
    if (intv) {
        fn = orig_fn.bind.apply(
            orig_fn,
            // 把封装器的this添加到bind()调用的参数中，以及克里化所有传入的参数
            [this].cancat([].slice.call(arguments))
        )
    } else {
        // 调用原来的函数
        orig_fn.apply(this, arguments)
    }
}

```

### chapter 8 Promise

1.未来值

之前我们用回调的方法来封装程序中的continuation，然后把回调交给第三方，意思是这是将来要做的事情，要在当前的步骤完成之后发生。但是，如果我们不把自己程序的continuation传给第三方，而是希望第三方给我们解决了解其任务何时结束的能力，然后由我们自己的代码来决定下一步做什么，这就是promise

举个例子

```

var x, y = 2;

console.log(x + y); // NaN, 因为x还没有准备好

```

将x + y，如果他们当中的任何一个还没有准备好，就等待两者都加载好，一旦可以就立刻执行+运算，用程序来表达

```

function add(getX, getY, cb) {

    var x, y;
    getX(function(){
        x = xVal;
        // 两个都准备好了？
        if (y != undefined) {
            cb(x + y)
        }
    });
    getY(function(){
        y = yVal;
        // 两个都准备好了？
        if (x != undefined) {
            cb (x + y)
        }
    })

}

add(fetchX, fetchY, function(sum) {
    console.log(sum)
})

```

Promise方法

```

function add(xPromise, yPromise) {

    return Promise.all([xPromise, yPromise]).then(function(values){
        return values[0] + values[1]
    })

}

add(fetchX(), fetchY()).then(function(sum) {

    console.log(sum);

})

```

promise一旦决议，就是外部不可变的值，我们可以安全的把这个值传递给第三方，并确信它不会被更改

2. 完成事件

假设要调用一个函数foo()执行某个任务，我们不知道也不关心它的任何细节。这个函数可能立即完成任务，也可能需要一段时间才能完成

我们只需知道foo()什么时候结束，这样就可以进行下一个任务。换句话说，我们想要通过某种方式在foo()完成的时候得到通知，以便可以继续下一步

3.具有then方法的鸭子类型

在promise领域，如何判断某个值是不是真的promise？promise是通过new promise()语法创建的，识别是通过then()函数的一个对象或函数来完成。

```

Object.prototype.then = function(){}
Array.prototype.then = function(){}

```

如果你像上述代码那样，给原生原型添加then(),promise也会获取到该值

#### 回调函数带来的困扰

1). 调用回调过早
2). 调用回调过晚（或不被调用）
3). 调用回调次数过少或过多
4). 未能传递所需的环境或参数
5). 吞掉可能出现的错误或异常

我们来一个个解决这些问题

1. 调用回调过早

对一个promise调用then的时候，即使这个promise已经决议，提供给then()的回调也总是会被异步调用，不再需要插入自己的setTimeout，promise会自动防止其出现

2. 调用回调过晚（或不被调用）

promise创建对象调用resolve()或reject()时，这个promise的then()注册的观察回调就会被自动调度。一个promise决议后，这个promise上所有的通过then()注册的回调都会在下一个异步时机点上依次被立即调用，这些回调中的任意一个都无法影响或延误对其他回调的调用

```

p.then(function(){
    p.then(function(){
        console.log('c')
    })
    console.log('a')
})

p.then(function(){
    console.log('b')
})

// abc

```

c无法打断或抢占b，这是因为promise的运作方式

在这两个独立的promise上链接的回调的相对顺序可能无法可靠的预测

如果两个promise p1和p2都已经决议，那么p1.then()，p2.then()应该最终会先调用p1的回调，然后是p2的那些，但有时不是这样的

```

var p3 = new Promise(function(resolve, reject){
    resolve('b');
})

var p1 = new Promise(function(resolve, reject){
    resolve(p3)
})

var p2 = new Promise(function(resolve, reject){
    resolve('a')
})

p1.then(function(v){
    console.log(v)
})

p2.then(function(v){
    console.log(v)
})

// a b

```

此时 p1的回调排在p2之后

3. 回调未调用

没有任何东西能阻止promise向你通知它的决议(如果它决议了的话)，如果你对一个注册了一个完成回调和一个拒绝回调，那么promise在决议时总是会调用其中的一个

```

function timeoutPromise(delay) {

    return new Promise(function(resolve,reject){
        setTimeout(function(){
            reject('timeout')
        }, delay)
    })

}

Promise.rece([
    foo(),                  // 试着开始foo()
    timeoutPromise(3000)    // 给它3秒钟
])
.then(
    function(){
        // foo() 及时完成
    },
    function(err){
        // 或者foo()被拒绝，或者只是没能按时完成
        // 查看err来了解是哪种情况
    }
)

```

4. 调用次数过少或过多