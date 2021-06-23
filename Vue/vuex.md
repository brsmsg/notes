## 状态管理模式

* state
* view
* action

类似Redux

component中dispatch acitons。或者commit mutations，

通过mutations修改state，state修改后render到component上

![vuex](https://vuex.vuejs.org/vuex.png)

### store

改变store中的状态的**唯一**途径就是显示提交mutation

单一状态，每个应用只有一个store

读取状态使用computed(()=> store.state) 返回的同样是proxy对象

* 使用

```javascript
const store =createStore({
	state: {
		count: 0
	},
	mutationse:{
		increment(state){
			state.count++;
		}
	}
})


store.state.count;
store.commit('increment');

//组件中访问
app.use(store)
this.$store.xxx
```



一个组件获取多个状态，使用mapState

```
computed: mapState({
	count: state.count,
	counAlias: 'count',	//等同于state=>state.count
})
```



### getters

(看作store中的computed) 多个组件使用同一个属性时，使用getter

Access方法：

1. property style：store.getters.xxx

2. method style: getter中返回函数，store.getters.xxx(args) 可传参，使用函数时，每次都会进行调用，而不是缓存结果。

3. mapGetters： ...mapGetters([

   ​	'xxx',

   ​	'xxx'

   ]) 



### mutation

一个mutation又一个string type和一个handler

handler第一个参数为state，第二个参数为payload

eg: 

```javascript
increment (state, n) {
    state.count += n
  }
```



!! mutation 必须是同步 如果是async callback，mutation已经执行了callback还没有执行

提交mutation

```
store.commit("increment", payloads)
```





### Action

action类似mutation，不同在于：

* action 提交mutation，而不是直接change state
* action可以进行一步操作

action函数接受一个ctx对象(包含store的所有属性/方法)，ctx.commit()，直接解构. 

function({commit}){}



* 分发：store.dispatch(). 使用 payload method or object method



store.dispatch处理promise后仍返回promise



### modules



## 单元测试

### mutations



### actions

内容：

1. 是否正确使用了API
2. payload是否正确
3. 根据结果，是否有正确的mutation被commit

