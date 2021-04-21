通过app.use(Router)使用



router：全局路由器

Route：当前路由

api类似react-router



## 路由守卫

组件内守卫：

beforeRouteEnter((to, from, next)=>{ 

 })   无法获取vm实例，可以通过next回调来使用组件vm实例

next(vm=>{}) 导航被确认后，会执行next中的回调

to, from 都是route对象

beforeRouteUpdate

beforeRouteLeave



配置meta字段





### 导航解析流程

1. 导航触发，
2. 失活组件中调用beforeRouterLeave
3. 调用全局beforeEach（全局前置守卫）
4. 重用组件中调用beforeRouteUpdate 守卫
5. 配置中调用beforeEnter
6. 解析异步路由
7. 被激活组件中调用beforeRouteEnter
8. 嗲用全局beforeResolve
9. 确认导航
10. 调用全局afterEach
11. 触发DOM更新
12. 调用beforeRouteEnter中的next回调