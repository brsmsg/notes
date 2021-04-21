# Promise

### 为什么要使用Promise

* 处理多个异步请求嵌套，代码难以维护，对每一次请求结果进行处理，产生回调地狱。
* 开发时需要同步请求最终的结果，增加可阅读性和可维护性
* Promise通过链式调用消灭掉嵌套，使用.all方法合并多个任务的错误处理。

### 基础问题

Promise构造函数接受函数作为参数，该函数两个参数分别是resolve和reject（这两个函数由引擎提供，不需要自己实现）

resolve函数将Promise对象从pending->fulfilled

reject函数从pending -> rejected

即：回调中结果操作正常就调用resolve，结果错误调用reject



### 几个方法

#### 1. then()

状态改变调用

返回新promise实例，因此可采用链式调用。通过杨接受两个回调作为参数，第一个是状态变为fulfilled之后的调用，第二个是状态变为rejected的调用。第二个参数可选

#### 2. catch()

异步抛出异常时调用catch中回调函数。Promise中错误有冒泡性质，一直向后传递，直到被捕获为止。一般不要在then中定义Reject回调，总是使用catch方法

#### 3. finally()

不管最后状态如何，总是执行方法中的回调

#### 4. all()

接收数组为参数，数组中每个元素都是Promise实例。返回Promise实例。

``````javascript
const p = Promise.all([p1, p2, p3]);
``````

p1, p2, p3 全为fulfilled， p为fulfilled。有一个为rejected，p为rejected

成功返回一个结果数组，失败返回最先被reject失败的状态值

场景： 所有数据获取完后才加载界面

#### 5.race()

同样将多个Promise实例包装成一个新的Promise实例

```javascript
const p = Promise.race([p1, p2, p3]);
```

有一个实例率先改变状态，p的状态就会改变，不管结果本身成功还是失败。

使用场景：多台服务器部署同样的服务接口，使用race，哪个快就从哪个服务器接口拿

#### 6. resolve()

将现有对象转化为Promise对象，all和race方法中参数不为Promise实例时自动调用转换

* 参数是promise实例不做任何修改
* 参数为tenable对象（具有then方法对象），转为promise对象，立即执行then方法
* 不具有then方法 or 不是对象 返回新Promise对象，状态为resolved
* 不带参数，直接返回resolved的Promise对象



### Promise A+ 规范

https://promisesaplus.com/

* 3个状态： pending， fulfilled， rejected

* new Promise时，传递executor()，立即执行
* executor接收resolve/reject两个参数
* 默认状态为pending
* 有一个value保存成功状态的值
* reason保存失败状态的值
* 必须有then方法，两个参数，成功回调和失败回调onFulfilled, onRejected
* 调用then时，成功（执行resolve）则执行onFulfilled，参数为value
* then中抛出异常，作为参数传递给下一个then的失败回调onRejected



### Promise实现中的发布订阅模式

发布订阅模式中有3个组件：发布者，消息中心，订阅者

