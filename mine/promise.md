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

1). return一个同步的值,或者undefined(当没有返回一个有效值时,默认返回undefined),then方法将返回一个resloved状态的Promise对象,Promise对象的值就是这个返回值.
2). return另一个Promise,then方法将根据这个Promise的状态和值创建一个新的Promise对象返回.
3). throw一个同步异常,then方法将返回一个rejected状态的Promise,值是该异常.

### promise回调异步性

![Image text](https://github.com/cwzp990/notes/blob/master/images/promise4.png)

Promise接受的函数参数是同步执行的,但是then方法中的回调函数则是异步的,因此,成功!会在后面输出.