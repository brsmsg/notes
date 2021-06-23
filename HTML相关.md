#### 语义化

* 已修改维护/无障碍阅读支持
* 利于SEO
* header>  main>  footer> 



### 块级和行内

块级：独占一行，默认填充父元素宽度.可以设置width/height, 可以设置margin/padding

行内：宽度随内容变化,无法设置width/height,边距效果只有margin-left(right)/padding-left(right)

块级: div   h    li    ul p 

行内: a  i img input



#### DOCTYPE

文档类型

document type

* 标准模式: 根据W3C标准渲染
* 混杂模式: 向后兼容,就是用来兼容旧网页

!DOCTYPE html表示浏览器进入标准模式



### Meta

metaData不会显示在页面上，但是机器可以识别。

name: viewPort。使用响应式 media







## Canvas

Canvas绘图模糊问题

**devicePixelRatio**属性

返回物理像素分辨率和CSS像素分辨率。Retina屏下是2

即像素点变多，一个px会对应两个像素

**解决方案**

canvas宽高和css宽高：

将canvas宽高（canvas的width和height属性），即画布大小*2来转换成相应物理像素，之后设置canvas元素宽高（style的width和height）

此时画布内容缩小，使用scale()，不会改变内容素质。

