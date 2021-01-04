# JavaScript Info

> 文档地址：[传送门](https://javascript.info/)

阅读提示： ⚠️ 注意事项 🤯 个人思考 🌟 重要内容  🌚奇巧淫技

## 简介

### 历史

[javaScript 历史 传送门](https://github.com/NorthwesternDirector/myGitBook/blob/master/%E5%89%8D%E7%AB%AF%E5%AD%A6%E4%B9%A0/Javascript/JavaScript%2020%E5%B9%B4.md)

### JavaScript 引擎

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

#### 简版引擎工作流程

1. 引擎读取（解析）脚本
2. 将脚本转换（编译）为机器语言
3. 运行机器代码，非常快 😌

## 基础

### 运算符&算数

####  `+` 的隐式转换

🌟 二元运算符 `+` 是 ***唯一一个*** 以 “如果任何一个操作数是一个字符串，那么另一个也将转换为字符串”  的方式进行操作的运算符；其他算术运算符只对数字工作，并且 ***总是将其操作数转换为数字***

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

#### `,` 

逗号运算符是最罕见、最不常见的运算符之一。逗号操作符允许我们对几个表达式求值，用逗号分隔它们。将对其中的每一个进行计算，但只返回最后一个的结果👇

```js
let a = (1 + 2, 3 + 4);
alert( a ); // 7 (the result of 3 + 4)
// 举个使用场景的 🌰
for (a = 1, b = 3, c = a * b; a < 10; a++) {
 ...
}
```

## 数据类型

### Object

