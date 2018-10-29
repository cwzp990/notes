## 单线程

同一时间只能做一件事情

原因：避免dom渲染冲突

解决方案：异步(无奈的选择，但是很高效)

浏览器需要渲染dom，js可以修改dom，js执行的时候，浏览器dom渲染会暂停，两段js也不能同时执行(引起dom修改冲突)

webworker支持多线程，但是不能访问dom

## event-loop 事件轮询

同步代码直接执行，异步函数先放在异步队列中(有延时的延时函数需要等到延时结束后再放入)，所有同步函数执行完毕，轮询执行异步队列的函数

异步执行的运行机制如下：

所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
主线程不断重复上面的第三步。
主线程从"任务队列"中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为 Event Loop（事件循环）。只要主线程空了，就会去读取"任务队列"，这就是 JavaScript 的运行机制。这个过程会循环反复。以下这张图可以很好说明这点。

## Promise

鉴于promise比较重要，单独开一章说一下

### promise里面的函数是立即执行的，然后跳过promise执行外面的语句，最后执行then，比如：

![Image text](https://github.com/cwzp990/notes/blob/master/images/promise1.png)

### promise的三种状态

promise有三种状态：pending(等待)、resolved(成功)、rejected(失败)

当Promise刚创建完成时,处于pending状态;当Promise中的函数参数执行了resolve后,Promise由pending状态变成resloved状态;如果Promise的函数参数中执行的是reject方法,那么Promise会有pending状态变成rejected状态

### promise状态的不可逆性

![Image text](https://github.com/cwzp990/notes/blob/master/images/promise2.png)

Promise的状态一旦变成resolved或rejected时,Promise的状态和值就固定下来了,无论后续再怎么调用reslove或是reject方法,都不能改变它的状态和值.所以,p1中reslove('成功2!')并不能将p1的值更改为成功2!,p2中reject('失败!')也不能将p2的状态由resolved改变为rejected.

### 链式调用

![Image text](https://github.com/cwzp990/notes/blob/master/images/promise3.png)

Promise对象的then方法返回一个新的Promise对象,所以可以通过链式调用then方法.then方法接受两个函数作为参数,第一个参数是Promise执行成功时的回调,第二个参数是Promise执行失败时的回调.两个函数只会有一个被调用,函数返回值将用作创建then返回的Promise对象.这两个参数的返回值可以是下面三种情况的一种:

+ return一个同步的值,或者undefined(当没有返回一个有效值时,默认返回undefined),then方法将返回一个resloved状态的Promise对象,Promise对象的值就是这个返回值.

+ return另一个Promise,then方法将根据这个Promise的状态和值创建一个新的Promise对象返回.

+ throw一个同步异常,then方法将返回一个rejected状态的Promise,值是该异常.

### promise回调异步性

![Image text](https://github.com/cwzp990/notes/blob/master/images/promise4.png)

Promise接受的函数参数是同步执行的,但是then方法中的回调函数则是异步的,因此,成功会在后面输出.

## promise出现的原因

在promise出现之前，我们都是这样处理一个异步请求的：

```js
请求1(function(请求结果1){
  处理请求结果1
})

// 但是我们通常有这种情况，我们需要根据第一个网络请求的结果，再去执行第二个网络请求：
请求1(function(请求结果1){
  请求2(function(请求结果2){
  处理请求结果2
})
```

这个带来的结果就是可读性差，后续维护性差等毛病。而promise就是用来解决回调地狱的痛点，用一种更好的方式来解决异步嵌套的问题，类似这样：

```js
let 请求结果1 = 请求1();
let 请求结果2 = 请求2(请求结果1); 
let 请求结果3 = 请求3(请求结果2); 
let 请求结果4 = 请求2(请求结果3); 
let 请求结果5 = 请求3(请求结果4);
```

还是上述例子，我们来看看promise的写法：
```js
new Promise(请求1)
    .then(请求2(请求结果1))
    .then(请求3(请求结果2))
    .then(请求4(请求结果3))
    .then(请求5(请求结果4))
    .catch(处理异常(异常信息))
```

如果我们后续任务是一个异步任务，需要return一个新的promise对象，如果是同步任务，只需return一个结果即可

### promise的使用总结

1. 首先需要初始化一个promise对象，可以通过两种方式创建，这两种方法都会返回一个promise对象

+ new Promise(fn)

+ Promise.resolve(fn)

2. 然后调用上一步返回的promise对象的then方法，注册回调函数。then中的回调函数可以有一个参数，也可以不带参数。如果then中的回调函数依赖
上一步的返回结果，就要带上参数。如：

```js
   new Promise(fn)
    .then(fn1(value）{
        //处理value
    })
```

3. 最后注册catch异常处理函数，处理前面回调中可能抛出的异常

### pormise与事件循环

Promise在初始化时，传入的函数是同步执行的，然后注册 then 回调。注册完之后，继续往下执行同步代码，在这之前，then 中回调不会执行。同步代码块执行完毕后，才会在事件循环中检测是否有可用的 promise 回调，如果有，那么执行，如果没有，继续下一个事件循环。
