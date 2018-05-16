# redux-LOL

## [分析原文地址](https://github.com/kenberkeley/redux-simple-tutorial/blob/master/redux-advanced-tutorial.md)

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
    
找了新的教程，感觉比之前看那个好多了：

[地址](https://github.com/kenberkeley/redux-simple-tutorial/blob/master/redux-advanced-tutorial.md)

关于  createStore

源码：

        import isPlainObject from 'lodash/isPlainObject'
        import $$observable from 'symbol-observable'

        /**
        * 这是 Redux 的私有 action 常量
        * 长得太丑了，你不要鸟就行了
        */
        export var ActionTypes = {
            INIT: '@@redux/INIT'
        }

        /**
        * @param  {函数}  reducer 不多解释了
        * @param  {对象}  preloadedState 主要用于前后端同构时的数据同步
        * @param  {函数}  enhancer 很牛逼，可以实现中间件、时间旅行，持久化等
        * ※ Redux 仅提供 applyMiddleware 这个 Store Enhancer ※
        * @return {Store}
        */
        export default function createStore(reducer, preloadedState, enhancer) {
            // 这里省略的代码，到本文的最后再讲述（用于压轴你懂的）666

            var currentReducer = reducer         // 这玩意就是reducer那个纯函数，纯爷们......
            var currentState = preloadedState    // 这就是整个应用的 state
            var currentListeners = []            // 用于存储订阅的回调函数，dispatch 后逐个执行  ???这么说我在项目里多次dispatch是合理的吗 
            var nextListeners = currentListeners //【悬念1：为什么需要两个 存放回调函数 的变量？】
            var isDispatching = false

            /**
            * 【悬念1·解疑】
            * 试想，dispatch 后，回调函数正在乖乖地被逐个执行（for 循环进行时）
            * 假设回调函数队列原本是这样的 [a, b, c, d]
            *
            * 现在 for 循环执行到第 3 步，亦即 a、b 已经被执行，准备执行 c
            * 但在这电光火石的瞬间，a 被取消订阅！！！(取消订阅咋个意思？？？不懂...
            *
            * 那么此时回调函数队列就变成了 [b, c, d]
            * 那么第 3 步就对应换成了 d！！！
            * c 被跳过了！！！这就是躺枪。。。哈哈哈
            * 
            * 作为一个回调函数，最大的耻辱就是得不到执行
            * 因此为了避免这个问题，本函数会在上述场景中把
            * currentListeners 复制给 nextListeners
            *
            * 这样的话，dispatch 后，在逐个执行回调函数的过程中
            * 如果有新增订阅或取消订阅，都在 nextListeners 中操作
            * 让 currentListeners 中的回调函数得以完整地执行
            *
            * 既然新增是在 nextListeners 中 push，因此毫无疑问
            * 新的回调函数不会在本次 currentListeners 的循环体中被触发
            *
            * （上述事件发生的几率虽然很低，但还是严谨点比较好）
            */
            function ensureCanMutateNextListeners() { // <-------这货就叫做【ensure 哥】吧
                if (nextListeners === currentListeners) {
                nextListeners = currentListeners.slice()
                }
            }

            /**
            * 返回 state
            */
            function getState() {
                return currentState
            }

            /**
            * 负责注册回调函数的老司机
            * 
            * 这里需要注意的就是，回调函数中如果需要获取 state
            * 那每次获取都请使用 getState()，而不是开头用一个变量缓存住它
            * 因为回调函数执行期间，有可能有连续几个 dispatch 让 state 改得物是人非
            * 而且别忘了，dispatch 之后，整个 state 是被完全替换掉的
            * 你缓存的 state 指向的可能已经是老掉牙的 state 了！！！
            *??? 这个state不是个对象吗？对象不是赋址？？？
            * @param  {函数} 想要订阅的回调函数
            * @return {函数} 取消订阅的函数
            */
            function subscribe(listener) {
                if (typeof listener !== 'function') {
                    throw new Error('Expected listener to be a function.')
                }

                var isSubscribed = true

                ensureCanMutateNextListeners() // 调用 ensure 哥保平安
                nextListeners.push(listener)   // 新增订阅在 nextListeners 中操作

                // 返回一个取消订阅的函数
                return function unsubscribe() {
                    if (!isSubscribed) {
                        return
                    }

                    isSubscribed = false

                    ensureCanMutateNextListeners() // 调用 ensure 哥保平安
                    var index = nextListeners.indexOf(listener)
                    nextListeners.splice(index, 1) // 取消订阅还是在 nextListeners 中操作
                }
            }

            /**
            * 改变应用状态 state 的不二法门：dispatch 一个 action
            * 内部的实现是：往 reducer 中传入 currentState 以及 action
            * 用其返回值替换 currentState，最后逐个触发回调函数
            *
            * 如果 dispatch 的不是一个对象类型的 action（同步的），而是 Promise / thunk（异步的）
            * 则需引入 redux-thunk 等中间件来反转控制权【悬念2：什么是反转控制权？】
            * 
            * @param & @return {对象} action
            */
            function dispatch(action) {
                if (!isPlainObject(action)) {
                    throw new Error(
                    'Actions must be plain objects. ' +
                    'Use custom middleware for async actions.'
                    )
                }

                if (typeof action.type === 'undefined') {
                    throw new Error(
                    'Actions may not have an undefined "type" property. ' +
                    'Have you misspelled a constant?'
                    )
                }

                if (isDispatching) {
                    throw new Error('Reducers may not dispatch actions.')
                }

                try {
                    isDispatching = true
                    // 关键点：currentState 与 action 会流通到所有的 reducer
                    // 所有 reducer 的返回值整合后，替换掉当前的 currentState
                    currentState = currentReducer(currentState, action)
                } finally {
                    isDispatching = false
                }

                // 令 currentListeners 等于 nextListeners，表示正在逐个执行回调函数（这就是上面 ensure 哥的判定条件）
                var listeners = currentListeners = nextListeners

                // 逐个触发回调函数
                for (var i = 0; i < listeners.length; i++) {
                    listeners[i]()

                    /* 现在逐个触发回调函数变成了：
                    var listener = listeners[i]
                    listener() // 该中间变量避免了 this 指向 listeners 而造成泄露的问题 */
                    // 在此衷心感谢 @BuptStEve 在 issue7 中指出之前我对此处的错误解读
                }

                return action // 为了方便链式调用，dispatch 执行完毕后，返回 action（下文会提到的，稍微记住就好了）
            }

            /**
            * 替换当前 reducer 的老司机
            * 主要用于代码分离按需加载、热替换等情况
            *
            * @param {函数} nextReducer
            */
            function replaceReducer(nextReducer) {
                if (typeof nextReducer !== 'function') {
                    throw new Error('Expected the nextReducer to be a function.')
                }

                currentReducer = nextReducer         // 就是这么简单粗暴！
                dispatch({ type: ActionTypes.INIT }) // 触发生成新的 state 树
            }

            /**
            * 这是留给 可观察/响应式库 的接口（详情 https://github.com/zenparsing/es-observable）
            * 如果您了解 RxJS 等响应式编程库，那可能会用到这个接口，否则请略过
            * @return {observable}
            */
            function observable() {略}

            // 这里 dispatch 只是为了生成 应用初始状态
            dispatch({ type: ActionTypes.INIT })

            return {
                dispatch,
                subscribe,
                getState,
                replaceReducer,
                [$$observable]: observable
            }
        }
        【悬念2：什么是反转控制权？ · 解疑】
        在同步场景下，dispatch(action) 的这个 action 中的数据是同步获取的，并没有控制权的切换问题
        但异步场景下，则需要将 dispatch 传入到回调函数。待异步操作完成后，回调函数自行调用 dispatch(action)

        说白了：在异步 Action Creator 中自行调用 dispatch 就相当于反转控制权
        您完全可以自己实现，也可以借助 redux-thunk / redux-promise 等中间件统一实现
        （它们的作用也仅仅就是把 dispatch 等传入异步 Action Creator 罢了）



bu行了。。。,dei gan jin shui jiao le

emmmm....

今天贼累，搬东西胳膊酸死...

今天撸不动了，笑哭

什么都不更了。

redux 的 compose 了解一下：

源码：

        /**
         * 看起来逼格很高，实际运用其实是这样子的：
         * compose(f, g, h)(...arg) => f(g(h(...args)))
         *
         * 值得注意的是，它用到了 reduceRight，因此执行顺序是从右到左
         *
         * @param  {多个函数，用逗号隔开}
         * @return {函数}
         */

        export default function compose(...funcs) {
            if (funcs.length === 0) {
            // 判断这个 传进来的函数是不是 一个，如果是，返回一个 arg => arg 的函数
            return arg => arg
        }

        if (funcs.length === 1) {
            // 如果 length 为1，那么返回第一个函数
            return funcs[0]
        }

        // 定义 last 变量，并且将最后一个函数赋值
        const last = funcs[funcs.length - 1]
        // 将所有的函数赋址给 rest
        const rest = funcs.slice(0, -1) 
        // 使用reduceRight得出 func[-4](func[-3](func[-2](func[-1](...args))))
        return (...args) => rest.reduceRight((composed, f) => f(composed), last(...args))
        }
        
        
        
实例演示走一波：

        <!DOCTYPE html>
        <html>
        <head>
          <script src="//cdn.bootcss.com/redux/3.5.2/redux.min.js"></script>
        </head>
        <body>
        <script>
        function func1(num) {
          console.log('func1 获得参数 ' + num);
          return num + 1;
        }

        function func2(num) {
          console.log('func2 获得参数 ' + num);
          return num + 2;
        }

        function func3(num) {
          console.log('func3 获得参数 ' + num);
          return num + 3;
        }

        // 有点难看（如果函数名再长一点，那屏幕就不够宽了）
        var re1 = func3(func2(func1(0)));
        console.log('re1：' + re1);

        console.log('===============');

        // 很优雅
        var re2 = Redux.compose(func3, func2, func1)(0);
        console.log('re2：' + re2);
        </script>
        </body>
        </html>
        
控制台输出：

func1 获得参数 0

func2 获得参数 1

func3 获得参数 3

re1：6

===============

func1 获得参数 0

func2 获得参数 1

func3 获得参数 3

re2：6

## 关于 combineReducers

当项目功能越来越强大，代码会越来越复杂，但是每个模块，每个组件的状态是不一样的，不能够写在一起需要独立开来分成不同的reducer,最后合并 ：

源码：

        function combineReducers(reducers) {
          var reducerKeys = Object.keys(reducers)
          var finalReducers = {}

          for (var i = 0; i < reducerKeys.length; i++) {
            var key = reducerKeys[i]
            if (typeof reducers[key] === 'function') {
              finalReducers[key] = reducers[key]
            }
          }

          var finalReducerKeys = Object.keys(finalReducers)

          // 返回合成后的 reducer
          return function combination(state = {}, action) {
            var hasChanged = false
            var nextState = {}
            for (var i = 0; i < finalReducerKeys.length; i++) {
              var key = finalReducerKeys[i]
              var reducer = finalReducers[key]
              var previousStateForKey = state[key]                       // 获取当前子 state
              var nextStateForKey = reducer(previousStateForKey, action) // 执行各子 reducer 中获取子 nextState
              nextState[key] = nextStateForKey                           // 将子 nextState 挂载到对应的键名
              hasChanged = hasChanged || nextStateForKey !== previousStateForKey
            }
            return hasChanged ? nextState : state
          }
        }
