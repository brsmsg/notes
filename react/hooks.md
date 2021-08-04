### 为什么不能在循环、判断内部使用hooks

hooks实现中初始状态保存在全局变量中。多个状态使用一个全局数组。（模拟实现）

本质实质上为链表

过程如下：

* 第一次渲染时，根据useState顺序，逐个声明state并放入全局Array中。每次声明state就要将cursor + 1
* 更新state时，触发再次渲染，cursor重置为0。根据useState声明顺序，一次拿出最新的state值，视图更新

如果在if中使用hooks，如果第一次渲染进入判断，之后不进入判断。那么重新渲染的时候cursor会指向不同的state变量，导致setState方法不起作用



核心：函数组件使用状态

* mount
* update

**hooks以链表形式存在**: 根据调用顺序被链接。

update时通过依次遍历定位hooks，也就是类似之前说的数组

![image-20210323180920278](/Users/brsmsg/Library/Application Support/typora-user-images/image-20210323180920278.png)

不能在if/else中写

useState：

mount阶段： 

* 创建hooks节点，加入hooks连标
* 初始值拿到，挂载hooks上
* 返回[state, setState]



* 调用setState

mount阶段，使用循环链表，利用尾插法加入新的update。queue指向该链表（最新的update）

![image-20210323171318647](/Users/brsmsg/Library/Application Support/typora-user-images/image-20210323171318647.png)



update阶段，把循环链表遍历。循环执行即可



* useEffect

避免多余渲染

生成effect节点，挂载updateQueue上

render阶段执行操作，commit阶段处理存储的effect信息。



update 阶段，和useState类似，会对依赖项比较

先执行unmount，（上一个effect的return函数）

指针queue同样指向最新节点



useEffect会形成闭包，在里面写的setInterval会一直存在，必须return onMount。

或者使用useRef， ref.current

* useRef

  内部调用Object.seal() 不能扩展新属性/删除现有属性。但可以修改属性的值

mount阶段

* useMemo & useCallback

依赖项不变，不进行计算

父子组件传值时 function会返回不同的引用

！ 不一定会达到优化的效果

* 会产生空间消耗
* 如果依赖项频繁变化，使用意义不大

