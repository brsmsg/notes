### 模块化

Common JS： node中使用

ES Module和Common JS区别：

1. C 支持动态导入
2. C时同步导入，ES Module是异部导入，因为用于Browser需要下载文件，同步会对渲染造成影响



### Map filter reduce

1. map：生成新数组，遍历原数组，每个元素做变换之后放到新数组

2. filter：生成新数组，遍历时将函数返回值为true元素放入新数组

3. reduce：两个参数 回调+初始值 

   reduce实现map:   arr.reduce((acc, cur) => {acc.push(对cur的map操作)}, [])



### Set & WeakSet Map & WeakMap

api  add()  size delete()  has()  clear()

WeakSet 只能放置对象，且都是弱引用。GC时不考虑WeakSet对该对象引用，其他对象不引用该对象，GC会自动回收内存。 

GC的引用计数！引用为0才GC。适合临时存放



Map api  set(k, v)   get(k)  has() 

WeakMap 只接受对象作为键名。 没有遍历操作



#### 尾递归

##### 尾调用

函数a的最后一个动作是调用函数b

return b()

提前出栈，减少调用栈大小

##### 尾递归

相同原理 



#### GC

##### mark & sweep 标记清除

变量进入环境（比如函数中声明一个变量），就进行标记(进入环境)，离开环境时标记(离开环境)

GC 工具给所有存储在内存中的变量加上标记。去掉环境中的变量，以及被引用的变量标记，剩余的都要进行GC 

##### reference counting 引用计数

循环引用: 

对象1中某个属性指向对象2，对象2的属性指向对象1 