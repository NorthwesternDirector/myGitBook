# history API

>  history 对象为 DOM window 对象提供了***访问浏览器会话历史的能力***，允许在用户浏览历史中向前和向后跳转；特别自 HTML5 后提供了对 history 栈中内容的操作功能。

## 常见方法

> 注意：以下5个方法***只会修改页面的 url 而不发送请求***；前3个方法仅支持在url历史栈中跳跃，后2个方法可跳至任意 url

### back()

后退到上一个路由

### forward()

前进到下一个路由，如果有的话

### go(number)

进入到任意一个路由，正数为前进，负数为后退

### pushState(state, title, url)

前进到指定的 url，***不刷新页面***

* `state`： 状态对象 state，包含一些路由携带的状态信息，popstate 事件的 state 属性会包含该历史记录条目状态对象的副本

  ⚠️注意：状态对象有640k的大小限制（序列化表示后）；状态过大建议使用 sessionStorage & localStorage

* `title`：目前没啥用 🌝，传个空字符串

* `url`：定义了新的历史URL记录

  ⚠️注意：url 可以为相对/绝对路径；新 url 必须与当前 url 同源

### replaceState(state, title, url)

用 url 替换当前的路由，***不刷新页面***

## popstate 事件 🌟 

> window.onpopstate 是 popstate 事件在 window 对象上的事件处理程序

**⚠️注意**：调用 history.pushState() 或者 history.replaceState() 不会触发 popstate 事件，popstate 事件只会在浏览器某些行为下触发, 比如点击后退、前进按钮（或者在 js 中调用 back()、forward()、go()方法），另外 a 标签的锚点也会触发该事件。

~~*猜测：React-Router  browersRouter 的实现是先将路由配置全部都推入栈中，底层通过 history.go() 的方法跳转来让 popstate 事件监听*~~

深入学习：React-Router  browersRouter 的实现是为 pushState&replaceState  方法手动注册监听事件

```jsx
const listener = function (type) {
  var orig = history[type];
  return function () {
    var rv = orig.apply(this, arguments);
    var e = new Event(type);
    e.arguments = arguments;
    window.dispatchEvent(e); // used to trigger an event
    return rv;
  };
};
window.history.pushState = listener('pushState');
window.history.replaceState = listener('replaceState')
...
window.addEventListener('pushState', this.refresh, false);//可以监听了😌
window.addEventListener('replaceState', this.refresh, false);
```

