### 高并发策略

其他：提供多线程模型，系统切换线程来弥补同步IO调用时间开销。当线程多了，创建/销毁线程以及线程间切换开销也很大。

Node.js使用单线程模型(JS运行环境单线程)：适合IO密集型任务

### 异步非阻塞IO

阻塞/非阻塞对于OS内核而言，发生在等待资源阶段

* 阻塞：发起IO操作后，收到相应或是超时才处理其他事情。
* 非阻塞：如果数据没有就绪，立刻返回（标志数据资源不可用），CPU时间切片可以用来做其他事。





### event loop

启动时初始化event loop。每个包含如下阶段

1. timers：执行setTImeout/setInterval等预定callback

2. I/O callback：执行操作系统回调
   3. 闲置idle，prepare阶段：仅系统内部使用
4. 轮询poll 获取新I/O
5. check：执行setImeediate的callback
6. close callbacks  socket.on(‘close’, callback)



timers & poll & check

##### timers

timers执行setTimeout/setInterval回调，由poll控制

##### poll

![GitHub](https://user-gold-cdn.xitu.io/2020/3/2/1709951e65ffe00e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

最后等待回调阶段，会有一个超时设置，防止一直等待下去。一段时间后自动进入check阶段

##### check

执行setImmediate





一个Tick也就是一个事件循环体

setImmediate表示主线程执行完后立即执行，该轮事件循环最后立即执行，先于下一轮事件循环中的timers执行

setImmediate采用check观察者，process.nextTick采用idle，在setTimeout/setInterval前执行

setImmidate和setTimeout，如果都在主模块中调用，执行先后取决于系统性能，如果都不在主模块调用，setImmediate回调会先执行

process.nextTick不属于任何阶段，属于过度 优先级高于promise



* process.nextTick会优先于setImmediate执行
* process.nextTick不能取消，setImmediate可以用clearImmediate取消

![这里写图片描述](https://img-blog.csdn.net/20161204121951097)

![这里写图片描述](https://img-blog.csdn.net/20161204132234376)

一个🌰：

``````javascript
async function async1(){
    console.log('async1 start')
    await async2()
    console.log('async1 end')
  }
async function async2(){
    console.log('async2')
}
console.log('script start')
setTimeout(function(){
    console.log('setTimeout0') 
},0)  
setTimeout(function(){
    console.log('setTimeout3') 
},3)  
setImmediate(() => console.log('setImmediate'));
process.nextTick(() => console.log('nextTick'));
async1();
new Promise(function(resolve){
    console.log('promise1')
    resolve();
    console.log('promise2')
}).then(function(){
    console.log('promise3')
})
console.log('script end')
``````

结果：

```
script start
async1 start
async2
promise1
promise2
script end
nextTick
promise3
async1 end
setTimeout0
setTimeout3
setImmediate
```





## Buffer

#### Buffer.alloc &  Buffer.allocUnsafe

allocUnsafe创建的Buffer实例底层没有经过初始化，新建的Buffer内容是未知的。Buffer.alloc创建以零初始化的Buffer实例

* Buffer占用内存不由V8分配，在Node.js的C++层面申请，（堆外内存）



## V8 GC

大部分对象生命周期很短，少部分对象生命周期很长。因此V8将堆分为新生代/老生代两种。新生代中对象较小1-8MB，GC频繁。幸存下来的会被提升到老生代空间。

### 新生代scavenge算法

复制算法，一分为二，两个相等大小的from-space和to-space。将from-space中的存活对象复制，移动到to-space。复制后，清理from-space，空间身份发生对调。to-space变为from-space，from-space变为to-space

* 晋升为老生代条件：经历过一次Scavenge筛选，To空间内存使用超过25%



### 老生代Mark-sweep和Mark-compact算法

老生代空间中对象至少经历过一次scavenge回收，存活几率大。scavenge浪费大量空间。

Mark-sweep分为标记/清除两个步骤，标记活对象并把未标记的死对象进行清除。

问题：被清除的对象遍布各内存地址，产生内存碎片。

Mark-comact（标记整理）一边标记一边把活对象向内存一端移动，移动完成后，直接清除内存边界外的内存。



## Module机制

CJS

Module本质就是对象

#### 加载机制：

路径分析+文件定位+编译执行

加载顺序：

1. 系统缓存：模块被执行后会进行缓存
2. 系统模块：原生模块（核心模块会省略路径分析和文件定位）
3. 文件模块



#### module.exports 和 exports

* 初始时exports和module.exports指向同一快内存区域
* 模块导出的是module.exports，exports只是对他的引用，修改exports值可以改变module.exports的值
* 尽量使用module.exports.
