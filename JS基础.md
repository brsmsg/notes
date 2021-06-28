### 基础类型

原始：null undefined number string symbol（新增BigInt）

引用: object

两者区别：值类型放在栈中  保存在堆中

值用typeof  引用用instanceof

* instance of判断原理：一个实例的原型链中出现过左侧的构造函数，instance of返回true



== 类型转换：步骤

* 先判断类型是否相同，类型相同进行 === 比较
* 先比较null == undefined ？
* 之后比较 string == number ？string 转number（核心：这两个都是转number）
* 之后不管任何类型，有boolean有就boolean转number
* 之后是否有object 有就object转基本类型，先调用valueOf，还是非原始类型就调用toString

000表示对象

[] == ![] true	



![img](https://user-gold-cdn.xitu.io/2018/11/15/16716dec14421e47?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

#### null 和 undefined 的区别

null是对象原型链的重点Object.prototype.__proto__ = null

Number(null) = 0	Number(undefined) = undefined

一切没有赋值或者声明的，都是undefined

#### 事件流

标准事件中：先捕获后冒泡

事件捕获 -> 处于目标阶段 -> 事件冒泡

W3C标准统一： DOM分为两个阶段，捕获和冒泡

先捕获，window接受事件，向下捕获，由具体元素接受，之后向上冒泡，返回到window

阻止冒泡：event.stopPropagation()

#### JS位置

clientHeight： 可视区域（不包括border和滚动）

offsetHeight： 可视区域(包括border和滚动)

scrollHeight： 所有区域高度(包括scroll隐藏部分)

clientTop：border厚度

### 拖拽

mousedown + mousemove+ mouseup实现

Drag/Drop实现

### setTImeout实现setInterval

const f = () =>{

​	setTimeout(f, 1000);

};

setTimeout(f, 1000);

### 执行上下文（Execution Context）

全局执行上下文，不在函数内部的代码都在全局上下文中

函数执行上下文，函数被__调用__时创建新的上下文

每个上下文一个关联的变量对象，该上下文中定义的



#### 执行栈

首先全局执行上下文入栈，每遇到一个函数调用就创建新上下文，Engine执行上下文位于栈顶的函数。执行完后上下文栈弹出顶层上下文





### 数组去重

1. 使用新数组(使用includes) 2.使用Set  3. 使用filter（不改变原数组)

改变原数组的方法：pop(),push(),reverse(),shift(), sort(),splice(start, count, newEle)

forEach()filter(),reduce等跳过空位，map()会跳过但是保留该值





# 变量

var 重复声明，作用域：全局 or 函数

let & const 拥有块级作用域

let & const暂时性死区： 在一个block中使用let/const重新定义了变量，就不能在之前引用他。会报错，但是使用var只会输出undefined

for中使用let，let在for中行成块级作用域，每次迭代时i是新的值，且存在于循环块级作用域内。

### 变量提升 & 函数提升

var的变量提取到上面进行声明。

！！ 函数是一等公民，会优先解析函数

函数声明会进行提升！！只有函数声明会提升 function fn(){}。  表达式等不会提升！

注意 new 带参数的化优先级高于函数调用。即：(new foo()).getName()

new Foo.getName() 则等于  先执行getname，之后 new Foo



### 作用域链

变量的查找范围，引用变量，如果没有就向父级作用域找。如果找不到就undefined。

脚本解析阶段就规定好，与执行阶段无关

#### 执行上下文

* 全执行上下文局  和   函数执行上下文

运行时确定，随时改变。放在调用栈中。最先压入全局context，

调用函数时，就会用新的上下文入栈。

* 创建阶段：创建作用域链，变量对象，决定this
* 执行阶段： 变量赋值，函数引用

### 垃圾回收

周期运行

mark & sweep 变量进入上下文时，加上存在于上下文标记，离开上下文时加上离开上下文标记。对所有被引用的变量进行标记，去掉没有被标记的变量。

优化：1（分代回收） -> V8引擎 活的久的对象进入老生代，较少次数被检查







### 闭包 高程P179

一句话解释： 能访问另一个函数作用域中的变量的函数

JS中，词法作用域规则：内部函数可以访问外部函数中的变量

调用一个外部函数，返回一个内部函数，即使外部函数执行完，作用域不会销毁！原因：匿名函数context入栈，最后退栈之后，栈顶还是外部函数的context，匿名函数作用域链还是在引用活动对象。

##### 内部函数对外部函数中变量的引用仍然保存在内存中。

* 好处：缓存，不被GC & 实现柯里化， 函数防抖节流
* 坏处：内存消耗 & 性能问题

匿名函数中的this指向window



### typeof 和 instance of 判断变量

基本类型除了null 都正常返回，函数是Function 其他类型均返回object 另外typeof null -> object

原因：早期版本32位系统，性能考虑使用低位存储变量类型，000开头代表对象，然后null是全零

##### instance of 判断原型链指向，顺着原型链查找

通过constructor也可以，但是constructor可以被修改

##### Object.prototype.toString.call()完美判断



# 原型链

* 不论何时，只要创建函数，就会生成原型
* hasOwnProperty判断属性来自实例还是原型
* in操作符表示可以通过对象访问指定属性

prototype是什么：一个指针，指向一个对象，创建新函数时自动创建prototype属性。该对象constructor指向该函数

__proto__是什么： 实例对象的属性，指向构造函数的prototype原型对象

原型链： __如果一个原型又是另一个类型的实例__，之前所说的有关原型的关系依然成立，层层递进，就构成了实例和原型的链条，也就是原型链

eg: person.proto -> Person.prototype  & Person.prototype.proto-> Object.prototype

& person.proto.proto -> Object.prototype

属性查找：实例对象.a -> 构造函数.prototype.a -> Object.prototype.a



### 深浅拷贝

* 浅拷贝：
  * Object.assign(),属性为对象时，拷贝的是地址
  * ES6展开运算符{...}
* 深拷贝：
  * JSON.parse(JSON.stringify())  问题：忽略函数/忽略undefined/忽略symbol/忽略循环引用
  * lodash
  * 手动实现：先判断类型Object.prototype.toString.call()。 之后进行遍历，如果是object/array，就递归调用deep clone。



### 继承

##### 组合继承

子类constructor中调用父类constructor， 调用Parent.call(this)  这一步用来继承父类的属性

之后子类的prototype指向一个Parent实例  Child.prototype = new Parent()  这一步用来继承父类的方法 

注意，这里Child.prototype.__proto__ === Parent.prototype

* 优点: 可以传参， 引用属性不会共享，父类方法可以复用
* 缺点：两次调用构造函数，又一次生成了对原来属性进行了覆盖，造成内存浪费



##### 原型继承

Object.create() 两个参数。新对象原型，新对象定义额外属性的对象。 第二个参数指定的属性会覆盖原想对象上的同名属性。

let a = Object.create(b) 即相当于a.--proto-- => b 

本质： 对传入的作为原型对象的对象进行了一次浅复制，会共享引用类型。

不能传参

#####  寄生继承

封装继承过程函数（可以是任何形式继承，不一定要用原型继承），在函数内部增强对象

##### 寄生组合继承（pararistic combining inheritance ）

组合继承中将子类prototype指向了new出来的Parent实例，寄生组合继承中使用Child.prototype = Object.create(Parent.prototype， {constructor: {}})

* 核心：相比组合继承，继承方法的方式改变
* 相比于组合继承的优点：只调用了一次父类构造函数



### 事件代理（委托）

* IE : 事件冒泡,最底层向上传播
* 事件捕获，由根元素按树向下查找

DOM0级处理：btn.onclick = () => {}

DOM2级处理：addEventListener("click", ()=>{})

事件对象：

* currentTarget：Element 事件处理程序正在处理的对象

* target：事件的目标。

* 区别 this始终指向currentTarget。target指向真正目标，如果真正目标没有事件处理程序，会向上冒泡。因此定义在父节点上的处理程序中的target会指向最底层。

什么是事件委托

* 利用事件冒泡，指定一个事件处理程序来管理一类型所有事件。所有的事件都会冒泡到父节点上的事件处理程序

比如对列表子节点，只需要使用父级委托操作父级节点即可。然后使用target来定位元素



### hash路由和history路由

hash是指url尾巴后的#以及后面的字符。#后hash值变化不会导致浏览器发出请求。使用hash实现前端路由，页面定位。 window.location.hash

history模式会向服务器发请求

window.history.back()后退 = history.go(-1)

window.hisory.forward()

history.go(0) 刷新



H5新增api window.history.pushState()，修改url也不刷新页面





### script标签的async和defer

* async

  说明引入的js需要异步加载& 执行。异步加载执行不会阻塞界面加载，但是不保证加载顺序。最好不要使用依赖

* defer

  异步执行，文档解析完后执行，会按原顺序执行。

* 推荐直接把script放到body底部

为什么执行JS阻塞HTML解析。对于浏览器的渲染进程而言：JS解析线程和渲染线程互斥。防止渲染结果不可预期。



#### 数组

判断是不是数组： 

Array.isArray()  / Object.prototype.toString.call()  /  instance of Array

valueOf 返回本身， toString 返回字符串形式，带逗号

* 改变原数组的方法：  

  pop() push()  shift()   unshift() 

  splice() 第一个参数删除的起始位置，第二个参数删除的个数。返回删除的元素组成的数组

* map和forEach， filter， reduce区别

  map返回新数组，forEach不返回值。

  map，forEach都会跳过空位

  filter返回一个，满足条件的元素组成的新数组

  reduce 第一个参数函数。函数四个参数（累计变量，当前变量，当前位置，原数组）记住前两个



#### ajax & fetch

##### Ajax

asynchronous javascript and XML。 页面不刷新发起请求数据

window.XMLHttpRequest() 网络请求对象

.open(method ,url)  

.send(body)

readyState5个状态

0. 未初始化，没有调用open方法
1. 建立链接，open已调用
2. 接受请求，send已调用，头部/状态已获得
3. 正在处理请求
4. 完成



fetch 替代XHR，全局使用比XHR更方便.是原生JS， 基于promise

method, header s, body



#### new的过程

1. 创建一个空对象
2. 空对象proto指向构造函数prototype
3. 绑定this，也就是构造函数中的this
4. 执行constructor



#### 防抖节流！！！

* 防抖： 函数频繁触发时，规定时间内，只让最后一次生效

  场景：搜索框（不需要不停调用请求），按钮点击，多次请求

* 节流： 函数多次触发，执行一次后时间大于设定周期才会执行第二次

  场景：搜索框的模糊查询功能，拖拽（mouseMove）每隔一段时间获取一次定位，射击游戏（点击鼠标），播放事件算进度。







## proxy & Reflect

基本使用：

```javascript
const proxy = new Proxy(obj, handler);
```

定义捕获器trap

```javascript
const handler = {
	// 目标对象，要代理的属性，代理对象
	get(target, property, receiver){}
	
}
```



Reflect，封装原始行为：

```javascript
const handler = {
	get(){
		const decoration = "!!!"
		return Reflect.get(...arguments) + decoration
	}
}
```



### 尾递归优化

外部函数的返回值是内部函数的一个返回值

```javascript
function outerfunction{
	return innerfunciton()
}
```

注意最后一步必须是调用函数!

```js
function f(x){
	g(x)
}

function f(x){
	return g(x) + 1
}
```

都不行！

#### 调用栈和调用帧

每运行一个函数，将该函数放进调用栈中，每个函数就对应一个调用帧

用图解释

```js
function foo () { console.log(111); }
function bar () { foo(); }
function baz () { bar(); }
baz();
```

调用栈如下

![162b410edd7877e9.jpg](https://segmentfault.com/img/bVby2fT)

使用尾调用优化：

```js
function foo () { console.log(111); }
function bar () { return foo(); }
function baz () { return bar(); }
baz();
```

![162b410edd6f2c82.jpg](https://segmentfault.com/img/bVby2d3)

核心： 尾调用是函数最后一步操作，不需要保留外层函数调用记录，直接出栈，栈顶保留内层函数调用帧。节省很大一部分内存。

#### 应用：尾递归

递归耗费内存，保留大量调用帧，使用尾递归形式，只存在一个调用帧，不会发生stack overflow错误。	
