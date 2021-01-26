# history API

>  history 对象为 DOM window 对象提供了***访问浏览器会话历史的能力***，允许在用户浏览历史中向前和向后跳转；特别自 HTML5 后提供了对 history 栈中内容的操作功能

## 常见方法

> 注意：前3个方法仅支持在url历史栈中跳跃，后2个方法可跳至任意 url

Q1: 在工场使用 window.history 方法时为啥均不刷新页面？

### back()

后退到上一个路由。***加载新页面***

### forward()

前进到下一个路由，如果有的话。***加载新页面***

### go(number)

进入到任意一个路由，正数为前进，负数为后退。***加载新页面***

### pushState(state, title, url)

前进到指定的 url，***不加载新页面，只改变url及状态***

* `state`： 状态对象 state，包含一些路由携带的状态信息，popstate 事件的 state 属性会包含该历史记录条目状态对象的副本

  ⚠️注意：状态对象有640k的大小限制（序列化表示后）；状态过大建议使用 sessionStorage & localStorage

* `title`：目前没啥用 🌝，传个空字符串也行

* `url`：定义了新的历史URL记录

  ⚠️注意：url 可以为相对/绝对路径；新 url 必须与当前 url 同源

### replaceState(state, title, url)

用 url 替换当前的路由，***不加载新页面，只改变url及状态***

## popstate 事件 🌟 

> window.onpopstate 是 popstate 事件在 window 对象上的事件处理程序

* popstate 事件只会在 same document 下，当 acticve history 记录在两个history记录之间切换时，被 window 调用。

* 如果当前 acticve history 记录是由 pushState() 方法创建的，或是由 replceState() 覆盖替换的，popstate 事件的 state 属性将会包含当前 acticve history 记录的state对象的一个副本。

**⚠️注意**：调用 history.pushState() 或者 history.replaceState() 不会触发 popstate 事件，popstate 事件只会在浏览器某些行为下触发, 比如点击后退、前进按钮（或者在 js 中调用 back()、forward()、go()方法），另外 a 标签的锚点也会触发该事件。

~~*猜测：React-Router  browersRouter 的实现是先将路由配置全部都推入栈中，底层通过 history.go() 的方法跳转来让 popstate 事件监听*~~

深入学习：

React-Router  BrowersRouter 可以改写代码让 pushState&replaceState 在调用的同时手动触发事件

```jsx
const listener = function (type) {
  var orig = history[type];
  return function () {//调用的同时，手动触发
    var rv = orig.apply(this, arguments);
    var e = new Event(type);
    e.arguments = arguments;
    window.dispatchEvent(e); // 触发事件
    return rv;
  };
};
window.history.pushState = listener('pushState');
window.history.replaceState = listener('replaceState')
...
window.addEventListener('pushState', function(){}, false);//可以监听了😌
window.addEventListener('replaceState', function(){}, false);
```

# history 库

> react-router 核心库 history  [github传送门](https://github.com/ReactTraining/history)。react-router使用history的库来***监听不同的路由变化***

通过阅读 history 库源码，发现🤔：

* state 值是通过 window.history.state 获取后塞入 location 对象中进行展示的

* ~~step1: 🤡BrowserRouter 因为设置了basename 为 #，代码中实际 push/replace 的路径都是 # 号之后的内容，history 库createBrowserRouter 生成的 history 对象是根据路径的内容获取 hash 内容👇，所以导致用 useLocation 获取 hash 时为空~~ 

  step2: 上边的逻辑有问题，path不是直接拿用户传的，是通过 window.location. (pathname + search + hash) 拼凑出来的

  step3: 🥰 push 等方法的 path 还是用了用户传的path值，计算后一直不断去更新history&location，所以🤡中的逻辑是对的。初始化的时候是拼凑path一个出来

* ❓***achor tag 一定是 url 最后的符号，所有在 achor tag 后面的内容都属于hash的一部分***，所以工场路由为 /#/home?page=1 的页面window.location.hash 无法正常获取。（涉及到url解析的规则，由右向左？）

*  search参数不一致问题：BrowserRouter 把 /#/home?page=1 整体当作 hash 去拼接一个新的 path，变向把 search 又保留在 path 里从而到后边解析出 search 返回给 location 对象。（也就解释了为什么 window.location 中获取不到 search 但 useLocation 可以获取到）

* 不支持history API的浏览器使用了BrowserRouter 时，会弹警告并使用 window.location.href 进行跳转

* 如何监听pushState&replaceState：（看图）

* 项目 basename 由`#` 调整为`/#`，是由于 history 库会强制变更，如果项目basename设置为`/miui/#`会被强制改为`#/miui/#`导致路径错误

* 用户输入 `https://datum.xoiaomi.com//#/a` 导致path变为`//`，url解析时会将`//`前半部分视为协议部分，并最终解析为``https:/#/a`` 出现错误，故需要在页面加载时需要将 `//` 利用正则规则替换为 `/`

