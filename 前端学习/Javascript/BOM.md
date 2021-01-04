# BOM

> JS 浏览器对象模型（Browser Object Model）

`window` 是客户端浏览器对象模型的基类，window 对象是客户端 JavaScript 的全局对象。一个 window 对象实际上就是一个独立的窗口，对于框架页面来说，浏览器窗口每个框架都包含一个 window 对象。在客户端浏览器中，***window 对象是访问 BOM 的接口***。

## localStorage & sessionStorage

> 注意⚠️：localStorage&sessionStorage 目前仅支持*字符串 (String)* 类型的 value 值，如果想使用存储对象，可以使用JOSN提供的 parse()&stringfy() 方法手动进行的解析和字符串化

### localStorage 

存到天荒地老 🥰

```js
// 保存用户的选择习惯，做默认回填项
window.localStorage.setItem('ItemName',ItemValues)
window.localStorage.getItem('ItemName')
```

### sessionStorage

关闭页面后就清除数据了🧐

