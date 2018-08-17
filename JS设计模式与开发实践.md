# notes
这里是我最近阅读的关于JS经典书籍的笔记，有一些是别人的理解，拿到一起，方便阅读，如有侵权，请告知我，我立即删除！

## 1. JS设计模式与开发实践

### chapter 1 面向对象的JavaScript

#### 1.1 动态类型语言

我们先来看一段代码

```js

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

```js

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

```js

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

```js

var Dog=function(){}
Dog.prototype.sound=function(){
	console.log('汪汪汪')
}

makeSound(new Dog());		// 汪汪汪

```

#### 1.3 JS的多态

多态的思想实际上是把“做什么”和“谁去做分离开来”，要实现这一点，归根结底先要消除类型之间的耦合关系。

对象应该做什么不是临时决定的，而是事先约定好了的。每个对象应该做什么，已经成了该对象的一个方法，被安排在对象的内部，每个对象负责他们自己的行为。所以这些对象可以根据同一个消息，有条不紊的分别进行各自的操作。

```js

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

```js

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

```js

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

```js

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

```js

var obj={
	name: 'sven';
}

var A=function(){};
A.propotype=obj;

var a=new A();
console.log(a.name)

```

### 1.5 call和apply

apply接受两个参数，第一个参数指定函数体内this对象的指向，第二个参数为一个带下标的集合，这个集合可以是数组也可以是类数组

call传入参数不固定，第一个参数指定this对象的指向，第二个参数往后，每个参数被依此传入函数

当使用call或apply的时候，如果我们传入的第一个参数为null，函数体内的this会指向默认的宿主对象，浏览器里是window

### 1.6 call和apply的用途

1. 改变this指向

```js

var obj1={
	name: 'sven';
}
var obj2={
	name: 'anne';
}

window.name='window';

var getName=function(){
	alert(this.name);
}

getName()			// window
getname.call(obj1)	// sven
getname.call(obj2)	// anne

```

2. Function.prototype.bind

```js

Function.prototype.bind=function(context){
	var self=this;								// 保存原函数
	var context=[].shift.call(arguments);		// 需要绑定的this上下文
	var args=[].slice.call(arguments);			// 剩余参数转成数组
	return function(){							
		return self.apply(context, [].cancat.call(args,[].slice.call(arguments))); 
												// 执行新函数时，会把之前传入的context当作新函数体内的this
												// 并且组合两次分别传入的参数，作为新函数的参数
	}
}

var obj={
	name: 'sven';
}

var func=function(){
	alert(this.name);							// sven
	alert([a,b,c,d,e]);							// [1,2,3,4]
}.bind(obj,1,2)

func(3,4)

```

3. 借用其他对象的方法

```js

// 在构造函数中，实现继承

var A=function(name){
	this.name=name;
}

var B=function(){
	A.apply(this,arguments);
}

B.prototype.getName=function(){
	return this.name;
}

var b=new B('sven');
console.log(b.getName());		// sven

```

### chapter 3 闭包和高阶函数

### 3.1 闭包

1. 封装变量

闭包可以帮助把一些不需要暴露在全局的变量封装成“私有变量”

2. 延续局部变量的寿命

### 3.1 闭包和面向对象设计

```js

var extent=function(){
	var value=0;
	return {
		call: function(){
			value++;
			console.log(value)
		}
	}
}

var extent=extent();
extent.call();			// 1
extent.call();			// 2
extent.call();			// 3

// 面向对象的写法

var extent={
	value:0,
	call:function(){
		this.value++;
		console.log(this.value)
	}
}

// 或者

var Extent=function(){
	this.value=0;
}

Extent.prototype.call=function(){
	this.value++;
	console.log(this.value);
}

var extent=new Extent();
extent.call();

```

### 3.2 用闭包实现命令模式

```js

// 面向对象的方式

var Tv={
	open: function(){
		console.log('open tv');
	},
	close: function(){
		console.log('close tv');
	}
}

var OpenTvCommand=function(receiver){
	this.receiver=receiver;
}

OpenTvCommand.prototype.execute=function(){
	this.receiver.open();			// 执行打开电视命令
}

CLoseTvCommand.prototype.excute=function(){
	this.receiver.close();			// 执行关闭电视命令
}

var setCommand=function(command){
	document.getElementById('execute').onclick=function(){
		command.execute();			// 输出：open tv
	}

	document.getElementById('undo').onclick=function(){
		command.undo();				// 输出：close tv
	}
}

setCommand(new OpenTvCommand(Tv));

```

命令模式的意图是把请求封装为对象，从而分离请求的发起者和请求的接收者（执行者）之间的耦合关系。在命令被执行之前，可以预先往命令对象中植入命令的接收者。

```js

// 闭包的方式

var Tv={
	open: function(){
		console.log('open tv');
	},
	close: function(){
		console.log('close tv');
	}
}

var createCommand=function(receiver){
	var excute=function(){
		return receiver.open();		// 执行打开电视命令
	}

	var undo=function(){
		return receiver.close();	// 执行关闭电视命令
	}

	return {
		execute:execute,
		undo:undo
	}
}

var setCommand=function(command){
	document.getElementById('execute').onclick=function(){
		command.execute();			// 输出：open tv
	}

	document.getElementById('undo').onclick=function(){
		command.undo();				// 输出：close tv
	}
}

setCommand(createCommand(Tv));

```

### 3.3 高阶函数

高阶函数必须满足下列条件之一：

1. 函数可以作为参数被传递
2. 函数可以作为返回值输出

#### 3.3.1 函数作为参数传递

1. 回调函数

2. Array.prototype.sort

它接收一个函数当作参数，这个函数里面封装了数组元素的排序规则，从Array.prototype.sort的使用可以看到，我们的目的是对数组进行排序，这是不变的部分，而使用什么规则进行排序是可变的。因此，将可变的部分封装在函数参数里，动态传入Array.prototype.sort：

```js

// 从小到大
[1,4,3].sort(function(a,b){
	return a-b;				// [1,3,4]
})

// 从大到小
[1,4,3].sort(function(a,b){
	return b-a;				// [4,3,1]
})

```

#### 3.3.2 函数作为返回值输出

```js

// 判断数据类型

var isType=function(type){
	return function(obj){
		return Object.prototype.toString.call(obj)==='[Object'+ type +']';
	}
}

// 单例模式

var getSingle=function(fn){
	var ret;
	return function(){
		return ret || (ret=fn.apply(this, arguments));
	}
}

```

### 3.3 高级函数实现AOP

AOP(面向切面编程)的主要作用是把一些跟核心业务逻辑模块无关的功能抽离出来，这些跟业务逻辑无关的功能通常包括日志统计、安全控制、异常处理等。把这些功能抽离出来之后，再通过“动态植入”的方式掺入业务逻辑模块中。通常，在JS中实现AOP，都是指把一个函数“动态植入”到另一个函数之中，具体实现的技术有很多，本书我们通过扩展Function.prototype来做到这一点。

```js

Function.prototype.before=function(beforefn){
	ver _self=this;
	return function(){
		beforefn.apply(this, arguments);
		return _self.apply(this, arguments);
	}
}

Function.prototype.after=function(afterfn){
	var _self=this;
	return function(){
		var ret=_self.apply(this, arguments);
		afterfn.apply(this, arguments);
		return ret;
	}
}

var func=fuction(){
	console.log(2);
}

func=func.before(function(){
	console.log(1);
}).after(function(){
	console.log(3);
});

func();

```

### 3.3 高级函数的其他应用

1. currying

首先我们讨论的是函数柯里化。currying又称部分求值。一个currying的函数首先会接受一些参数，接受的这些参数以后，该函数并不会立即求值，而是继续返回另一个函数，刚才传入的参数在函数形成的闭包中被保存起来。待到函数被真正需要求值的时候，之前传入的所有参数都会被一次性用于求值。

```js

// 计算月开销函数
var monthlyCost=0;
var cost=function(money){
	monthlyCost+=money;
}

cost(100);
cost(200);
cost(300);

// 输出60
// currying
var currying=function(fn){
	var args=[];
	return function(){
		if (arguments.length===0){
			return fn.apply(this,args);
		} else {
			[].push.apply(args,arguments);
			return arguments.callee;
		}
	}
}

var cost=(function(){
	var money=0;
	return function(){
		for (i=0,l=arguments.length;i<l;i++){
			money+=arguments[i];
		}
		return money;
	}
})();
var cost=curry(cost);			// 转化成currying函数
cost(100);						// 未真正求值
cost(200);						// 未真正求值
cost(300);						// 未真正求值
alert(cost());					// 输出600 

```

uncurrying

```js

Function.prototype.uncurrying=function(){
	var self=this;			// 此时是Array.prototype.push
	return function(){
		var obj=Array.prototype.shift.call(arguments);
							// obj={length: 1, 0: 1}
							// arguments的第一个对象被截去，剩下[2]
		return self.apply(obj, arguments);
							// 相当于 Array.prototype.push.apply(obj, 2)
	}
}

var push=Array.prototype.push.uncurrying();
var obj={
	'length': 1,
	'0': 1
};

push(obj, 2);
console.log(obj);			// 输出 {0:1,1:2,length:2}

```

3. 函数节流

window.onresize事件

我们给window对象绑定了resize事件，当浏览器窗口大小被拖动而改变的时候，这个事件触发的频率非常之高，如果我们在window.onresize事件函数里有一些跟dom节点相关的操作，而跟dom节点相关的操作是非常消耗性能的，这是浏览器可能因吃不消而导致卡顿。

同理，还有mouseover事件等

函数节流的原理：上述场景导致的问题都是函数被触发的频率太高。我们可以利用setTimeout将即将被执行的函数延迟一段处理，如果这次延迟执行还没有完成，则忽略接下来调用该函数的请求。

```js

var throttle=function(fn, interval){
	var _self=fn,							// 保存需要被延迟执行的函数的引用
		timer,								// 定时器
		firstTime=true;						// 是否第一次调用
	return function(){
		var args=arguments,
			_me=this;
		if(firstTime){						// 第一次调用不需要延迟执行
			_self.apply(_me,args);
			return firstTime=false;
		}
		if(timer){							// 如果定时器还在，说明前一次延迟执行还没有完成
			return false;
		}
		timer=setTimerout(function(){		// 延迟一段时间执行
			clearTimerout(timer);
			timer=null;
			_self.apply(_me,args);
		}, interval || 500);
	}
}

window.onresize=throttle(function(){
	console.log(1);
}, 500)

```

4. 分时函数

当我们在页面中渲染一个列表时，可能要一次性往页面中创建成百上千个节点，这种短时间内往页面中大量添加dom节点显然也会让浏览器吃不消，导致浏览器卡顿或假死。解决方法是，我们可以让创建节点的工作分批次进行，如把1s创建1000个节点分成每200ms创建8个节点。

```js

var timeChunk=function(ary, fn, count){
	var obj, t;
	var len=arr.length;
	var start=function(){
		for(var i=0,l<Math.min(count || 1, ary.length);i++){
			var obj=ary.shift();
			fn(obj);
		}
	}
	return function(){
		t=setInterval(function(){
			if (ary.length===0){
				return clearInterval(t)
			}
			start();
		}, 200)
	}
}

// 分批次往页面中创建8个节点
var ary=[];
for(var i=1;i<=1000; i++){
	ary.push(i);
}

var renderFiendList=timerChunk(ary, function(n){
	var div=document.createElement('div');
	div.innerHtml=n;
	document.body.appendChild(div);
}, 8);

renderFriendList();

```

5.惰性懒加载