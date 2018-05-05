# redux-LOL

1.看看 createStore

创建一个 redux store 来掌控你的数据树

只能通过 "dispatch()" 来改变你的数据

他们是你的 app 中唯一的 store.去指定 state 中不同的部分来响应actions，你可以通过"combineReducers"合并多个reducers到一个

reducer 到一个reducer。

参数1：reducer,一个函数， 连个参数，分别是当前的 state 树和要处理的 action返回新的state树

参数2：preloadedState, 初始化时的state，你可以把服务器端传来经过处理的state创建它 ,如果你使用 combinReducers创建reducer,它必

须时一个普通的对象，与传入的keys保持相同的结构。否则，你可以自由传入任何reducer可以理解的内容。

参数3：enhancer 一个组合的高阶函数，返回一个强化过的 store creator, 这与  middleware 相似，他也允许你通过复合函数改变 store 接口。

返回值： 返回一个对象，给外部提供dispatch、getState、subscrbe、replaceReducer

参数：bu行了。。。,dei gan jin shui jiao le


今天贼累，搬东西胳膊酸死...

今天撸不动了，笑哭