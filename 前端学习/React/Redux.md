### Redux

`provider    ` 使Redux存储可用于您的应用程序

```jsx
import { Provider } from 'react-redux'
...
<Provider store={store}>
  <App />
</Provider>
```

`connect`  提供了将组件连接到store的功能

```jsx
import { connect } from 'react-redux'
...
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter)
```



#### Hook with redux

[参考链接-Using Hooks in a React Redux App](https://react-redux.js.org/api/hooks)

> **explanation**：*React Redux now offers a set of hook APIs as an alternative to the existing `connect()` Higher Order Component. These APIs allow you to subscribe to the Redux store and dispatch actions, without having to wrap your components in `connect()`.*

##### `useSelector()`

从 Redux store state 中获取数据

```jsx
import { useSelector, useDispatch } from 'react-redux'
...
const hiveFormValues = useSelector(state => state.meta.hiveFormValues)
...
```

##### `useDispatch()`

这个钩子函数返回 dispatch，让开发者在代码里可以 dispatch 对应的 action ，从而提交变化的新值

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



