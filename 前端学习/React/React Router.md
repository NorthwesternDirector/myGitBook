###   React-router 🎉 `v5`

官方参考 ： [quick start]( https://reactrouter.com/web/guides/quick-start)

- React Router 核心库
  -  绑定 web 端的 `react-router-dom`
  -  绑定 React Native 的 `react-router-native`
  -  集成 Redux 的 `react-router-redux`
  -  配置静态路由的 `react-router-config`

Q：为什么 React-Router 不和 React 封装在一起？？？

！！！！ Prompt。Route 的参数～～～

#### 1. 前置知识

* GET 

```js
request(`/api/meta/adhoc/exec/stop?id=${id}`) //query param, post也可传
request(`/api/meta/adhoc/history/${queryId}`) //url，useParams只能获取此形式下的参数
```

* POST 

```react
request('/api/meta/dataImport/deleteById', { method: 'POST', data: param }, true) //request body
```

* 相对路径规则

`./` 当前目录 `../` 父级目录 `/` 根目录

#### 2. HOOKS

##### useParams

`useParams` 返回一个包含了 URL 参数键值对的对象，功能类似 `<Route>` 的 `match.params`

注意⚠️：只有 path: '/a/b/:c/:d' 中的 '':key" 才能被 `useParams` 匹配到。useParams 返回的是{a:value, b:value}

```js

```



##### useLocation

 `useLocation` 返回代表当前 URL 的 location 对象，功能类似 `useState`，当URL改变时总会返回一个新的 location 对象。useLocation 可以视为简版的 Location API， Location API 数据&方法更加全面

*以  localhost:3000/#/home/indexdetail?id=1 为例 👇：*

|              | useLocation (HashRouter下) | Location                                        | 备注                 |
| ------------ | -------------------------- | ----------------------------------------------- | -------------------- |
| hash         | 🌟 ""                       | "#/home/indexdetail?id=1"                       |                      |
| pathname     | "/home/indexdetail"        | "/"                                             |                      |
| search       | "?id=1"                    | 🌟 ""                                            |                      |
| state❓       | undefined                  | -                                               | 支持跳转携带一些状态 |
| host         | -                          | "localhost:3000"                                |                      |
| hostname     | -                          | "localhost"                                     |                      |
| href         | -                          | "http://localhost:3000/#/home/indexdetail?id=1" |                      |
| origin       | -                          | "http://localhost:3000"                         |                      |
| port         | -                          | "3000"                                          |                      |
| protocol     | -                          | "http:"                                         |                      |
| assign ( )   | -                          | ✅                                               |                      |
| reload ( )   | -                          | ✅                                               |                      |
| replace ( )  | -                          | ✅                                               |                      |
| toString ( ) | -                          | ✅                                               |                      |
| valueOf ( )  | -                          | ✅                                               |                      |

##### useHistory

`useHistory` 可以用来访问 hidtory 实例，从而进行导航

<img src="https://github.com/NorthwesternDirector/myGitBook/blob/master/%E5%85%B6%E4%BB%96/%E5%BC%95%E5%85%A5%E5%9B%BE%E7%89%87/react/redux.PNG?raw=true"/>

```jsx
import {useHistory} from 'react-router-dom'
...
const history = useHistory()
history.push('url')//进行页面跳转
```

#### 3. 若干组件👇

##### Router --- 容器

`routes 属性` 将路由规则传入Router容器

`history 属性 ` hashHistory/browserHistory([History API](https://developer.mozilla.org/zh-CN/docs/Web/API/History)) 路由的切换由URL的 hash/URL 变化决定

History API:  *.back() . forword() .go() .pushState()*

```jsx
<Router routes={routes} history={browserHistory}/>
```

(1) `<BrowserRouter>` 浏览器的路由组件

(2) `<HashRouter>` URL格式为Hash路由组件

​	？<Switch>

(3)`<MemoryRouter>` 内存路由组件

(4)`<NativeRouter>` Native的路由组件

(5)`<StaticRouter>` 地址不改变的静态路由组件

##### Route --- 容器中的具体规则（定义URL路径与组件的对应关系）👇

```jsx
<Router history={hashHistory}>
  <Route path="/" component={App}/>
  <Route path="/repos(/:name)" component={Repos}/>
  <Route path="/about/:a" component={About}/>
  <Route path="/think/*" component={Think}/>
</Router>
```

注意 ***path通配符*** 的使用👇

**（1）`:paramName`**

`:paramName` 匹配URL的一个部分，直到遇到下一个`/`、`?`、`#`为止。这个路径参数可以通过`this.props.params.paramName`取出。

**（2）`()`**

`()`表示URL的这个部分是可选的。

**（3）`\*`**

`* `匹配任意字符，直到模式里面的下一个字符为止。匹配方式是非贪婪模式。

**（4） `\**`**

`**` 匹配任意字符，直到下一个`/`、`?`、`#`为止。匹配方式是贪婪模式

##### IndexRoute 组件 

指定默认情况下加载的子组件

```jsx
<Router>
  <Route path="/" component={App}>
    <IndexRoute component={Home}/> {/*显式指定Home是根路由的子组件，即指定默认情况下加载的子组件*/}
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

##### Redirect 组件

用于路由的跳转，用户访问一个路由后会自动跳转到另一个路由

```jsx
<Route path="inbox" component={Inbox}>
  {/* 从 /inbox/messages/:id 跳转到 /messages/:id。访问/inbox/messages/5，会自动跳转到/messages/5 */}
  ＜Redirect from="messages/:id" to="/messages/:id" />
</Route>
```

##### IndexRedirect 组件

用于访问 *根路由* 的时候，将用户重定向到某个子组件

```jsx
<Route path="/" component={App}>
  ＜IndexRedirect to="/welcome" />
  <Route path="welcome" component={Welcome} />
  <Route path="about" component={About} />
</Route>
```

##### Link

`<a> `元素的React 版本，可以接收 Router 的状态

##### Switch

只渲染**第一个匹配**到的路由组件或重定向组件

```react
<Switch>
  <Route exact path="/" component={Home}/>
  <Route path="/about" component={About}/>
  <Route path="/:user" component={User}/>
  <Route component={NoMatch}/>
</Switch>
//当访问地址为 localhost:3000/about 时，此路由配置如果不使用Switch组件，将会渲染<About><User><NoMatch>三个组件
```

##### Redirect

重定向到页面

```react
<Redirect key={key} from={path} to="/exception/403" />
```

* to<string>：链接到的路径名或位置

* push<bool>：当为true时，重定向会将新条目推入历史记录，而不是替换当前条目

* from<string>：要重定向的路径名。 这可以用于在<Switch>内部渲染<Redirect>时匹配位置。

##### 路由钩子

ing。。。

##### 组件接收props

所有路由中指定的组件将被传入以下三个 props 

- match

  `match` 对象包含了 `<Route path>` 如何与URL匹配的信息。`match` 对象包含以下属性：

  - `params` -（ object 类型）即路径参数，通过解析URL中动态的部分获得的键值对
  - `isExact` - 当为 `true` 时，整个URL都需要匹配
  - `path` -（ string 类型）用来做匹配的路径格式。在需要嵌套 `<Route>` 的时候用到
  - `url` -（ string 类型）URL匹配的部分，在需要嵌套 `<Link>` 的时候会用到

- location

  Location 是指你当前的位置(结构类似👇)

  ```json
  {
    key: 'ac3df4', // 在使用 hashHistory 时，没有 key
    pathname: '/somewhere'
    search: '?some=search-string',
    hash: '#howdy',
    state: {
      [userDefined]: true
    }
  }
  ```

- history

  * 「browser history」 - history 在 DOM 上的实现，经常使用于支持 HTML5 history API 的浏览器端。

  * 「hash history」 - history 在 DOM 上的实现，经常使用于旧版本浏览器端。

  * 「memory history」 - 一种存储于内存的 history 实现，经常用于测试或是非 DOM 环境（例如 React Native）

  * `length` -（ number 类型）指的是 history 堆栈的数量。

    `action` -（ string 类型）指的是当前的动作（action），例如 `PUSH`，`REPLACE` 以及 `POP` 。

    `location` -（ object类型）是指当前的位置（location），location 会具有如下属性：

    - `pathname` -（ string 类型）URL路径。
    - `search` -（ string 类型）URL中的查询字符串（query string）。
    - `hash` -（ string 类型）URL的 hash 分段。
    - `state` -（ string 类型）是指 location 中的状态，例如在 `push(path, state)` 时，state会描述什么时候 location 被放置到堆栈中等信息。这个 state 只会出现在 browser history 和 memory history 的环境里。

    `push(path, [state])` -（ function 类型）在 hisotry 堆栈顶加入一个新的条目。

    `replace(path, [state])` -（ function 类型）替换在 history 堆栈中的当前条目。

    `go(n)` -（ function 类型）将 history 对战中的指针向前移动 `n` 。

    `goBack()` -（ function 类型）等同于 `go(-1)` 。

    `goForward()` -（ function 类型）等同于 `go(1)` 。

    `block(prompt)` -（ function 类型）阻止跳转，（请参照 [history 文档](https://github.com/ReactTraining/history#blocking-transitions)）

#### 4. 响应式路由

理解：响应手机、平板横向/纵向切换导致屏幕大小变换，从而动态改变路由界面布局。

#### 5. 静态路由

静态路由是在应用运行之前就固定好了路由结构，可以使用 [react-router-config ](https://link.zhihu.com/?target=https%3A//github.com/ReactTraining/react-router/tree/master/packages/react-router-config) 这个库来静态配置路由

##### 2.1 `react-router-config` API

- `renderRoutes(routes, extraProps = {}, switchProps = {})`

  这个ApI把路由组件渲染出来，routes 即是配置的可嵌套路由配置对象

- `matchRoutes(routes, pathname)`

  这个API返回被匹配的路由，使用它可以 在应用渲染路由匹配的组件之前 做一些操作