# notes
这里是我最近阅读的关于JS经典书籍的笔记，大部分是自己的理解，还有一些是别人的理解，拿到一起，方便阅读，如有侵权，请告知我，我立即删除！

## 1. 你不知道的JavaScript 上卷

资料文献：http://www.cnblogs.com/wangfupeng1988/p/3977924.html          https://www.jianshu.com/p/efe38b392f53

### chapter 1 作用域

程序能够存储变量的值，并在之后对这个值进行访问修改，那么问题来了，这些变量存储在哪里，如何在需要的时候找到他们，我们需要制定一套规则来存储

变量，这套规则就是作用域。

#### 1.1 编译原理

JS是解释执行语言，但事实上是一门编译语言。

传统的编译语言有三个步骤：

1.分词/词法分析

这个过程将由字符组成的字符串分解成对编程语言来说有意义的代码块，称为词法单元

如 将 `var a = 2`分解成`var 、a 、 = 、 2 、 ;` 空格是否有效取决于他是否有意义

2. 解析/语法分析

这个过程是将词法单元流转换成由元素逐级嵌套所组成的程序语法结构的树（AST）

3. 代码生成

将AST转换成可执行代码的过程。

简单来说，就是将`var a = 2`转换成机器指令，用来创建一个叫做a的变量（包括分配内存），并将一个值存在变量a中

编译器对`var a = 2`进行如下处理

1. 首先询问在同一作用域集合下是否已经存在一个该名称的变量，如果是，则忽略该声明，继续进行编译，否则声明一个新的变量a

2. 引擎运行时首先会询问作用域，在当前作用域下是否存在这么一个变量a，如果是，引擎就会使用这个变量，否则继续查找该变量

3. 如果找到了，就将2赋值给他，否则引擎将会抛出一个异常

引擎会为变量a进行LHS查找，另一个查找叫做RHS。即当变量出现在左侧时进行LHS查询（找到赋值操作的目标是谁，从而可以对其赋值），右侧时进行RHS查询（找到赋值操作的源头，即取值操作）

那么，`console.log(a)`就是一个RHS引用，而`a = 2`则是一个LHS引用

#### 1.3 作用域的嵌套

当一个块或函数嵌套在另一个块或函数中时，就产生了作用域的嵌套。在当前作用域中无法找到某个变量时，引擎就会去外层嵌套的作用域进行查找，直到找到该变量，一直到全局作用域为止！

不成功的LHS引用会导致自动隐式的创建一个全局变量（非严格模式下），而不成功的RHS引用会导致抛出ReferenceError异常。

### chapter 2 词法作用域

#### 2.1 词法阶段

词法作用域在函数定义时就已经确定了，而不是函数调用时确定！！！

作用域查找会在找到第一个匹配的标识符时停止。在多层嵌套作用域中，可以定义同名的标识符，称为“遮蔽效应”

作用域查找始终从运行时所处的最内部作用域开始，逐级向外层查找，直到遇到第一个匹配的标识符为止。

在这里，全局变量会自动成为全局对象（在浏览器环境下是window）的属性，因此，我们可以得出下面的结果：

```

a = 10;

console.log(window.a === 10) // true

```

#### 2.2 欺骗词法

前面我们说到，词法作用域往往是在函数定义时决定，那么我们如何才能在运行时来修改词法作用域呢？

注意，修改词法作用域会导致性能下降，我们并不推荐这么做！eval和with

### chapter 3 函数作用域和块级作用域

在JS中，只有函数才能创建作用域，我们无法在一个函数的外部去访问其内部变量的值。

通常，我们都是先声明一个函数，再向里面添加代码。我们也可以反过来，在所写的代码中任挑选一段，用函数声明进行包装，实际上就是将他隐藏起来了。

例如：

```

var a = 2;

function foo() {            <--- 添加这一行

    var a = 3;

    console.log(a); // 3

}                           <--- 添加这一行

foo();                      <--- 添加这一行

console.log(a); // 2

```

这样做会导致全局作用域里多了一个foo，我们还可以这样：

```

var a = 2;

(function foo() {            <--- 添加这一行

    var a = 3;

    console.log(a); // 3

})()                         <--- 添加这一行

console.log(a); // 2

```

在这里，foo变成了一个函数表达式，函数声明和函数表达式的区别是前者的foo被绑定在所在作用域中（全局环境），而后者(function foo(){...})只能在...处被访问，外部作用域则不行。

#### 3.2 匿名函数

通常，我们可以在回调函数中见到匿名函数，如：

```

setTimeout(function (){

    console.log('this is a function')

}, 1000)

```

#### 3.3 立即执行函数

```

var a = 2;

(function foo(){

    var a = 3;

    console.log(a); // 3

})()

console.log(a); // 2

```

#### 3.4 块级作用域

es3中的try/catch的catch会创建一个块级作用域，但是个别浏览器会报错，因此有的开发者会将catch的参数命名为err1，err2...

es6以前没有块级作用域的概念，直到es6中，我们可以用{}来创建一个块级作用域。

### chapter 4 提升

JavaScript看到 var a = 2 时，会将其看成两个部分，var a, a = 2，第一个声明是在编译阶段执行，第二个赋值声明是留在原地等待执行阶段

函数声明会被提升，而函数表达式不会

函数声明和变量声明都会被提升，但是函数优先，然后才是变量

```

foo();                  // 若没有第三个，则会输出1，否则输出3

function foo() {
    console.log(1);
}

var foo = function() {
    console.log(2);
}

function foo() {
    console.log(3);
}

```

### chapter 5 作用域和闭包

### chapter 6 关于this

this是在调用时进行绑定的，而不是声明的时候，它的指向取决于函数在哪里被调用

参考文献：https://www.cnblogs.com/pssp/p/5216085.html           https://www.jianshu.com/p/fcbc21a2c507

1.如果一个函数中有this，且没有被其他函数调用，则指向window，严格模式下为undefined

```

function foo() {

    console.log( this.a );
}

var a = 2;

foo(); // 2

function foo() {

    "use strict";

    console.log( this.a );
}

var a = 2;

foo(); // TypeError: `this` is `undefined`

```

2. 如果一个函数中有this，这个函数有被上一层函数调用，那么this指向上层函数

```

function foo() {

    console.log( this.a );
}

var obj = {

    a: 2,

    foo: foo
};

obj.foo(); // 2

```

3. 如果一个函数中有this，这个函数中包含多个对象，尽管这个函数是被最外层的对象所调用，this指向的也只是它上一级的对象

```

function foo() {

    console.log( this.a );
}

var obj2 = {

    a: 42,

    foo: foo
};

var obj1 = {

    a: 2,

    obj2: obj2
};

obj1.obj2.foo(); // 42

```

4. this与构造函数

```

function Fn(){

    this.user = "tom";

}

var a = new Fn();

console.log(a.user); // tom

```

5. 如果返回值是一个对象，那么this指向的就是那个返回的对象，如果返回值不是一个对象那么this还是指向函数的实例，注意，虽然null也是对象，但是在这里this还是指向那个函数的实例，因为null比较特殊

### chapter 7 JavaScript中的对象

创建对象有两种方法：

1. 对象字面量

var obj = {

    name: tom,

    age: 18,

    ...
}

2. 构造函数

var obj = new Object();

obj.name = tom;

obj.age = 18;

JS中有六种主要的数据类型： string number boolean undefined null object

前五个是基本类型，注意 typeof null，得到的是object

JS中有一些内置对象：String Number Boolean Object Function Array Date RegExp Error，它们实际上是一些内置函数，它们可以当做构造函数来使用。

注意，'i am a string'本质上不是一个String对象，而是一个字面量，如果我们要对他进行操作，需要将他转换成String对象。但是通常我们不用这么做，因为JS已经自动转换了。

3. 浅拷贝与深拷贝

4. Object.defineProperty

```

var obj = {};

Object.defineProperty(obj, 'a', {

    value: 2,

    writable: false,        // 不可写   obj.a = 3赋值操作无效, 严格模式下会报错

    configurable: false,    // 不可配置

    enumerable: false        // 不可枚举

})

```

enumerable详细介绍：

```

var obj = {};

object.defineProperty(obj, 'a', {

    enumerable: true,

    value: 2

})

object.defineProperty(obj, 'b', {

    enumerable: false,

    value: 3

})

obj.b; // 3

('b' in obj); // true

obj.hasOwnProperty('b') // true

for (var k in obj) {

    console.log(k, obj[k]); // 'a', 2

}

```

上述代码意思就是，当设置enumerable为false时，obj.b确实存在且有值，但是无法出现在对象属性的遍历中

对象的get

```

var obj = {

    a: 2;
}

obj.a; // 2

```

上述代码实际上是实现了get的操作（有点像函数的调用），对象默认的内置get操作首先在对象中查找是否有名称相同的属性，如果找到就会返回这个属性的值

若没有找到，将会执行另一个操作，即遍历可能存在的原型链

若还没有找到，则返回undefined

对象的put

put被触发时，实际的行为取决于许多因素，包括对象中是否已经存在这个属性，如果已经存在：

1. 属性是否是访问描述符，是并且存在setter则调用setter；

2. 属性的数据描述符中writable是否是false，是，在非严格模式下静默失败，在严格模式下将抛出异常

3. 若都不是，则将该值设置为属性的值

在es5中，可以使用getter和setter部分改写默认操作，但是只能应用在单个属性上，无法应用在整个属性上，getter和setter是一个隐藏函数，前者会在获取属性值时调用，后者在设置属性值时调用


我们通过hasOwnProperty来判断对象本身中是否存在这个属性，不会沿着原型链查找

但是有时有的对象没有连接到Object.prototype上，我们可以强硬的进行判断： Object.prototype.hasOwnProperty.call(obj, 'a')

上面我们提到用for in 对对象进行遍历，但是如何遍历属性的值呢？

数组：

```

let arr = [1, 2, 3]

for (let i = 0; i < arr.length; i++) {
    
    console.log(arr[i]) // 1 2 3

}

```

这实际上并不是在遍历值，而是在遍历数组的小标来取值

for in遍历对象是无法直接获取到属性的值的，它实际上遍历的是对象中所有可枚举的属性，你要手动去获取属性值。数组的遍历采用的是数字顺序，即数组的下标，而对象不行，对象属性的属性是不确定的，在任何时候都不要去遍历对象的数字顺序来取值！！！

那么如何直接遍历值而不是数组下标呢，在es6中，提供了遍历数组的方法 for of

```

var arr = [1, 2, 3];

for (let v of arr) {

    console.log(v); // 1 2 3

}

```

### chapter 7 JavaScript中的类
