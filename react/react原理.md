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

####  JSX && Fiber

JSX不包含以下信息；

* 更新优先级
* 组件state
* 用于renderer的标记





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
4. JSX对象to

本质：对比 1 & 4，生成2



### diff策略 O(n)

* 同级比较
* 判断类型 (type （div/p）) -> 判断属性 (props)
* 同级节点，使用key区分，直接移动，减少删除重建

删除

更新 （）

替换（对比key）



#### 同级比较

两种情况： 单节点diff & 多节点diff。这个节点是指更新后（newChild）的单/多节点，

单节点：

* 判断复用：先比较key，key相同再比较type，都相同时一个DOM才能复用

##### 多节点DIff

* 两轮遍历：**因为更新节点更容易出现**。第一轮处理更新节点，第二轮遍历处理剩下的不属于更新的节点

* 第一轮遍历：key不同导致不可复用，跳出遍历进入第二轮 && newChildren 或者 oldChildren遍历完 跳出遍历，进入第二轮遍历.

  key相同 type 不同导致不可复用，oldFiber标记为deletion	

* 第二轮：
  * old/new 同时遍历完：理想情况
  
  * new 没遍历完， old 遍历完，表示有新的节点，继续遍历剩下的new，并给生成的WIP fiber标记为placement
  
  * new遍历完，old没遍历完，遍历剩下的old，标记为deletion
  
  * #### 都没遍历完：说明节点位置发生了变化！ **diff最精髓的部分（ **map实现）

调换位置处理key！

old/new均未遍历完的情况下，将剩余oldFiber存入以key为key，oldFIber为value的map中。

```
new Map<key, oldFiber>()
```

之后遍历剩余new Children，就可以找到相应key对应的old Fiber。

节点移动参照：最后一个可复用节点在oldFiber中的位置索引(lastPlaceIndex)。本次更新中节点按照newChildren

## 回调Ref

组件挂载时会调用ref回调函数并传入DOM元素，当卸载时调用它并传入null

转发Ref：转发Ref给子组件，通过forwardRef，父组件中创建的Ref来获取子组件中的DOM节点



## Scheduler-reconciler-Renderer架构

* Scheduler负责调度任务优先级，高优先级任务进入Reconciler

* Reconciler工作阶段就是render阶段。该阶段调用组件render方法。 （Reconciler重构目的：将老的同步更新改为**异步可中断**更新）

* Renderer工作阶段为commit阶段。commit阶段把render阶段提交的信息渲染在页面上
* render&commit 统称为work。即React在工作中，任务如果在Scheduler里调度，就不属于work





## Fiber架构(unit of work)

fiber树结构：

![Fiber架构](https://react.iamkasong.com/img/fiber.png)

更新DOM原理

"双缓存" 两颗Fiber树：（类似canvas，使用offscreen canvas）

当前浏览器显示的current Fiber

内存中构建的 workInProgress Fiber

两棵树使用alternate连接

* 每次状态更新会产生新的workInProgress Fiber。通过替换wIP 和 current Fiber完成DOM替换

* WIP FIber树构建完后renderer渲染在页面上候，根节点current指针指向wip FIber树，WIP变为current

* fiberRootNode节点，current属性指向curFiber：

* mount阶段时：图中构建完左侧的WIP Fiber将在commit阶段被渲染到页面

  ![workInProgressFiber](https://react.iamkasong.com/img/workInProgressFiber.png)

#### 影响生命周期

```
componentWillMount
componentWillUpdate
componentWillReceiveProps
```

render阶段允许暂停，终止，重启，导致render阶段生命周期可能重复执行，故废弃

## render阶段(执行Reconciler)

**改为使用遍历方式实现可中断递归！**

开始于`performSyncWorkOnRoot`或`performConcurrentWorkOnRoot`方法的调用，这两个方法会调用

```js
// performSyncWorkOnRoot会调用该方法
function workLoopSync() {
  while (workInProgress !== null) {
    performUnitOfWork(workInProgress);
  }
}

// performConcurrentWorkOnRoot会调用该方法
function workLoopConcurrent() {
  while (workInProgress !== null && !shouldYield()) {
    performUnitOfWork(workInProgress);
  }
}
```

**shouldYield(): 浏览器有没有剩余时间，如果没有，就终止，有空闲时间再继续遍历**

WIP表示正在处理的WIP fiber节点，是一个全局变量

```js
// The fiber we're working on
let workInProgress: Fiber | null = null;
```

**performUnitOfwork()**会创建下一个Fiber节点，并赋值给WIP，并将WIP和已创建的Fiber节点连接。 即触发**beginWork()**调用

分为递&归两段

* 递：（创建Fiber节点，构成树）从root fiber向下遍历，为每个节点调用beginWork()方法。根据传入Fiber节点创建子Fiber节点并连接。遍历到叶子结点进入归阶段

* 归：（处理Fiber节点：添加DOM，处理props）归阶段调用completeWork函数，执行完后如果存在sibling节点执行sibling Fiber的completeWork()，不存在sibling就执行父节点。直到执行到root fiber，整个render结束

### beginWork()

```javascript
function beginWork(
  current: Fiber | null,
  workInProgress: Fiber,
  renderLanes: Lanes,
): Fiber | null {}
```

参数说明：

current：当前fiber节点对应上次更新的fiber => 通过current是否为null来判断是update还是mount

WIP：即当前fiber

renderLanes：优先级相关

通过fiber.tag调用不同处理逻辑。

* current !== null时，即update时，判断是否可复用。如果可以复用就检查更新，克隆currentChild为WIP Child。
* 不可复用或者mount阶段时，会执行reconcileChildren方法

#### reconcileChildren()

* Mount阶段创建新的子fiber节点
* update阶段执行diff算法，将比较结果生成新fiber节点

最终生成新的子fiber节点

使用fiber.effectTag属性来标记Render需要执行的DOM操作

要想通知renderer将fiber插入页面，需要两个条件：

1. fiber.stateNode存在，Fiber中保存DOM节点
2. fiber存在placement effectTag

对于第二个条件：如果mount阶段所有fiber树节点都有effectTag，commit阶段对所有DOM节点进行插入，效率低。解决办法：mount阶段只对rootFiber生成effectTag，只进行一次插入操作。

<img src="https://react.iamkasong.com/img/beginWork.png" alt="beginWork流程图" style="zoom: 200%;" />

beginwork（）的工作：

* 核心：根据传入的Fiber节点，创建Fiber子节点，并连接两个Fiber，调用到叶子组件时进入归阶段

* 通过参数currentFiber判断组件是update还是mount
  * 如果是update：判断是否可以复用
    * 不可以复用，则根据WIP.tag区别对待，执行reconcileChildren(),diff算法生成带effect tag的新Fiber，复制给WIP.child，作为beginwork返回值
      * 可以复用，检查子树是否需要更新，如果不需要就不进行操作
      * 如果子树需要更新，
  * 如果是mount，执行mountChildFiebr生成新子Fiber节点

### completeWork()

通过fiber.tag调用不同处理逻辑。completeWork中主要关注HostComponent：原生DOM节点对应的Fiber。

和beginWork一样，判断mount/update

* update时：fiber节点已经存在对应DOM，不需要重新生成。主要处理props

* Mount时：

  1. 为fiber生成对应DOM节点->fiber.dom = x x x并没有挂在道页面
  2. 子孙节点插入刚生成DOM节点中
  3. 处理props

  ```javascript
  // 为fiber创建对应DOM节点
  const instance = createInstance(
      type,
      newProps,
      rootContainerInstance,
      currentHostContext,
      workInProgress,
    );
  // 将子孙DOM节点插入刚生成的DOM节点中
  appendAllChildren(instance, workInProgress, false, false);
  ```

  当递归到rootFiber时，我们已经有了一个完整的offscreen fiber树

#### effectlist

commit阶段需要找到所有存在effectTag的fiber节点并以此执行操作，显然commit阶段不可能再遍历一遍fiber树来找到所有节点。

因此使用一条单链表effectlist来保存所有存在effectList的fiber节点。

这样在**commit阶段**只需要遍历effectList即可。

![completeWork流程图](https://react.iamkasong.com/img/completeWork.png)



最后render阶段任务全部完成后调用commitRoot(root)进入commit阶段

completeWork()的工作：



* 注意顺序！执行完completeWork后，如果存在sibling节点，会进入sibling节点“递”阶段。不存在sibling就进入父节点的“归阶段”

## Commit 阶段

commitRoot(root)为入口，进入commit阶段

生命周期hook：componentDidxxx， useEffect都是在commit阶段执行

三个主要工作：

* before mutation阶段（执行DOM前）
* mutation阶段（执行DOM操作）
* layout阶段（执行DOM操作之后）

### beforeMutation（执行DOM操作前）

遍历EffectList，对每个Fiber执行：

**主函数commitBeforeMutationEffects**

1. 处理DOM渲染/删除后的autoFocus blur逻辑
2. 调用getSnapshotBeforeUpdate生命周期钩子
3. 调度useEffect（而非调用）

#### 调用getSnapshotBeforeUpdate()

* **componentWillxxx的问题**：更新fiber后，render阶段任务肯能会被中断/重新开始，因此componentWillxxx会被触发多次
* 改为getSnapshotBeforeUpdate，在commit阶段调用。commit阶段同步，不会存在被打断问题。

#### 调度useEffect

异步调度：调用scheduler模块的scheduleCallback

执行方法 flushPassiveEffects()



### mutation阶段（执行DOM操作）

同样，遍历EffectList，执行主函数

**主函数commitMutationEffects**

1. 根据contentReset effectTag重制文字节点
2. 更新ref
3. 根据effectTag分别处理  其中`effectTag`包括(`Placement` | `Update` | `Deletion` | `Hydrating`)
4. 执行componentWillUnmount

#### placement effect

该Fiber对应的DOM应该被插入到页面中

1. 获取父级DOM节点`getHostParentFiber(finishedWork);`
2. 获取DOM兄弟节点 ```getHostSibling(finishedWork);```
3. 根据兄弟节点是否存在决定调用insert Before还是appendChild

* 注意getHostSibling（获取兄弟节点的DOM）操作很费时。因为Fiber节点不只包括HostComponent（即真实DOM对应的FIber），因此可能会存在跨层级遍历



#### update effect

该Fiber对应DOM执行更新操作

functional Component： 执行所有useEffectLayout的销毁函数

host component：渲染页面



#### deletion effect

该Fiber对应DOM应该删除

1. 页面移除Fiber对应的DOM节点
2. 解绑ref
3. 调度useEffect销毁函数（而非调用）

#### mutation阶段结束后，layout阶段开始前，执行

```js
root.current = finishedWork;
```

### layout

遍历effectlist，执行函数	

* 调用componentDidUpdate/componentDidMount
* 对于classComponent，调用setState回调。
  * 对于functional Component，调用useEffectLayout回调

##### useEffect 和 useLayoutEffect

useLayoutEffect在layout阶段同步执行

useEffect需要先调度在layout阶段后异步执行（防止同步阻塞页面渲染）



## 状态更新

#### 流程：触发状态更新 ====> 创建Update对象====> 从fiber到root ====> 调度更新 ====> render ====> commit

##### 创建Update对象,保存更新状态相关内容，beginWork中根据update计算新state

以下方法可以触发状态更新：

ReactDOM.render()

this.setState()

this.forceUpdate()

useState()

useReducer()

##### 触发状态更新的fiber 通过 markUpdateLaneFromFiberToRoot 方法向上遍历到root并返回rootFiber



### UPdate结构

classComponent和HostComponent结构相同：

``````javascript
const update: Update<*> = {
  eventTime,  //未来重构
  lane,   //优先级相关
  suspenseConfig,  //suspense相关
  tag: UpdateState, //更新类型，包括UpdateState | ReplaceState | ForceUpdate | CaptureUpdate
  payload: null,  //更新挂载的数据: classComponent中payload为setState第一个传参，Host中为render第一个传参
  callback: null,  //更新回调 setState中回调

  next: null,  //链接其他update形成链表
};
``````

fiber.updateQueue上保存update组成的链表



## 优先级

根据HCI交互研究结果：

* 生命周期方法：同步执行
* 受控用户输入：input之类的同步执行
* 交互事件：比如动画，高优先级执行
* 其他：比如数据请求，低优先级

![优先级如何决定更新的顺序](https://react.iamkasong.com/img/update-process.png)



## Hooks

hooks工作分为两部分

1. 通过途径产生更新，更新后造成组件render
2. 组件render时useState返回更新后的结果

update的数据结构：

```js
const update = {
  // 更新执行的函数
  action,
  // 与同一个Hook的其他更新形成链表
  next: null
}
```

形成环状单项链表，queue指向该链表头节点。对于每个useState，每调用一个setState就会往该queque环形链表中插入一个update

* queue放在fiber中：**fiber.memoizedState.queue**

  ```js
  // App组件对应的fiber对象
  const fiber = {
    // 保存该FunctionComponent对应的Hooks链表
    memoizedState: null,
    // 指向App函数
    stateNode: App
  };
  ```

hook数据结构：（每一个useState对应一个hook数据结构）

```js
hook = {
  // 保存update的queue，即上文介绍的queue
  queue: {
    pending: null
  },
  // 保存hook对应的state
  memoizedState: initialState,
  // 与下一个Hook连接形成单向无环链表
  next: null
}
```

