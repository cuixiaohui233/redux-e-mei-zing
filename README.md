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

返回值： 返回一个对象，给外部提供 dispatch、getState、subscrbe、replaceReducer

首先 判断 loadedState 是不是一个 function， enhancer 是不是 undefined，如果是就将 preloadedState（初始状态）赋给 enhancer

将 loadedState 设为 undefined.

判断enhancer是不是 undefined 如果不是就判断 enhancer 是不是 function 如果不是就抛出异常，enhancer 必须是一个 function

返回 enhancer 前面说了这个一个高阶的函数，是一个组合 store creator 的高阶函数，返回一个新的强化过的 store creator。这与

middleware 相似，它也允许你通过复合函数改变 store 接口。他妈的到底是个啥，不懂...

判断 reducer 是不是function 如果不是就抛出异常 reducer 必须是一个 function

    let currentReducer = reducer 懂
    let currentState = preloadedState 懂
    let currentListeners = [] 干嘛的？
    let nextListeners = currentListeners 干嘛的？
    let isDispatching = false 不知道

bu行了。。。,dei gan jin shui jiao le

emmmm....

今天贼累，搬东西胳膊酸死...

今天撸不动了，笑哭

什么都不更了。