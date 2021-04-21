# JSX:

对JS语法扩展，使用JSX来替代常规JS，使用JS 来描述视图

* 更快（不用编写createElement）
* 类型安全（编译器会进行类型检测）
* 开发效率高

原理：

* 被编译为React.createElement() => 返回一个React Element
* 核心：描述当前组件内容的数据结构。
* mount时，Reconciler通过JSX描述的组件内容生成对应Fiber节点

ReactDOM.render(JSX, dom)

JSX=>虚拟DOM，object

virtual dom： 

```javascript
{

​	type: "div",

​	props: [

​		id: ""	

​		children: ""

​	]

}
```



# setState

每次执行添加异步队列





# diff

* 真实DOM操作缓慢
* 使用vdom(React.createElement())比较 => 减少重绘重排

### Diff核心概念

执行Diff算法时，一个DOM节点有四个值跟他相关

1. current FIber
2. workInProgress Fiber
3. DOM本身
4. JSX对象

本质：对比 1 & 4，生成2



### diff策略 O(n)

* 同级比较
* 判断类型 (type （div/p）) -> 判断属性 (props)
* 同级节点，使用key区分，直接移动，减少删除重建

删除

更新 （）

替换（对比key）



#### 同级比较

两种情况： 单节点diff & 多节点diff



##### 多节点DIff

* 两轮遍历：因为更新节点更容易出现。第一轮处理更新节点第二轮遍历处理	剩下的不属于更新的节点
* 第一轮遍历：key不同导致不可复用，跳出遍历进入第二轮 && newChildren 或者 oldChildren遍历完 跳出遍历，进入第二轮遍历

* 第二轮：
  * old/new 同时遍历完
  * new 没遍历完， old 遍历完，表示有新的节点
  * new遍历完，old没遍历完
  * 都没遍历完：说明节点位置发生了变化 diff精髓部分

调换位置处理key！

old/new均未遍历完的情况下，将剩余oldFiber存入以key为key，oldFIber为value的map中。

之后遍历剩余new Children，就可以找到相应key对应的old Fiber。

节点移动参照：最后一个可复用节点在oldFiber中的位置索引。本次更新中节点按照newChildren

## 回调Ref

组件挂载时会调用ref回调函数并传入DOM元素，当卸载时调用它并传入null

转发Ref：转发Ref给子组件，通过forwardRef，父组件中创建的Ref来获取子组件中的DOM节点



## Scheduler-reconciler-Renderer架构

* Reconciler工作阶段就是render阶段。该阶段调用组件render方法。

* Renderer工作阶段为commit阶段。commit阶段把render阶段提交的信息渲染在页面上
* render&commit 统称为work。即React在工作中，任务如果在Scheduler里调度，就不属于work





## Fiber架构

更新DOM原理

"双缓存" 两颗Fiber树：

当前浏览器显示的current Fiber

内存中构建的 workInProgress Fiber

* 每次状态更新会产生新的workInProgress Fiber。通过替换wIP 和 current Fiber完成DOM替换
* wIP FIber树构建完后renderer渲染在页面上候，根节点current指针指向wip FIber树，WIP变为current

