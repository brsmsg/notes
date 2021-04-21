### 全屏布局

顶部底部高度固定，主体高度自适应

### 两列布局

一列宽度固定，另一列自适应。核心：最右侧触发BFC，BFC不会和浮动区域重叠！

或者flex

### 三列布局

连续两列宽度固定，剩余一列宽度自适应

#### 圣杯布局&双飞翼布局

区别:解决div内容不被遮挡的思路不同

* 不同
  * 双飞翼: 中间列要插入一个子节点,子节点使用margin-left/right来为左右栏div流出位置
  * 圣杯: 父节点padding为左右留出空位

全部float left来做

浮动节点在位置上不能高于平级非浮动节点,否则浮动节点下沉

从上往下解析，线解析了mid，之后right的float不会对之前的元素造成影响

#### 居中布局

* 水平居中  
  * 块级: margin: 0 auto  + width (块级)
  * 行级元素: 父节点加上text-align: center
  * position + left/right + transform: translateX(-50%)
  * display: flex + justify-content

* 垂直居中
  * position + top/bottom + margin + height
  * 行内元素: 父节点加上line-height
  * position + top/ + transform
  * flex + align-items

position left: 0 right: 0 top:0 bottom: 0 margin: auto实现居中

margin： auto 分配剩余空间，水平方向上有剩余，垂直上没有！

原理: 普通流中margin auto 表示margin-top: 0 margin bottom 0

position absolute 绝对定位块跳出文档流，top/bottom 0 为浏览器重新分配边界框，填充父元素所有可用空间，margin垂直方向上有了可分配空间