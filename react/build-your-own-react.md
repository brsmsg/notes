## createElement()

函数作用：将jsx转化为javascript对象





### workLoop()

开始循环



### createFiber （performUnitWork）

renderh函数中创建root fiber，并设置nextUnitOfwork

performNode函数中对每个fiber做三件事：

1. element添加到DOM

2. 对element的children创建fiber

3. 选择下一个fiber（有children就遍历children，没有就遍历sibling，如果都没有，去找parent的sibling），之后归操作

4. 树结构：

   <img src="/Users/brsmsg/Library/Application Support/typora-user-images/image-20210724200825606.png" alt="image-20210724200825606" style="zoom: 33%;" />



## Reconcile

! 重点！

1. 比较type <h1> <div> 也就是html tag。相同直接更新props
2. 不同且有一个新的element，创建DOM
3. 不同且有一个旧的element，移除旧的element

