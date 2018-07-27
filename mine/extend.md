## 继承

### 原型链继承

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend1.png)

这种继承方式有两种缺点：

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend2.png)

1. 引用类型的属性会被所有实例共存！
2. 在创建Child实例时，不能向Parent传参

### 借用构造函数(经典继承)

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend3.png)

优点：

1. 避免引用类型的属性被所有实例共享

2. 可以在Child中向Parent传参

举个例子：

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend4.png)

缺点： 方法都在构造函数中定义，每次创建实例都会创建一遍方法

### 组合继承

原型链继承+组合继承

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend5.png)

这也是JS中最常用的继承方式

### 原型式继承

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend6.png)

就是 ES5 Object.create 的模拟实现，将传入的对象作为创建的对象的原型

缺点：包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样

注意：修改person1的值，person2.name并未发生改变，并不是因为person1和person2有独立的name值，而是因为person1.name = 'person1'，给person1添加了name值，并非修改了原型上的name值

### 寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最终返回对象

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend7.png)

缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法

### 寄生组合式继承

为了方便，这是上面提到的组合继承代码：

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend5.png)

组合继承最大的缺点就是会调用两次父构造函数

一次是设置子类型实例原型的时候： Child.prototype = new Parent()

一次是在创建子类型实例的时候： var child = new Child('kevin', '18')

回想下 new 的模拟实现，其实在这句中，我们会执行：

Parent.call(this, name);

在这里，我们又会调用了一次 Parent 构造函数。

所以，在这个例子中，如果我们打印 child1 对象，我们会发现 Child.prototype 和 child1 都有一个属性为colors，属性值为['red', 'blue', 'green']。

那么我们该如何精益求精，避免这一次重复调用呢？

如果我们不使用 Child.prototype = new Parent() ，而是间接的让 Child.prototype 访问到 Parent.prototype 呢？

看看如何实现：

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend8.png)

最后我们封装一下这个方法：

![Image text](https://github.com/cwzp990/notes/blob/master/images/extend9.png)

这种方式的高效率体现它只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。
