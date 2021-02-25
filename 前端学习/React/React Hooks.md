# React Hooks

## 前置知识

### 生命周期方法（函数）

- [看图](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)
- *（v16.4 生命周期函数图）*
- 生命周期方法用于在组件不同阶段执行自定义功能：在组件被创建并插入到 DOM 时（即挂载中阶段(mounting)）、组件更新时、组件取消挂载或从 DOM 中删除时，都有可以使用的生命周期方法

## useState



## useEffect

### 迷惑的 useEffect

发生了什么？

- react 在 diff 后，会进入到 ***Commit 阶段***，准备把虚拟 DOM 发生的变化映射到真实 DOM 上，在这个阶段，会把使用了 useEffect 组件产生的生命周期函数入列到 React 自己维护的调度队列中，给予一个普通的优先级，让这些生命周期函数异步执行。

```js
// 可以近似的认为，React 做了这样一步，实际流程中要复杂的多
setTimeout(() => {
   const preDestory = element.destroy;
   if (preDestory) prevDestroy();
   const destroy = create();
   element.destroy= destroy;
}, 0);
```

- 在 commit 阶段的前期，会调用一些生命周期方法，对于类组件来说，需要触发组件的 getSnapshotBeforeUpdate 生命周期函数，对于函数组件，此时会调度 useEffect 的 create destroy 函数（⚠注意是调度，不是执行）。
- 随后就到了 React 把 ***虚拟 DOM 设置到真实 DOM 上的阶段***，这个阶段主要调用的函数是 commitWork，commitWork 函数会针对不同的 fiber 节点调用不同的 DOM 修改方法，比如文本节点和元素节点的修改方法是不一样的。
- commitWork 如果遇到了类组件的 fiber 节点，不会做任何操作，会直接 return，进行收尾工作，然后去处理下一个节点，因为类组件的 fiber 节点没有对应的真实 DOM 结构，所以就没有相关操作（⚠注意一个节点在 commitWork 后，这个时候，我们已经把发生的变化映射到真实 DOM 上了，但由于 JS 线程和浏览器渲染线程是互斥的，因为 JS 虚拟机还在运行，即使内存中的真实 DOM 已经变化，浏览器也没有立刻渲染到屏幕上）
- 但在有了 hooks 以后，函数组件在这个阶段，会同步调用上一次渲染时 useLayoutEffect(create, deps) create 函数返回的 destroy 函数
- 紧接着会进入***收尾阶段***，同步执行对应的生命周期方法，componentDidMount、componentDidUpdate 以及 useLayoutEffect(create, deps) 的 create 函数都是在这个阶段被同步执行
- 对于 react 来说，commit 阶段是不可打断的，会一次性把所有需要 commit 的节点全部 commit 完，至此 react 更新完毕，JS 停止执行
- 浏览器把发生变化的 DOM 渲染到屏幕上，到此为止 react 仅用一次回流、重绘的代价，就把所有需要更新的 DOM 节点全部更新完成
- 浏览器渲染完成后会通知 react 自己处于***空闲阶段***，react 开始执行自己调度队列中的任务，此时才开始执行 useEffect(create, deps) 产生的函数

### 重新认识useEffect

useLayouteffect

> 其函数签名与 useEffect 相同，但它会在所有的 DOM 变更之后同步调用 effect。可以使用它来读取 DOM 布局并同步触发重渲染。在浏览器执行绘制之前，useLayoutEffect 内部的更新计划将被同步刷新。尽可能使用标准的 useEffect 以避免阻塞视觉更新

- useLayoutEffect，从源码中调用的位置来看，useLayoutEffect的 create 函数的调用位置、时机都和 componentDidMount，componentDidUpdate 一致，且都是被 React 同步调用，都会阻塞浏览器渲染。
- useLayoutEffect 的 detroy 函数的调用位置、时机与 componentWillUnmount 一致，且都是同步调用。useEffect 的 detroy 函数从调用时机上来看，更像是 componentDidUnmount (注意React 中并没有这个生命周期函数)。

参考文献

1. [深入理解 React useLayoutEffect 和 useEffect 的执行时机](https://blog.csdn.net/yunfeihe233/article/details/106616674/)
2. [React: the-component-lifecycle](https://zh-hans.reactjs.org/docs/react-component.html#the-component-lifecycle)

### useEffect & setInterval 

> 属于闭包问题，不了解闭包是啥：[传送门]()

需求：显示3s倒计时

```jsx
const LoginModal = ({ setModal }) => {
  const [second, setSecond] = useState(3)
  useEffect(() => {
    const timer = setInterval(() => {
      if (second > 0) {
        setSecond(second - 1) // 调试可以发现一直都是“3-1” ✅ setSecond(val => val - 1)
      } else {
        clearInterval(timer)
      }
    }, 1000)
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [])
  ...
}
```

先举个例子 🌰：

```jsx
...
const [count, setCount] = useState(0);
console.log('render val:', count) // render val:0,1,2,3
useEffect(() => {
    let id = setInterval(() => {
      console.log('interval val:', count)// interval val:0,0,0,0
      setCount(val => val + 1);
    }, 1000);
    return () => clearInterval(id);
}, []);
...
```

可以看到 useEffect 只会被执行一次， `setInterval` 也只会被注册一次

* `setInterval` ***第一个参数（函数）引用了外部变量 count，故形成了闭包***，函数内部引用的 count 值永远停留在第一次引用它时的状态

* XJB操作 🌝：删了 useEffect 的引用相等性判断 [ ]，让 useEffect 随每次 render 都执行，setInterval ***每次会拿到新的状态形成新的闭包***，但如果触发重新 render 的因素不止 setCount 一个时，计时就会出现混乱

* 基本操作：使用 setCount(val => val + 1) 方法，利用setCount 内部的实现原理，总能准确获取上一次的 state 值后加一
* 变道超车：使用 useRef 去同步 count 值，setInterval  内部访问 useRef 内存储的值，由于 useRef 在 hook 内维护的是一个 ref 对象，每次访问，虽然引用的是初始状态的对象，但对象的属性值一直在改变，故 setInterval 内也能访问到最新的值

## useReducer

>  useReducer 可以让你通过 reducer 来管理组件复杂的本地 state

```jsx
// 和 redux 尿性差不多一样
// 1. state 就是 ‘store’，可通过两种方式初始化： 1. 利用 initialArg 参数进行初始化 2. 利用 init(initialArg) 参数进行初始化
// 2. dispatch 就是触发器（触发 reducer 中的内容），参数带上 actionType&payload，就会触发 reducer 内指定的方法
// 3. dispatch 使用：dispatch(action) ，action通常为一个对象：{type:'actionType',payload:{}}
// 4. 使用 useReducer 时，如果返回了相同的 state，react不会重新渲染

const [state, dispatch] = useReducer(reducer, initialArg, init)
```

注意⚠️：当需要层层传递回调函数时，建议使用 useReducer [how-to-avoid-passing-callbacks-down](https://reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down)

## useContext

> useContext(MyContext) 让我们能够读取 context 的值以及订阅 context 的变化

```jsx
const MyContext = React.createContext(initData);
...
<MyContext.Provider value={changeData}>
   <Toolbar />
</MyContext.Provider>
...
const Toolbar = () => {
  const context = useContext(MyContext);
   /*
    * 使用 context
   */
}

// useContext 和 class 组件中的 <MyContext.Consumer> 一个意思
// 1. useContext 接收一个 context 对象，并返回该 context 的当前值
// 2. 调用了 useContext 的组件总会在 context 值变化时重新渲染
const value = useContext(MyContext);
```

### 组合使用

注意⚠️：

1. useReducer+useContext 的组合***发挥了 useContext 数据跨层级存储传输能力***，使用 useContext 存储 useReducer 的 state&dispatch，使其易于在复杂组件结构中被获得；***发挥了 useReducer 类似redux的规范化操纵数据的能力***，对共用 state 的操作更加统一和规范。
2. 子组件进行 dispatch 操作更新 state 后，useReducer 会致使子组件重新渲染；state 改变即 context 值改变，调用了 useContext 的组件重新渲染，

```jsx
// 举个例子 🌰
//写个 reducer 函数
const triggerReducer = (state,action) => {
  return {...state, [action.type]: action.value }
}
//写个 context 对象
const MyContext = React.createContext()

//父组件使用 useReducer，同时将 state&dispatch 当作状态存入 context 中供子组件使用
const App = () => {
  const [state, dispatch] = useReducer(triggerReducer,{})
  return (
    <MyContext.Provider value={{state, dispatch}}>
      <Child/>
    </MyContext>
  )
}

const Child = () => {
  const {state, dispatch} = useContext(MyContext)
  ...
  //利用 state dispatch 就可以完成数据的CURD
  ...
}
```



## useImperativeHandle

### 前置知识

#### forwardRef (react 16.3)

> 父亲 ---(控制)---> 孩子[ forwardRef ]

作用：forwardRef 使用目的就是实现 ref 的转发（透传）：forwardRef 会创建一个 React 组件，这个组件能够将其接受的 ref 属性转发到其组件树下的另一个组件中；在封装组件时，外层组件可以通过 ref 直接控制内层组件或元素的行为

🤔：讲道理，可以用普通组件的 props 传 ref 呀，但事实就是函数组件不使用 forwardRef 的情况下是没法接收 ref 的 🤷‍♂️ （歪门邪道：名字不是 ref 的话其实就可以通过 props 进行传递）

使用方式：forwardRef 的参数为一个函数，该函数的第一个参数为父组件传递的 props，第二给参数为父组件传递的 ref

```js
// forwardRef 使用示例
import React, { useCallback, useRef } from 'react';
import ReactDOM from 'react-dom';
// 实现 ref 的转发
const FancyButton = React.forwardRef((props, ref) => (
  <div>
    <input ref={ref} type="text" />
    <button>{props.children}</button>
  </div>
));

// 普通 props 不可以转发 ref
/*const FancyButton = props => (
  <div>
    <input ref={props.ref} type="text" />
    <button>{props.children}</button>
  </div>
));*/

// 父组件中使用子组件的 ref
function App() {
  const ref = useRef();
  const handleClick = useCallback(() => ref.current.focus(), [ ref ]);

  return (
    <div>
      <FancyButton ref={ref}>Click Me</FancyButton>
      <button onClick={handleClick}>获取焦点</button>
    </div>
  )
}
ReactDOM.render(<App />, root);
```

### 加强版 forwardRef ？

作用：useImperativeHandle 可以让你在使用 ref 时 ***自定义暴露给父组件的实例值***

>  父亲 ---(控制)---> 孩子[ forwardRef +  useImperativeHandle ]

使用：useImperativeHandle 的第一个参数是需要被定义 current 对象的 ref，第二个参数是一个函数，返回值是一个对象，即这个 ref 的 current 对象，这样可以通过自定义父组件的 ref 来使用子组件 ref 的某些方法，第三个参数是一个数组，用作相等性判断是否更新第二个参数的返回值。

``` js
// useImperativeHandle 使用示例
import React, { useRef, useImperativeHandle } from 'react';
import ReactDOM from 'react-dom';

const FancyInput = React.forwardRef((props, ref) => {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));

  return <input ref={inputRef} type="text" />
});

const App = props => {
  const fancyInputRef = useRef();

  return (
    <div>
      <FancyInput ref={fancyInputRef} />
      <button
        onClick={() => fancyInputRef.current.focus()}
      >父组件调用子组件的 focus</button>
    </div>
  )
}
ReactDOM.render(<App />, root);
```

### 在 Antd v3.x 中

wrappedComponentRef 

> antd 3 官网： 经过 Form.create 之后如果要拿到 ref，可以使用 rc-form 提供的 wrappedComponentRef

⚠️ ：此行为拿到的不是 form 实例，而是 Form 这个子组件 !

- 回手掏 😂

通过 useImperativeHandle + forwardRef + ref绑定(父）的操作，**子组件可将内部的方法及属性暴露给父组件**，父组件实现回首掏 

🌍：例如modal中包含一个form，modal的确认按钮需要去调子组件form中字段校验的方法，哦豁，是不是要回手掏了 🤨



