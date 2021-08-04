## 	生命周期

![image-20210514112832862](/Users/brsmsg/Library/Application Support/typora-user-images/image-20210514112832862.png)

子组件更新不会影响父组件，父组件更新影响子组件。

Fiber树 =>   也就是虚拟DOM 树  

render阶段：

Reconciler=> 执行reconcile算法（diff算法）

### Reconcile机制：

1. props或state改变
2. render函数返回不同的DOM树
3. 新界DOM对比（diff算法）
4. 针对差异地方更新
5. 渲染为真实DOM



commit阶段：

Render=> 执行ReactDOM渲染器



首次渲染：

1. render阶段

   深度优先遍历Fiber树，调用相应的生命周期函数

   对于每个node：

   constructor => getDerivedStateFromProps => render => ComponentDidMount

   

2. 进入commit阶段，渲染
3. 从子节点开始执行生命周期函数 componentDidMount



产生交互：

1. 调用this.setState 会深度优先遍历创建Fiber树
2. 通过Reconcile算法发现改变的节点
3. 标记变化，调用改变的node的生命周期函数getDerivedStateFromProps=> shouldComponentUpdate => render
4. 继续构建Fiber树
5. 进入commit阶段 渲染
6. 执行变化中对应的试图操作，并执行对应生命周期函数：getSnapShotBeforeUpdate => componentDidUpdate

7. 新Fiber树替换前一棵Fiber树



新旧区别：getDerivedStateFromProps(nextProps, prevState) 替代willRecieveProps  getSnapShotBeforeUpdate 替换 componentWillUpdate

从props中获取state

#### Fiber

v15之前改动上册组件调用栈会很长，因为没有IO操作，渲染过程为同步

使用Fiber，将之前的同步渲染改为异步。 也就是Slice，切片。耗时任务分成小片，每片执行完后，允许其他紧急任务执行。Fiber就是维护分片的数据结构



正是因为更新过程可能被打断，因此分为两个阶段：也就是 Reconciliation 和 commit。 Commit阶段不可以被打断。



#### 通信

* 父子： 父组件传递Props给子组件。子组件通过调用父组件传过来的函数向父组件通信
* 兄弟：通过共同父组件



#### MVVM

!!! React不是MVVM框架，只借鉴MVVM思路

MVVM: model-viewModel-view

MVVM就是实现了数据绑定的MVP（双向绑定：View 变动反映在ViewModel，反之亦然）。React只是单项数据流，仅状态驱动视图。没有绑定



## V-DOM & Diff

一个规范化JS对象，之后再渲染成真实DOM节点。伪类尽可能少的操作DOM，在后续渲染上提升效率

### React Diff： 

1. 分层比较：仅比较同一层的节点
2. 不同两个节点会产生不同的树（只要节点不同，删除所有子树，创建新树）
3. 不使用ke（指定相同元素），会直接对列表元素进行增加删除操作。使用key了之后会对新集合遍历，通过key判断是否需要创建/删除

#### diff 流程

1. 元素类型不同，直接删除
2. 元素类型相同
   * 都是DOM节点(对比 className/ title)
   * 都是组件（实例不变，更新props）
3. 特殊(子元素列表)
   * 引入key， 通过key得知元素是否存在，换位置，就不需要跟新整个节点。只需要移动位置。



## 为什么使用Immutable.js

* PureComponent 和 memo只进行浅层比较（引用类型只比较引用地址），数组内数据发生变化无法得知
* memo只比较props

### immutable

一旦部分被修改，就会返回全新对象（用树实现）



### useMemo 和 useCallback

useMemo（）同样

父组件向子组件传递函数，如果不用useCallback，每次都会生成不以样的函数引用，造成子组件不必要的重新渲染。使用useCallback能在依赖不变的情况下保持一样的函数引用，避免重复渲染



#### setState同步还是异步

legacy/blocking/conurrent模式.创建方式不同

render/createBlockingRoot/createRoot



legacy中：异步。更新优先级相同。

原因：命中batchedUpdates 批处理时，会合并多次setState为一次，异步执行。如果没有命中batchedUpdates，就是同步更新



开启Concurrent模式：都是异步，更新都是不同的优先级



### Hooks的优点

#### 什么是hooks

* 用于在函数组件中引入状态管理和生命周期方法
* 取代render props实现抽象/可重用性

优点：

* 避免使用class组件，不需要写复杂的this
* 组件逻辑复杂，生命周期函数常常包含不相关逻辑。使用useEffect（）





### Redux

![img](https://user-gold-cdn.xitu.io/2019/6/18/16b68beda28f65bc?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

* 组件通过actionCreators创建action，通过store.dispatch()出去
* store存储state

* reducer通过传过来的action和state 计算出新的状态，返回到store。
* store就把新数据传给组件

所有组件从store中拿数据，不论嵌套多深，就可以避免传递

#### Redux-thunk

允许action返回函数，如果是函数，这个函数传参dispatch，然后就直接执行函数。可以进行简单异步操作



### PureComponent && memo

当子组件需要用到的props更新时，才更新子组件（重写shouldComponentUpdate声明周期hook）

memo用于FC，

PureComponent用于class



### 高阶组件

函数：接受组件作为参数返回一个新的经过改造的组件

例子：React,memo()

### 父子组件生命周期

render以及render前的生命周期，父组件先执行，render后的生命周期子组件先执行，和父组件交替执行