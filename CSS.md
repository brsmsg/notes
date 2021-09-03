### 选择器

* 标签
* id
* 类
* 通配

层次： elemP  elemC 后代   elemP > elemC  子代   elem1+ elem2 相邻同胞

伪元素(实体)： ::before/ ::after 伪类(状态)

优先级：多个选择器同时指向一个元素，优先级高的选择器会作用在上面。

内联(1000) > ID(#)100 > Class/属性/伪类 100(.title input[type="text"]  :hover) > 标签 伪元素(div ::before)

###### 直观权重：

10000：！important

1000：内联/外联

100：ID

10：class/伪类/属性

1：标签/伪元素

0：通配，后代，兄弟

##### 微观权重

1，0，0，0，0	！important

0，1，0，0，0  内联

优先级相同，使用最后出现的规则





####  继承

可继承的样式：font-size/ font-family/ color/ ul 等

不可继承：border， padding， margin等



#### 伪元素伪类

name: viewPort。使用响应式 media



#### position 属性

默认：static 忽略z-index

inherit：继承父元素position

relative: 相对原本位置

absolute：相对最近已经定位祖先元素（不为static）注意 相对border内边缘

fixed：相对viewport

sticky：不超过阈值时表现为relative，超过阈值表现为fixed

fix时相对偏移量为最近有滚动框的元素



#### margin padding 百分比

相对父元素width计算



### REM

em：font-size时 1em表示父元素的字体大小。其他表示自身字体大小

rem: 根元素的font-size

适配方案：设置html.style.fontSize ，跟clientWidth相关



### flex

* justifycontent: start/center/flex-end   space-between  space around
* align items;
* flex-grow: 剩余空间，子元素占剩余空间占比。设置为1 自动占满。默认为0，不占满
* flex-shrink：超过容器值后，如何收缩数值越大。默认为1：自动收缩
* flex-basis: 设置主轴方向尺寸（分配空间之前）根据他来计算剩余空间  默认 auto（和宽度同宽）
* flex: 默认g 0 1 auto   取用户定义的宽高
* flex：1  grow-1 shrink-1 basis-0  自动缩放，分配为0



## 动画

分类： JS控制（setTimeout/requestAnimationFrame）

CSS控制

Canvas/SVG

* setTimeout问题：主线程代码需要执行时间较长或者事件队列里有其他排在定时器前的事件，超过定时器间隔，导致帧率不够
* raf把动画绘制任务交给浏览器，浏览器空闲时绘制一帧动画，不会有等待/多余的帧，更平滑

CSS3:

* 无法使用逐帧，仅使用关键帧
* 执行和js主线程无关



## 开启BFC

* float值不为none

* overflow：hidden（不为visible）

* position不为relative和static

* display为table-cell/inline-block

IFC:

inline-format-context

无法指定宽高，垂直方向上的margin/padding不起作用
