![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/98c1f92c802545a3a92d9600e2779de1~tplv-k3u1fbpfcp-watermark.image)



## Webpack

### 打包机制 

#### webpack_require

1. 从入口文件开始，分析依赖树

2. 将每个依赖包装，放入数组中等待调用
3. 实现模块加载的方法，并放到模块执行环境中，确保相互可以调用
4. 把执行入口文件逻辑放在函数表达式中，并执行







## loader

（翻译）处理非JS

常用：

css-loader-解析css文件

style-loader-加载样式

Source-map-loader 配置sourcemap，方便调试

eslint-loader

加载顺序和配置顺序相反

## plugin

强调事件监听事件

改变打包后输出结果

new xxx()

html-webpack-plugin 	打包产物自动引入.html文件

clean-webpack-plugin 	清理打包产物

mini-css-extract-plugin   分离样式文件

### 区别

loader本质上是一个函数，对接受到的内容进行处理，返回转换后的结果。webpack只能处理Javascript，所以Loader就是对非JS资源进行转换

plugin就是插件，主要用于扩展webpack功能，监听webpack运行生命周期中的广播事件，在合适的时间改变其输出结果



## 优化

#### loader配置优化

使用include/exclude，明确哪些文件需要处理，哪些不需要

#### 缓存

webpack5 自带持久化缓存，配置：

```
cache: {
    type: 'memory'
},
```

#### 代码拆分

Mode:production 自动开启

#### mode production

自动做了很多优化

### thread-loader 多线程构建

