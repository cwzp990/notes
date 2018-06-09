# notes
这里是我最近阅读的关于JS经典书籍的笔记，有一些是别人的理解，拿到一起，方便阅读，如有侵权，请告知我，我立即删除！

## 1. JS设计模式与开发实践

### chapter 1 面向对象的JavaScript

#### 1.1 动态类型语言

我们先来看一段代码

```

var dark = {

	duckSinging:function(){
		console.log('嘎嘎嘎')
	}
}

var chicken = {

	duckSinging:function(){
		console.log('嘎嘎嘎')
	}
}

var choir = [];    // 合唱团

var joinChoir=function(){

	if (animal && typeof animal.duckSinging==='function'){
		choir.push(animal);
		console.log('恭喜加入合唱团')
	}
}

joinChoir(duck);		// 恭喜加入合唱团

joinChoir(chicken);		// 恭喜加入合唱团

```

我们看到，对于加入合唱团的动物，我们不关注它们的类型，只要拥有duckSinging方法，如果下次加入的是一只小狗，它也会鸭子叫，那么它也能加入。

#### 1.2 多态

多态的含义是：同一操作作用于不同的对象上，可以产生不同的解释和不同的执行结果。也就是说，给不同的对象发送同一个消息的时候，这些对象会根据这个消息分别给出不同的反馈。

我们来看一段代码：

```

var makeSound=function(animal){

	if (animal instanceof Duck) {
		console.log('嘎嘎嘎')
	} else if (animal instanceof Chicken) {
		console.log('咯咯咯')
	}
}

var Duck=function(){}
var Chicken=function(){}

makeSound(new Duck());		// 嘎嘎嘎
makeSound(new Chicken());	// 咯咯咯

```

上面代码就体现了“多态性”，但是有一个缺陷，如果后来又加入了一只动物，比如狗，此时我们就需要改动makeSound函数，改动代码是危险的，修改的地方越多，出错的可能性就越大。

多态背后的思想是将“做什么”和“谁去做以及怎样做”分离开来，也就是将“不变的事物”和“可变的事物”分离开来。

下面是改动后的代码：

```

var makeSound=function(animal){
	
	animal.sond();
};

var Duck=function(){}

Duck.prototype.sound=function(){
	
	console.log('嘎嘎嘎');
}

var Chicken=function(){}

Chicken.prototype.sound=function(){

	console.log('咯咯咯')
}

makeSound(new Duck());		// 嘎嘎嘎

makeSound(new Chicken());	// 咯咯咯

```

如果这时加入了一只狗，我们只需添加这段代码即可：

```

var Dog=function(){}

Dog.prototype.sound=function(){

	console.log('汪汪汪')
}

makeSound(new Dog());		// 汪汪汪

```

#### 1.3 JS的多态

多态的思想实际上是把“做什么”和“谁去做分离开来”，要实现这一点，归根结底先要消除类型之间的耦合关系。

对象应该做什么不是临时决定的，而是事先约定好了的。每个对象应该做什么，已经成了该对象的一个方法，被安排在对象的内部，每个对象负责他们自己的行为。所以这些对象可以根据同一个消息，有条不紊的分别进行各自的操作。

```

var googleMap=function(){
	
	show:function(){
		console.log('开始渲染谷歌地图')
	}
}

var baiduMap=function(){
	
	show:function(){
		console.log('开始渲染百度地图')
	}
}

var renderMap=function(map){

	if(map.show instanceof Function){
		map.show()
	}
}

renderMap(googleMap)
render(baiduMap)

```

#### 1.4 JS的封装

1.封装数据

JS并没有提供关键字来支持这个特性，我们只能依赖变量的作用域来实现封装特性，除了ES6中提供的let之外，一般我们通过函数来创建作用域。

```

var Object=(function(){

	var _name='sven';			// 私有变量
	return {
		getName:function(){		// 公开方法
			return _name;
		}
	}
})()

console.log(Object.getName())	// sven

console.log(Object._name)		// undefined

```

2. 封装实现

封装的目的是将信息实现，封装不仅仅是隐藏数据，还包括隐藏实现细节、设计细节以及隐藏对象的类型等。

从封装实现细节来讲，封装使得对象内部的变化对其他对象而言是透明的，也就是不可见的。其他对象或者用户都不关心它的内部实现。

封装使得对象之间的耦合性松散，对象之间只通过暴露的api接口来通信。

3. 封装变化

拿创建型模式来说，要创建一个对象，是一种抽象行为，而具体创建什么对象则是可以变化的，创建型模式的目的就是封装创建对象的变化。而结构型模式封装的是对象之间的组合关系。行为型模式封装的是对象行为的变化。

通过封装变化的方式，把系统中稳定不变的部分和容易变化的部分隔离开来，在系统的演变过程中，我们只需要替换那些容易变化的部分，这些部分是已经封装好的，替换起来也很方便。这可以最大程度上保证系统的稳定性和可扩展性。

4.使用克隆的原型模式

如果我们要创建一个对象，一种方法是指定它的类型，然后通过类来创建对象。原型模式是找到一个对象，然后通过克隆来创建一个一模一样的对象。

既然原型模式是通过克隆来创建对象的，那么如果需要一个一模一样的对象，我们就可以使用原型模式。JS中，提供了Objec.create方法。

举个栗子

```

var Plane=function(){

	this.blood=100;
	this.attacklevel=1;
	this.defenselevel=1;
}

var plane=new Plane();

plane.blood=500;

plane.attacklevel=10;

plane.defenselevel=7;

var clonePlane=Object.create(plane);

console.log(clonePlane.blood)		// 500

console.log(clonePlane.attacklevel)	// 10

console.log(clonePlane.defenselevel)// 7

```

注意：原型模式真正目的并非在于需要得到一个一模一样的对象，而是提供了一种便捷的方式去创建某个类型的对象，克隆只是创建这个对象的过程和手段。

JS本身就是使用原型模式搭建整个面向对象系统的，在JS中不存在类的概念，对象也并非从类中创建出来的，所有的JS对象都是从某个对象上克隆出来的。

#### 1.4 JS的原型继承

JS在设计的时候，模仿JAVA引入了两套类型机制：基本类型和对象类型。

我们不能说JS中所有数据都是对象，但可以说绝大多数数据都是对象，那么在JS中应该有一个根对象，这些对象追根溯源都来源于这个根对象。

实际上，JS中的根对象是Object.prototype对象，我们在JS中遇到的每个对象，都是从Object.prototype对象克隆出来的，Object.prototype对象就是它们的原型。

要找到一个对象，不是通过实例化类，而是找到一个对象作为原型克隆它，如：

```

function Person(name){

	this.name=name;
}

Person.prototype.getName=function(){
	
	return this.name;
}

var a=new Person('sven');

console.log(a.name);			// sven
console.log(a.getName());		// sven

```

对象会记住它们的原型

如果请求可以在一个链条中依此向后传递，那么每个节点都必须知道它的下一个节点。同理，JS语言中的原型链查找机制，每个对象至少应该先记住它自己的原型。

我们一直在讨论对象的原型，实际上，应该说是对象的构造器的原型

JS给对象提供了一个名为_proto_的隐藏属性，某个对象的_proto_属性会默认指向它的构造器的原型对象，即{constructor}.prototype

在JS中，每个对象都是从Object.prototype对象克隆而来的，如果是这样的话，我们只能得到单一的继承关系，即每个对象都继承自Object.prototype对象，这样的对象系统将会非常受限。

实际上，JS的对象最初都是由Object.prototype对象克隆而来的，但对象构造器的原型不仅限于Object.prototype上，而是可以动态指向其他的对象。

```

var obj={
	name: 'sven';
}

var A=function(){};

A.propotype=obj;

var a=new A();

console.log(a.name)

```