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



## Module机制

CJS

Module本质就是对象

#### 加载机制：

路径分析+文件定位+编译执行

路径分析：通过文件路径名获取模块引用

加载顺序：

1. 载入内置（核心）模块
2. 载入文件模块
3. 载入文件目录模块
4. 载入node_modules模块 => index.js
5. 自动缓存已载入模块
6. 二次加载一律以缓存为最高优先级

#### module.exports 和 exports

* 初始时exports和module.exports指向同一快内存区域
* 模块导出的是module.exports，exports只是对他的引用，修改exports值可以改变module.exports的值
* 尽量使用module.exports.

#### import & require

* CJS输出值的拷贝，即模块内部的变化影响不到已经输出的值， ES6输出值的引用（原始值变化，import加载值变化）一个例子如下（类似Unix符号连接）

  ```
  // lib.js
  export let counter = 3;
  export function incCounter() {
    counter++;
  }
  
  // main.js
  import { counter, incCounter } from './lib';
  console.log(counter); // 3
  incCounter();
  console.log(counter); // 4
  ```



import静态加载，require动态加载

静态加载在代码编译时执行（即在运行前执行，被导入的模块不论位置如何都会先执行），动态加载代码编译后运行时执行（脚本运行完后对象才会生成）

```
// index.js
console.log('running index.js');
import { sum } from './sum.js';
console.log(sum(1, 2));

// sum.js
console.log('running sum.js');
export const sum = (a, b) => a + b;
输出
running sum.js, running index.js, 3。
```
