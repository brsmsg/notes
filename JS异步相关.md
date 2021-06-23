## 进程&线程

进程是CPU分配资源最小单位，拥有资源&独立运行

线程是CPU调度最小单位，一个进程中有多个线程

多线程指一个进程内的单和多 

浏览器多进程，一个tab就是一个独立进程

## 为什么不设计成多线程？

多线程不加锁的情况下，会导致DOM渲染不可预期（一个加节点，一个删节点）

GUI线程和 JS引擎线程互斥。执行一个另一个就会挂起。如果同时修改元素并同时渲染界面，渲染前后获得元素可能不一致。



### EVENT LOOP

同步任务在引擎线程上执行，行成一个执行栈。触发线程管理任务队列，将异步任务放到任务队列中，同步任务执行完后，从任务队列中依次添加到执行栈中开始执行。 之后如此循环

![](https://user-gold-cdn.xitu.io/2019/8/21/16cb1d70e5120bea?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



宏任务：每次执行栈执行的代码就是宏任务，如果栈中没有就到事件队列中获取，（主代码块，包括setTimeout 以及 setInterval）

执行完宏任务后，立即执行微任务： Promise()/ process.nextTick属于微任务

之后开始渲染。

渲染完之后开始下一个宏任务（从事件队列获取）



NodeJS的事件循环





### async await

以同步写法彻底消灭回调函数



核心：

* 使用async函数会返回一个promise，保证返回值为promise
* 如果不使用await，可以使用.then()来调用async
* async关键字将函数转为promise,使用promise.resolve()转换直接量
* 本身返回Promise的函数加不加async都一样

await 关键字：

* 可以在任何返回promise的函数时使用await,暂停代码执行，直到promise返回。
* 等待一个实际返回值，后面不仅可以接promise，也可以等待直接值
* ！！！！如果等到的是一个promise对象，阻塞代码，等着promise resolve，然后取resolve的值，作为await运算结果。 Promise也可能返回rejected，因此最好放try catch中

优势： 处理then链。

复习一下：Promise的then方法返回新的Promise实例。如果前一个回调函数返回Promise

.then中，return返回一个值，将返回的值作为下一个then中回调函数的参数值，如果返回的是一个Promise对象，将这个Promise接收状态的回调函数中参数值作为下一个then回调函数的参数值

