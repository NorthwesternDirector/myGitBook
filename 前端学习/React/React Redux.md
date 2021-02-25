# React Redux

> React Redux 是 React 官方绑定的 Redux，它使您的 React 组件可以从 Redux 存储中读取数据，并向存储分派操作以更新数据
>
> [传送门](https://react-redux.js.org/)

## 确定要使用 redux ？

- [x] UI 需要根据状态变化
- [x] 状态并不总是以一种线性的，单向的方式流动（组件树复杂）
- [x] 许多不相关的组件以相同的方式更新状态
- [x] 状态以许多不同的方式更新
- [x] 您需要能够撤消以前的用户操作 ❓

## 使用后有什么优势 ？

**Integrating Redux with a UI**

🤔：*Redux存在类似数据双向绑定的优势，data更新->UI更新，UI更新->data更新*

（优点慢慢更新中...）

## 使用后有什么缺点 ？

1. 状态提前提升带来的管理混乱以及不必要的重新渲染（见文末）
2. redux 是需要额外引入的组件

（缺点也在慢慢更新中...）

## Redux 细节

### Provider-环境

嵌套在 `<Provider> ` 内的组件只要通过 connect 包裹，都可以访问到 store

```jsx
import { Provider } from 'react-redux'
...
<Provider store={store}>
  <App />
</Provider>
```

### connect-管道

`connect`  提供了将组件连接到store的功能

```jsx
/* --------示例用法---------- */
import { connect } from 'react-redux'
...
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter)
/* ------------------------- */

/* --------connnect方法参数说明---------- */
const mapStateToProps = (state, ownProps) => ({
 	// 方法支持从 store 中取数据并返回
  // 第二个参数 ownProps 为组件接受的 props 值，当组件 props 改变时（浅比较），会执行当前函数
  todo: state.todos[ownProps.id]
})

const mapDispatchToProps = (dispatch, ownProps) => {
  // 可以让 dispatch 方法不被组件察觉，减轻心智负担
  // 如果不传此参数，组件还是会默认接受一个 dispatch 方法
  // 第二个参数 ownProps 为组件接受的 props 值，当组件 props 改变时（浅比较），会执行当前函数
  return {
    increment: () => dispatch({ type: 'INCREMENT' }),
    decrement: () => dispatch({ type: 'DECREMENT' }),
    reset: () => dispatch({ type: 'RESET' })
  }
}

const  mergeProps =(stateProps, dispatchProps, ownProps) => Object
//不设置的话默认返回 { ...ownProps, ...stateProps, ...dispatchProps }

//options 看文档吧 https://react-redux.js.org/api/connect#options-object

function connect(mapStateToProps, mapDispatchToProps, mergeProps, options)
/* ------------------------------------ */
```

### dispatch-触发器

触发 reducer 内指定的 action 去更新 store

### Reducer-执行器（payload，action type）

> Reducer 名字的缘由：因为reducer函数的（参数及返回值）类型与被传入 **Array.prototype.reduce(reducer, ?initialValue)** 方法的回调函数的类型相似

***reducer仅仅是一个概念***，它实际是一个函数，通过接收不同 action type 匹配不同的 action ，结合不同的 payload，执行对应的操作

### Hook with redux

[参考链接-Using Hooks in a React Redux App](https://react-redux.js.org/api/hooks)

> **explanation**：*React Redux now offers a set of hook APIs as an alternative to the existing `connect()` Higher Order Component. These APIs allow you to subscribe to the Redux store and dispatch actions, without having to wrap your components in `connect()`.*

####  useSelector()

从 Redux store state 中获取数据，***天降 store***

```jsx
import { useSelector, useDispatch } from 'react-redux'
...
const hiveFormValues = useSelector(state => state.meta.hiveFormValues)
...
```

#### useDispatch()

这个钩子函数返回 dispatch，***天降 dispatch***

```jsx
import { useSelector, useDispatch } from 'react-redux'
const dispatch = useDispatch()
...
dispatch({
   type: 'SET_HIVE_FORMVALUE',
   payload: {
     fileType,
     serdeProtocol,
     serdeParamsFormat,
     definition,
     thriftStruct,
   },
 })
...
```

#### useStore()

*不常用，用useSelector()即可。官方：这对于一些不太常见的场景是有用的，例如替换 reducers*

## 还在使用redux ？

### useContext + useReducer 🤡

> 神奇hook帮助你，良渝：优点是无需引入多余的库。具体使用见 [React Hook](./React Hooks.md)

###  一些 React 状态管理的建议 🌟

#### 避免潜在的 re-render

[学习链接传送门](https://overreacted.io/before-you-memo/)

核心思想都是 **是谁的状态就放谁那**

组件平行时：属于子组件状态就放置在子组件内，避免放置在父组件内***导致其他兄弟组件的重新渲染***（🌰见code1）

组件嵌套时：将受状态影响的部分单独封装，不受状态影响的子组件通过props传入；***由于prop始终保持不变，其子结构不会重新渲染***（🌰见code2）

```jsx
//code1
// ----------------改造前------------------
export default function App() {
  /* color改变会引起 ExpensiveTree组件的不必要渲染😣 */
  let [color, setColor] = useState('red'); 
  return (
    <div>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p style={{ color }}>Hello, world!</p>
      <ExpensiveTree />
    </div>
  );
}
// ----------------改造后------------------
export default function App() {
  return (
    <>
      <Form />
      <ExpensiveTree />
    </>
  );
}
/* 单独封装了一个组件，并将属于当前组件的状态都收纳了进来👍 */
function Form() {
  let [color, setColor] = useState('red');
  return (
    <>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p style={{ color }}>Hello, world!</p>
    </>
  );
}
```

```jsx
// code2
// ----------------改造前------------------
export default function App() {
  let [color, setColor] = useState('red');
  /* 与code1不同，使用color状态的部分无法单独进行封装，方法1无效😣 */
  return (
    <div style={{ color }}>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      <p>Hello, world!</p>
      <ExpensiveTree />
    </div>
  );
}
// ----------------改造后------------------
export default function App(){
  /* 将与color状态有关的内容提升，重新封装，将<ExpensiveTree>通过children传入 👍 */
  /* 原理是color状态改变<ColorPart>组件重新渲染，当子树被当作prop传入且无改变时，不会跟随着一起重新渲染 */
  return (
  	<ColorPart>
      <ExpensiveTree />
    </ColorPart>
  )
}
function ColorPart({children}){
  let [color, setColor] = useState("red");
  return (
    <div style={{ color }}>
      <input value={color} onChange={(e) => setColor(e.target.value)} />
      {children}
    </div>
  );
}
```

