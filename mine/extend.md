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

