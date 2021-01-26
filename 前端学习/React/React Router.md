# React-router 🎉 `v5`

一句话解释：***通过指定方法监听路由的变化，并利用监听到的变化值去匹配渲染不同的组件***

官方参考 ： [quick start 传送门]( https://reactrouter.com/web/guides/quick-start)

- React Router 核心库
  -  绑定 web 端的 `react-router-dom`
  -  绑定 React Native 的 `react-router-native`
  -  集成 Redux 的 `react-router-redux`
  -  配置静态路由的 `react-router-config`

Q：为什么 React-Router 不和 React 封装在一起

🤔：为了支持多端考虑的，不同终端的 React-Router 实现原理不同

## 常用API

### HOOKS

> 三个 Hook 几乎覆盖了下文提到的旧版本三兄弟: match，location，history 的所有功能

#### useParams (match)

`useParams` 返回一个包含了 URL 参数键值对的对象，功能类似 `<Route>` 的 `match.params`

注意⚠️：只有 path: '/a/b/:c/:d' 中的 '':key" 才能被 `useParams` 匹配到。useParams 返回的是{a:value, b:value}

 **match 对象**
  `match` 对象包含了 `<Route path>` 如何与URL匹配的信息。`match` 对象包含以下属性：

  - `params` -（ object 类型）即路径参数，通过解析URL中动态的部分获得的键值对（⚠️useParams只返回这个参数）
  - `isExact` - 当为 `true` 时，整个URL都需要匹配
  - `path` -（ string 类型）用来做匹配的路径格式。在需要嵌套 `<Route>` 的时候用到
  - `url` -（ string 类型）URL匹配的部分，在需要嵌套 `<Link>` 的时候会用到

#### useLocation (location)

 `useLocation` 返回代表当前 URL 的 location 对象，功能类似 `useState`，当URL改变时总会返回一个新的 location 对象。useLocation 可以视为简版的 Location API， Location API 数据&方法更加全面

*以  localhost:3000/#/home/indexdetail?id=1 为例 👇：*（HashRouter&BrowserRouter 下表现一致）

| 参数名       | useLocation         | Location                                        | 备注                 |
| ------------ | ------------------- | ----------------------------------------------- | -------------------- |
| hash         | 🌟 ""                | "#/home/indexdetail?id=1"                       |                      |
| pathname     | "/home/indexdetail" | "/"                                             |                      |
| search       | "?id=1"             | 🌟 ""                                            |                      |
| state        | undefined           | 🚫                                               | 支持跳转携带一些状态 |
| host         | 🚫                   | "localhost:3000"                                |                      |
| hostname     | 🚫                   | "localhost"                                     |                      |
| href         | 🚫                   | "http://localhost:3000/#/home/indexdetail?id=1" |                      |
| origin       | 🚫                   | "http://localhost:3000"                         |                      |
| port         | 🚫                   | "3000"                                          |                      |
| protocol     | 🚫                   | "http:"                                         |                      |
| assign ( )   | 🚫                   | ✅                                               |                      |
| reload ( )   | 🚫                   | ✅                                               |                      |
| replace ( )  | 🚫                   | ✅                                               |                      |
| toString ( ) | 🚫                   | ✅                                               |                      |
| valueOf ( )  | 🚫                   | ✅                                               |                      |

思考🤔：useLocation() 生成的 location 对象（是 context 中存储的 history 库生成的 history 对象中存放的 location 对象）和 window.location 字段值不同的缘由阅读 history 库源码发现是：path 在 `stripBasename(path,basename) ` 方法中👇，为除去 path 中已有的 basename('/#') 部分，会误把 /#/a/b?k=1 处理为 /a/b?k=1，原本的 hash 部分被识别为 pathname 部分，且 search 部分误打误撞也被保留了下来（ window.location 中 serach 无法被识别是因为#后都会被记为 hash 路由部分，正常情况是 serach 部分应该在 hash 部分之前）

```js
function hasBasename(path, prefix) {
  return path.toLowerCase().indexOf(prefix.toLowerCase()) === 0 && '/?#'.indexOf(path.charAt(prefix.length)) !== -1;
}
function stripBasename(path, prefix) {
  return hasBasename(path, prefix) ? path.substr(prefix.length) : path;
}
```

#### useHistory (history)

`useHistory` 可以用来访问 hidtory 实例从而进行导航

与window.history进行对比👇：

| 参数名                     | useHistory                | History | 备注                              |
| -------------------------- | ------------------------- | ------- | --------------------------------- |
| action                     | -                         | 🚫       | 当前动作 (`PUSH` `REPLACE` `POP`) |
| length                     | -                         | -       | history 堆栈中的条目数量          |
| block()                    | -                         | 🚫       | 阻止跳转                          |
| createHref()               | -                         | 🚫       |                                   |
| go()                       | -                         | -       | 在 history 堆栈中跳跃             |
| goBack() / back()          | -                         | -       | 等同于 `go(-1)`                   |
| goForward() / forward()    | -                         | -       | 等同于 `go(1)`                    |
| listen()                   | -                         | 🚫       |                                   |
| location                   | 同 useLocation 中的返回值 | 🚫       |                                   |
| push() / pushState()       | -                         | -       | 在 hisotry 堆栈顶加入一个新的条目 |
| replace() / replaceState() | -                         | -       | 替换在 history 堆栈中的当前条目   |
| scrollRestoration          | 🚫                         | -       |                                   |
| state                      | 🚫                         | -       |                                   |

```jsx
import {useHistory} from 'react-router-dom'
...
const history = useHistory()
history.push('url')//进行页面跳转
```

### Router (容器)

> 所有路由器组件的通用底层接口（[官方]和状态管理库深度集成时会使用得到 🤔）

```jsx
...
//[官方] import { createBrowserHistory } from "history";
//[官方] const history = createBrowserHistory();
const history = useHistory()//不确定这个 history 对象好使不
...
<Router history={history}/>
```

**参数**

* `history  `  需传入用于导航的 history 对象

**注意**

通常都是使用较为高级的 <BrowserRouter>、<HashRouter>、<MemoryRouter>、<NativeRouter>、<StaticRouter> 中的一个作为路由容器使用

### Route (规则) 🌟

>  定义URL路径与组件的对应关系，从而呈现出不同的UI界面

#### 三种渲染方式

> 提供多种渲染方式主要是为了支持在引入Hook之前，使用老版本开发的应用程序

<Route component>、<Route render>、<Route children> 这三种方式都会给渲染组件注入 match/location/history (后文统称为三兄弟）三个参数（举例🌰👇），参数用法见：[传送门]()

```jsx
//基础用法，没得三兄弟
<Router>  
  <Route exact path="/">
    <Home />
  </Route>     
</Router>

//1. component
// 🤔：props中已被注入三兄弟，有了Hook：不用组件层层传递三兄弟，随时随地useXXX就行
// ❓：component是一个属性，每次渲染都会执行component中的内容（即重新构建组件）
function User(props) { 
  return <h1>Hello {props.match.params.username}!</h1>;
}
<Router>
  <Route path="/" component={User}/>
</Router>

//2. render
//🤔：你看，是不是要层层传递三兄弟。此方法避免了1中的疑问，匹配后再调用（构建）
<Router>
	<Route render={routeProps => (<Component {...routeProps} />)} />
</Router>

//3. children
// 用法和render一样区别是：render是匹配后调用，children直接调用不管匹没匹配到
<Router>
	<Route children={routeProps => (<Component {...routeProps} />)} />
</Router>
```

**参数**

* `path `

  > 如果为空，当前Route总是匹配的

  注意 ***path通配符*** 的使用👇

  **（1）`:paramName`**：`:paramName` 匹配URL的一个部分，直到遇到下一个`/`、`?`、`#`为止。这个参数可以通过三兄弟里的 `match.params.paramName` 取出

  **（2）`()`**：`()`表示URL的这个部分是可选的。

  **（3）`\*`**：`* `匹配任意字符，直到模式里面的下一个字符为止。匹配方式是非贪婪模式。

  **（4） `\**`**：`**` 匹配任意字符，直到下一个`/`、`?`、`#`为止。匹配方式是贪婪模式

* `exact`

  > 值为 true时，需要精准匹配 location.pathname

* `strict`

  > 值为 true时，url末尾的反斜杠需要精准匹配

* `sensitive`

  > 值为 true时，url匹配对大小写敏感

### Switch

> 只渲染**第一个匹配**到的<Route> 或者 <Redirect> 

```react
//当访问地址为 localhost:3000/about 时，此路由配置如果不使用Switch组件，将会渲染<About><User><NoMatch>三个组件
// 🤔：根据React-Router的设计，不使用switch，可以利用一个路由支持匹配多个组件的原理，去匹配一个页面的多个子组件
<Router>
  <Switch>
    <Route exact path="/" component={Home}/>
    <Route path="/about" component={About}/>
    <Route path="/:user" component={User}/>
    <Route component={NoMatch}/>
  </Switch>
<Router/>
```

### Redirect

> 用于导航至新路由，用户访问一个路由后会自动跳转到另一个路由。

⚠️注意：导航至新路由 history 栈中是覆盖替换不是新增

```jsx
<Redirect from="/old-path" to="/new-path" />
```

**传参**

* `to`(String/Object)：链接到的路径名或位置

  当to值为一个对象时👇，其中的 state 值会在新路由中通过 location.state 获得

  ```jsx
  <Redirect
    to={{
      pathname: "/login",
      search: "?utm=your+face",
      state: { referrer: currentLocation }
    }}
  />
  ```

* `push` ：当为true时，会将新路由器推入history 栈，而不是覆盖替换

* `from` ：要重定向的路径名。 这可以用于在<Switch>内部渲染<Redirect>时匹配位置

* `exact`

* `strict`

* `sensitive`

### Link

> `<a>  `元素的React 版本，可以接收 Router 的状态

**传参**

* `to ` (String/Object/Function)：链接到的路径名或位置；与Redirect类似，值可以为一个对象；值还可以为一个函数，返回一个字符串或对象
* `replace`：值为 true 时，会在 history 栈中替换当前记录而不是新增
* ...

### Prompt

> 用于在离开页面之前提示用户

* when：是否开启页面离开提示
* message：<string>离开提示信息，<func>return true 直接跳转，return string 显示提示信息

```jsx
<Prompt
  when={!locked} 
  message={(location, action) => {
    if (action === 'POP') {
      console.log("Backing up...")
    }
    return location.pathname.startsWith("/app")
      ? true
      : `Are you sure you want to go to ${location.pathname}?`
  }}
/>
```

### HashRouter 🌟

*说在前面：react开发的是单页面应用程序，保持UI与URL的同步在react-router内有两种实现方式分别是 HashRouter&BrowserRouter*

> 属于 Router 的一种，使用URL的哈希部分（window.location.hash）来保持UI与URL同步
>
> 实现原理：window.onhashchange

***hashRouter的存在是为了支持旧版浏览器不支持 HTML5 history API 的情况，推荐使用 BrowserRouter***

* basename<string>：所有URL的基础值，⚠️以 / 开头但不以 / 结尾

  ```jsx
  <HashRouter basename="/calendar"/>
  <Link to="/today"/> // renders <a href="#/calendar/today">
  ```

### BrowserRouter 🌟

> 属于 Router 的一种，使用 HTML5 的 history API 来保持UI与URL同步
>
> 实现原理：HTML5 history API 中的 popstate 事件

### IndexRoute 

> 指定默认情况下加载的子组件

```jsx
<Router>
  <Route path="/" component={App}>
    <IndexRoute component={Home}/> {/*显式指定Home是根路由的子组件，即指定默认情况下加载的子组件*/}
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

### IndexRedirect 

> 用于访问 *根路由* 的时候，将用户重定向到某个子组件

```jsx
<Route path="/" component={App}>
  ＜IndexRedirect to="/welcome" />
  <Route path="welcome" component={Welcome} />
  <Route path="about" component={About} />
</Route>
```


