# JavaScript.Info

> 文档地址：[传送门](https://javascript.info/)

阅读提示： ⚠️ 注意事项 🤯 个人思考 🌟 重要内容  🌚奇巧淫技

## `Part1`  The JavaScript language

### 简介

#### 历史

[javaScript 历史 传送门](https://github.com/NorthwesternDirector/myGitBook/blob/master/%E5%89%8D%E7%AB%AF%E5%AD%A6%E4%B9%A0/Javascript/JavaScript%2020%E5%B9%B4.md)

#### JavaScript 引擎

> JavaScript 不仅可以在浏览器中执行，而且可以在服务器上执行（实际上可以在任何具有 [JavaScript引擎](https://en.wikipedia.org/wiki/JavaScript_engine) 的设备上执行）

| 浏览器引擎 👉🏻  |  V8  | SpiderMonkey🌟 | Chakra | ChakraCore | Nitro/SquirrelFish |      浏览器内核       |
| :------------: | :--: | :-----------: | :----: | :--------: | :----------------: | :-------------------: |
|     Chrome     |  ✅   |               |        |            |                    |     Webkit->Blink     |
|     Opera      |  ✅   |               |        |            |                    | Presto->Webkit->Blink |
|    Firefox     |      |       ✅       |        |            |                    |         Gecko         |
|       IE       |      |               |   ✅    |            |                    |        Trident        |
| Microsoft Edge |      |               |        |     ✅      |                    |       EdgeHTML        |
|     Safari     |      |               |        |            |         ✅          |        Webkit         |

⚠️：浏览器内核初始分成两部分：渲染引擎 (LayoutEngineer/RenderingEngine) 和 JavaScript 引擎；但近些年习惯直接称渲染引擎为内核， JavaScript 引擎独立。***渲染引擎*** 主要功能是解析 HTML/CSS 进行渲染页面，渲染引擎决定了浏览器如何显示网页的内容以及页面的格式信息；***JavaScript 引擎*** 专门处理 JavaScript 脚本的虚拟机、解释器，用来解释执行js代码

**简版引擎工作流程**

1. 引擎读取（解析）脚本
2. 将脚本转换（编译）为机器语言
3. 运行机器代码，非常快 😌

### 基础

#### 运算符&算数

##### `+` 

> +的隐式转换： 🌟 二元运算符 `+` 是 ***唯一一个*** 以 “如果任何一个操作数是一个字符串，那么另一个也将转换为字符串”  的方式进行操作的运算符；其他算术运算符只对数字工作，并且 ***总是将其操作数转换为数字***

```js
alert( '1' + 2 ); // "12"
alert( 2 + '1' ); // "21"
alert( 2 + 2 + '1' ); // "41" 
alert( 6 - '2' ); // 4, converts '2' to a number
alert( '6' / '2' ); // 3, converts both operands to numbers
```

🌚：当 `+` 作为一元运算符使用时它对数字不起任何作用，但如果操作数不是数字，一元加号会将其转换为数字，就等同于 Number() 🎉

```js
let apples = "2";
let oranges = "3";
// both values converted to numbers before the binary plus
alert( +apples + +oranges ); // 5
alert( Number(apples) + Number(oranges) ); // 5
```

##### `,` 

逗号运算符是最罕见、最不常见的运算符之一。逗号操作符允许我们对几个表达式求值，用逗号分隔它们。将对其中的每一个进行计算，但只返回最后一个的结果👇

```js
let a = (1 + 2, 3 + 4);
alert( a ); // 7 (the result of 3 + 4)
// 举个使用场景的 🌰
for (a = 1, b = 3, c = a * b; a < 10; a++) {
 ...
}
```

### 数据类型

#### Object

...

## `Part3`  Additional articles

### 4 在浏览器中存储数据

#### 4.1 Cookie

>  Cookie 是 HTTP 协议的一部分，由 [RFC 6265](https://tools.ietf.org/html/rfc6265) 规范定义

##### 如何用作身份验证 🌟 

>  服务端通常会利用 HTTP 响应头中的 `Set-Cookie` 字段去种 cookie，随后浏览器在同域下发出请求时会在请求头中的 `cookie` 字段中自动带上（注：如果是 fetch 请求需要在第二参数中设置 `credentials:'include'` 才能在每个请求中自动带上 cookie）服务端种下的 cookie，通常服务端种下的是一个用户 session 标识，用于判断当前登录用户

1. 登陆之后，服务端利用 `Set-Cookie` HTTP-header 去种一个cookie，里边会包含一个独一无二的 session 标识 (session identifier)

   > 例如：Cookie: meta_session=YWE5N2IxZGMtYmIxMy00YzZhLWE4ZDgtM2Q1NzJiMDI3MGM0

2. 下次如果请求是由相同域发起的，浏览器会使用 `Cookie` HTTP-header 通过网络发送 cookie。

3. 所以服务端就知道是谁发起了请求

##### 大小限制

- `encodeURIComponent` 编码后的 `name=value` 对，大小不能超过 4KB

> 小知识 💡
>
> * 不同编码方式字符所占的字节数
>
> ASCII码：1个英文字母（不分大小写）占1个字节，1个中文汉字占2个字节。
>
> UTF-8编码：1个英文字母占1个字节，1个中文（含繁体）占3个字节。中文标点占3个字节，英文标点占1个字节
>
> Unicode编码：1个英文字母占2个字节，1个中文（含繁体）占2个字节。中文标点占2个字节，英文标点占1个字节
>
> * 单位换算
>
> 1 Byte = 8 bit 字节是数据存储的基础单位，比特是信息的最小单位
>
> 1 KB=1024 Byte ... 
>
> KB MB GB TB PB EB ZB YB

- 每个域的 cookie 总数不得超过 20+ 左右，具体限制取决于浏览器

##### document.cookie

可以对 cookie 进行读写操作

```js
document.cookie = "user=John"; // 写：只会更新名称为 user 的 cookie
alert(document.cookie); // 读：展示所有 cookie，如果设置了httpOnly属性，则此方法可能无法获取 cookie
```

##### 配置项

```js
// 🌰
document.cookie = "user=John; path=/; expires=Tue, 19 Jan 2038 03:14:07 GMT; secure; samesite=lax"
```

* path

  > 设置可以访问该 cookie 的路径。必须是绝对路径，默认为当前路径。通常我们应该将 `path` 设置为根目录：`path=/`，以使 cookie 对此网站的所有页面可见

* domain

  > 设置可以访问该  cookie 的域

  ```js
  // ⚠️注意：如果不设置domain参数，一级域名相同，二级域名不同也属于不同域，故无法访问cookie，
  // 解决方式就是将domain设置为同一个一级域名
  // 在 site.com
  document.cookie = "user=John"
  // 在 forum.site.com
  alert(document.cookie); // 没有 user
  ```

* expires、max-age

  > 设置 cookie 的有效期。
  >
  > 默认情况下， cookie 没有设置这两个参数中的任何一个，那么在关闭浏览器之后 cookie 就会消失

  ```js
  expires = "Tue, 19 Jan 2038 03:14:07 GMT" //日期必须完全采用 GMT 时区的格式
  max-age = 3600 // 过期时间距离当前时间的秒数
  ```

* secure

  > 设置 cookie 只能被通过 HTTPS 传输
  >
  > cookie 是基于域的，不区分协议：**默认情况下，如果我们在 `http://site.com` 上设置了 cookie，那么该 cookie 也会出现在 `https://site.com` 上**

* samesite

  > 防止 XSRF（跨站请求伪造）攻击

* httpOnly

  > 设置禁止任何 JavaScript 访问 cookie。服务端在响应头中的 Set-Cookie 可以进行设置，前端使用 `document.cookie` 看不到此类 cookie，也无法对此类 cookie 进行操作