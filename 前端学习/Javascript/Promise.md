# Promise

- [x]  2020.12.14 [参考传送门](https://zhuanlan.zhihu.com/p/183801144)

```js
// 嵌套结构产生的回调地狱👇
let fs = require('fs')
fs.readFile('./name.txt','utf8',function(err,data){
  fs.readFile(data, 'utf8',function(err,data){
    fs.readFile(data,'utf8',function(err,data){
      console.log(data);
    })
  })
})
```

Promise 用一种更加友好的代码组织方式（嵌套->链式），解决 *异步嵌套（”回调地狱“）*的问题

 [Promise/A+ 规范](https://promisesaplus.com/)

## Promise 流程大白话

1. promise 有三个状态：`pending`，`fulfilled`，or `rejected`，默认状态是 `pending`「规范 Promise/A+ 2.1」
2. `new promise` 时， 需要传递一个 `executor()` 执行器，执行器立即执行👇，`executor` 接受两个参数，分别是 `resolve` 和 `reject` 👇

```js
const fetch = new Promise((resolve,reject)=>{
  console.log('创建一个promise');
  resolve('成功了！')
  reject('失败了！')
})
```

5. promise 有一个 `value` 保存成功状态的值，可以是 `undefined/thenable/promise`「规范 Promise/A+ 1.3」
6. promise 有一个 `reason` 保存失败状态的值「规范 Promise/A+ 1.5」
7. promise 只能从 `pending` 到 `rejected`, 或者从 `pending` 到 `fulfilled`，状态一旦确认，就不会再改变
8. promise 必须有一个 `then` 方法，then 接收两个参数，分别是 promise 成功的回调 onFulfilled, 和 promise 失败的回调 onRejected👇「规范 Promise/A+ 2.2」

```js
const fetchData = fetch.then(value => {// onFullfilled
  console.log('success', value)
}, err => {// onRejectd
  console.log('faild', err)
})
```

9. 如果调用 then 时，promise 已经成功（成功是指代码逻辑上的成功之后调用 resolve 函数），则执行 `onFulfilled`，参数是 `promise` 的 `value`👆
10. 如果调用 then 时，promise 已经失败（失败是指代码逻辑上的失败之后调用 reject 函数），那么执行 `onRejected`, 参数是 `promise` 的 `reason`👆
11. 🌟 then 的参数 `onFulfilled` 和 `onRejected` 可以缺省，如果 `onFulfilled` 或者 `onRejected`不是函数，将其忽略，且依旧可以在下面的 then 中获取到之前返回的值「规范 Promise/A+ 2.2.1、2.2.1.1、2.2.1.2」
12. promise 可以 then 多次，每次执行完 promise.then 方法后返回的都是一个“新的promise"「规范 Promise/A+ 2.2.7」
13. 如果 then 的返回值 x 是一个普通值，那么就会把这个结果作为参数，传递给下一个 then 的成功回调 `onFulfilled` 中；
14. 如果 then 中抛出了异常，那么就会把这个异常作为参数，传递给下一个 then 的失败回调 `onRejected` 中；「规范 Promise/A+ 2.2.7.2」
15. 如果 then 的返回值 x 是一个 promise，那么会等这个 promise 执行完，promise 如果成功，就走下一个 then 的成功；如果失败，就走下一个 then 的失败；如果抛出异常，就走下一个 then 的失败；「规范 Promise/A+ 2.2.7.3、2.2.7.4」
16. 如果 then 的返回值 x 和 promise 是同一个引用对象，造成循环引用，则抛出异常，把异常传递给下一个 then 的失败的回调中；「规范 Promise/A+ 2.3.1」
17. 如果 then 的返回值 x 是一个 promise，且 x 同时调用 resolve 函数和 reject 函数，则第一次调用优先，其他所有调用被忽略；「规范 Promise/A+ 2.3.3.3.3」

## Promise 静态方法

### Promise.resolve()

默认产生一个成功的 promise。**promise.resolve 是具备等待功能的**，如果参数是 promise 会等待这个 promise 解析完毕，再向下执行。

```js
// 实现
static resolve(data){
  return new Promise((resolve,reject)=>{
    resolve(data);
  })
}

// 测试
Promise.resolve(new Promise((resolve, reject) => {
  setTimeout(() => {
    reject('ok');
  }, 3000);
})).then(data=>{
  console.log(data,'success')
},err=>{console.log('!!!',err)})
```

### Promise.reject()

默认产生一个失败的 promise，Promise.reject 是直接将传参值变成错误结果（不管值是啥都直接走失败的回调）

```js
// 实现
static reject(reason){
  return new Promise((resolve,reject)=>{
    reject(reason);
  })
}

// 测试
Promise.reject(new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('ok');
  }, 3000);
})).then(data=>{
  console.log(data,'success')
},err=>{console.log('!!!',err)})// “!!! Promise“
```

### Promise.race()

Promise.race 用来处理多个请求，采用最快的（谁先完成用谁的）

```js
Promise.race = function(promises) {
  return new Promise((resolve, reject) => {
    // 一起执行就是for循环
    for (let i = 0; i < promises.length; i++) {
      let val = promises[i];
      if (val && typeof val.then === 'function') {
        val.then(resolve, reject);
      } else { // 普通值
        resolve(val)
      }
    }
  });
}
```

### Promise.all()

promise.all 是解决并发问题的，多个异步并发获取最终的结果（如果有一个失败则失败）

```js
Promise.all = function(values) {
  if (!Array.isArray(values)) {
    const type = typeof values;
    return new TypeError(`TypeError: ${type} ${values} is not iterable`)
  }
  return new Promise((resolve, reject) => {
    let resultArr = [];
    let orderIndex = 0;
    const processResultByKey = (value, index) => {
      resultArr[index] = value;
      if (++orderIndex === values.length) {
          resolve(resultArr)
      }
    }
    for (let i = 0; i < values.length; i++) {
      let value = values[i];
      if (value && typeof value.then === 'function') {
        value.then((value) => {
          processResultByKey(value, i);
        }, reject);
      } else {
        processResultByKey(value, i);
      }
    }
  });
}
```

## Promise 原型方法

### Promise.prototype.catch()

Promise.prototype.catch 用来捕获 promise 的异常，**就相当于一个没有成功的 then**

```js
Promise.prototype.catch = function(errCallback){
  return this.then(null,errCallback)
}
```

### Promise.prototype.finally()

finally 表示无论如何都会执行的意思

> 注意⚠️：如果 finally 内返回一个 promise， 那么会等待这个 promise 也执行完毕，且如果它返回的是成功的 promise，会采用上一次的结果；如果返回的是失败的 promise，会用这个失败的结果，传到 catch 中

```js
// 实现
Promise.prototype.finally = function(callback) {
  return this.then((value)=>{
    return Promise.resolve(callback()).then(()=>value)
  },(reason)=>{
    return Promise.resolve(callback()).then(()=>{throw reason})
  })  
}

// 测试
// 控制台输出 “456 success“，即采用上一次的结果
Promise.resolve(456).finally(()=>{
  return new Promise((resolve,reject)=>{
    setTimeout(() => {
        resolve(123)
    }, 3000);
  })
}).then(data=>{
  console.log(data,'success')
}).catch(err=>{
  console.log(err,'error')
})

// 控制台输出 “123 "error"“，即用这个失败的结果
Promise.resolve(456).finally(()=>{
  return new Promise((resolve,reject)=>{
    setTimeout(() => {
        reject(123)
    }, 3000);
  })
}).then(data=>{
  console.log(data,'success')
}).catch(err=>{
  console.log(err,'error')
})
```