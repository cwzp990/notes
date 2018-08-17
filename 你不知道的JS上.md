# notes
这里是我最近阅读的关于JS经典书籍的笔记，有一些是别人的理解，拿到一起，方便阅读，如有侵权，请告知我，我立即删除！

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

```js

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

```js

var a = 2;
function foo() {            <--- 添加这一行
    var a = 3;
    console.log(a); // 3
}                           <--- 添加这一行
foo();                      <--- 添加这一行
console.log(a); // 2

```

这样做会导致全局作用域里多了一个foo，我们还可以这样：

```js

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

```js

setTimeout(function (){
    console.log('this is a function')
}, 1000)

```

#### 3.3 立即执行函数

```js

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

```js

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

```js

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

```js

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

```js

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

```js

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

```js

var obj = {};

Object.defineProperty(obj, 'a', {
    value: 2,
    writable: false,        // 不可写   obj.a = 3赋值操作无效, 严格模式下会报错
    configurable: false,    // 不可配置
    enumerable: false        // 不可枚举
})

```

enumerable详细介绍：

```js

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

```js

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

```js

let arr = [1, 2, 3]
for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]) // 1 2 3
}

```

这实际上并不是在遍历值，而是在遍历数组的小标来取值

for in遍历对象是无法直接获取到属性的值的，它实际上遍历的是对象中所有可枚举的属性，你要手动去获取属性值。数组的遍历采用的是数字顺序，即数组的下标，而对象不行，对象属性的属性是不确定的，在任何时候都不要去遍历对象的数字顺序来取值！！！

那么如何直接遍历值而不是数组下标呢，在es6中，提供了遍历数组的方法 for of

```js

var arr = [1, 2, 3];
for (let v of arr) {
    console.log(v); // 1 2 3
}

```

### chapter 7 JavaScript中的类


类/继承描述了一种代码的组织结构形式，是一种在软件中对真实世界中问题领域的建模方法。面向对象编程强调的是数据和操作数据的行为本质上是互相关联的，因此好的设计就是把数据以及和他相关的行为打包（封装）起来，也称为数据结构。

#### 1. 类

然而在JS中，实际上并没有类的概念。类本质上是一种设计模式，JS只是在用一些方法来实现类的功能。

一个类就像建筑工程师手上的蓝图，他不是真实存在的，我们为了获得真实的交互对象，我们必须按照类来建造（也就是实例化）。

类实例是由一个特殊的类方法构造的，我们称之为构造函数，这个方法的任务就是初始化实例需要的所有信息。

#### 2. 类的继承

在面向对象的语言中，你可以先定义一个类，然后定义一个继承前者的类，前者通常称为父类，后者称为子类。子类会包含父类行为的原始副本，但是也可以重写所有继承的行为甚至定义新的行为。

#### 3. 多态

同一个操作作用于不同的对象上，可以产生不同的解释和不同的执行结果

多态最常见的两种实现方式：覆盖、重载

#### 4.混入

在其他语言当中，类表现出来的行为都是复制行为，而JS的对象机制并不会自动执行复制行为，也就是说JS中只有对象，并不存在被实例化的父类。

开发者想出来一个方法来模拟类的复制行为，这个方法就是混入：显式混入和隐式混入。

### chapter 8 原型

在之前我们提到，当你试图引用对象的属性时，会触发get操作，比如obj.a，对于默认的get操作来说，第一步是检查对象本身是否有这个属性，如果有就使用它，如果没有，就要用到原型链了

```js

var another_obj = {b: 3};
var obj = Object.create(another_obj);
obj.b; // 3

```

上述代码将obj的prototype和another_obj关联起来，并通过prototype找到了b的值，如果没有找到值，就会沿着prototype继续找下去

属性的设置

给一个对象设置属性，如 obj.a = 2 ,并不是我们想的那样，仅仅添加一个新属性或修改已有的属性的值，主要过程如下：

如果该对象obj已有这个属性a，则会修改这个属性的值，如果这个属性a不是直接存在于obj中，就会遍历prototype链，如果还是没有，就将这个属性添加到obj上

如果属性a存在于原型链上层时，分下列三种情况：

1. 它没有被标记为只读，那么会直接在obj中添加一个名为a的新属性，它是屏蔽属性

2. 它被标记为只读，那么无法修改已有属性或者在obj上创建屏蔽属性，即忽略这条语句，严格模式下，会抛出错误

3. 它是一个setter，那么调用这个setter，a不会被添加到obj里，也不会重写这个setter

如果我们希望第2、3种也会触发屏蔽，应该使用Object.defineProperty而不是赋值语句 =

如果属性a既存在obj中也存在prototype上层，那么会发生屏蔽。obj中包含的属性a会屏蔽原型链上层所有a属性，obj.a总是会选择原型链中最底层的foo属性

### 1. 再谈类

为什么之前要将一个对象和另一个对象进行关联，这是因为，在JS中，并没有类的概念，JS里只有对象！

多年来，JS一直有一种奇怪的行为在被滥用，那就是模仿类。这个行为实际上是利用了函数的一种特殊性，所有的函数默认都会拥有一个prototype公有的不可枚举的属性，它会指向另一个对象，这个对象就是该对象的原型（obj.prototype）

![prototype-pic2](https://github.com/cwzp990/notes/blob/master/images/prototype_1.png)

在面向类的语言中，类可以被复制（实例化）多次，实例化一个类就是把一个类的行为复制到物理对象中，但是在JS中，并没有类似的复制机制，你不能创建

一个类的多个实例，只能创建多个对象，它们的prtotype关联的是同一个对象。通过new关键字，如 var cat = new Animal(),将cat._proto_和Animal.prototype关联起来，这样一个对象就能通过委托访问另一个对象的属性和函数

### 2. 再谈构造函数

上述代码里谈到每一个函数都有一个prototype属性，指向一个对象，obj.prototype，即他的原型。而这个对象里有一个constructor属性，又指回这个函数。而在我们创建一个对象的时候，如 var cat = new Animal()，创建的对象也有一个constructor属性，指向创建这个对象的函数：cat.constructor === Animal。

在这里，Animal还是一个普通的函数，只不过new会劫持所有普通的函数并用构造对象的形式来调用它。

```js

function Animal() {
    console.log(animal);
}

var cat = new Animal()

```

上述代码中，Animal只是一个普通的函数，在调用new的时候，就构造了一个对象并赋值给了cat，这个调用是一个构造函数的调用，但是Animal本身并不是一个构造函数。在JS中，对于构造函数准确的说法是，所有带new的函数的调用。即函数不是构造函数，当且仅当使用new时，函数调用会变成“构造函数调用”。

JS开发者一直在模仿类的行为：

```js

function Animal(name) {
    this.name = name;
    this.voice = 'miaomiao'
}

Animal.prototype.sayName = function() {
    return this.name
}

var cat1 = new Animal('tom');
var cat2 = new Animal('jerry');
cat1.sayName(); // tom
cat2.sayName(); // jerry

```

在创建的过程中，cat1和cat2内部prototype都会关联到Animal.prototype上，当在cat1，cat2上没找到sayName时，它会委托在Animal.prototype上找到。

而上面提到的cat.constructor === Animal同样委托了Animal.prototype，而Animal.prototype.constructor默认指向Animal，举例说明：

```js

function Animal() {
    ...
}

Animal.prototype = {}                   <--- 我们在这里重新定义了Animal.prototype的指向
var cat = new Animal()
cat.constructor === Animal; // false    <--- 实际上cat并没有constructor属性，它会委托prototype上的Animal.prototype，它也没有（因                                              为我们上面更改了，默认是有的），所以它会继续委托，这次委托给顶端的Object.prototype，                                                它有这个属性，所以指向了Object

cat.constructor === Object; // true

```

constructor并不是一个不可变的属性，它是不可枚举的，但是它的值是可写的（可修改的）

### 3. 原型继承

前面我们了解到cat可以“继承”Animal.prototype并访问Animal.prototype的sayName()方法

```js

function Animal (name) {
    this.name = name;
}

Animal.prototype.sayName = function() {
    return this.name
}

function Person(name, age) {                 <--- 这里同时会将Person.prototype关联到默认的对象                                                                 (Person.prototype),然而我们并不想这样，因此我们需要更改
    Animal.call(this, name);
    this.age = age;
}

// es6以前的写法
// 这里会凭空创建一个新的对象并把对象内部的prototype关联到你指定的Animal.prototype对象，即创建一个Person.prototype，并把它与Animal.prototype相关联
Person.prototype = Object.create(Animal.prototype);     <--- 
// es6的写法
Object.setPrototypeOf(Person.prototype, Animal.prototype);
Person.prototype.sayAge = function() {
    return this.age
}

var tom = new Person('tom', 18)
tom.sayName; // 'tom'
tom.sayAge; // 18

```

这两个做法不正确：

Person.prototype = Animal.prototype

Person.prototype = new Animal()                         // 有副作用 函数Animal会影响Person的后代

### 4. 检查“类”的关系

我们通常使用instanceof来检测一个实例是否继承自它的祖先关系，instanceof左侧是一个对象a，而右侧是一个函数b，意思是，在a原型链中是否有指向b.prototype的对象，而当你想判断两个对象之间的关系时，无法实现。

b.prototype.isPrototypeOf(a)    // true

在上述代码中，我们甚至不需要关心b，我们只需要一个可以用来判断的对象，如b.prototype，意思是，在a的整条原型链中，是否出现过b.prototype。在这里，我们不需要间接引用函数b，它的.prototype属性会自动访问

我们可以直接获取一个对象的原型链，es5中标准方法为

Object.getPrototypeOf(a) === b.prototype

也可以这样写：

a._proto_ === b.prototype

#### 5. 对象关联

prototype就是存在于对象中的一个内部机制，实际上原型链的作用是：如果在对象上没有找到需要的属性或者方法，引擎就会继续沿着原型链往下查找，如果在后者依旧没有找到，就会沿着后者继续往下查找，以此类推。

我们知道，使用new关键字会生成.prototype和.constructor引用，而Object.create不会，它可以完美的创建我们需要的关联关系。

注：

```js

var another_obj = {
    cool: function() {
        console.log('cool');
    }
}

var obj = Object.create(another_obj);
obj.cool; // 'cool'             <--- 不推荐这种写法，
obj.doCool = function() {
    this.cool();                <--- 内部委托
}
obj.doCool(); // 'cool'

```

### chapter 8 行为委托

#### 1. 类理论

类的设计模式大体是这样的：定义一个通用的父类，如Animal，在Animal中定义所有任务都有的行为，然后定义子类cat，它继承自Animal并且会添加一些特殊的行为来处理对应的任务。

在类设计模式中，鼓励在继承时使用方法重写和多态。你会发现许多行为可以先“抽象”到父类然后再用子类进行转化（重写）。

我们可以实例化子类cat的一些副本然后使用这些实例来执行任务，这些实例会复制父类定义的通用行为以及子类自己定义的特殊行为。在构造完成后，你通常只需要操作这些实例而不是类，因为每个实例都有你需要完成任务的所有行为。

### 2. 委托理论

首先我们定义一个Animal的对象，它包含所有任务都可以使用的具体行为。接着对于每一个任务，你都会定义一个对象来存储对应的数据和行为。他会把特定的任务对象都关联到Animal功能对象上，让它们在需要的时候可以进行委托。

```

Animal = {

    name: function(name) {

        this.name = name;

    }

}

cat = Object.create(Animal);

cat.sayName = function(name, voice) {

    this.name(name);

    this.voice = 'miaomiao'

}

```

下面的代码在chrome和firefox上得出的结果并不相同：

```

function Foo() {}

var a = new Foo();

a;      // 在chrome上显示 Foo {}，在firefox上显示 Object {}

```

原因：

```

var foo = {};

var a = Object.create(foo);

// chrome下：foo.prototype.constructor = function newFoo() {};

a; // Object {}

Object.definePorperty(foo, 'constructor', {

    enumerable: false,

    value: function newFoo() {}

})

a; // newFoo {}         <--- chrome使用了a.constructor.name进行跟踪

```
