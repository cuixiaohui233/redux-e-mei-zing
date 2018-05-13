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
