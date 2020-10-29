# 1995-2015 

阅读提示： ⚠️ 注意事项    🤯 个人思考   🌟 重要内容   

## 语言诞生

> 最初，JavaScript 被认为是 Java 的辅助脚本语言，用于编写脚本来动态地修改 Java 对象的属性和行为，作为 Java 的补充，方便进行在线应用开发，其中所有复杂的编程任务都将使用 Java 来完成。但是随着对 JavaScript 的熟悉，Web 开发者们开始意识到他们其实只要有 JavaScript 就够了🤔...

### 时间线

「时间线参考图例：👱🏻‍♂️人物  🏠 公司   🕓 时间   🦊 实验版本  🏖伴随事件」

* 👱🏻‍♂️Brendan Eich  🕓1995-05  🦊 *Mocha*  十天大作  🏖 Java发布

> *Mocha*这个 Demo 包括语言的最小实现，并最小化地集成到了 Netscape 2 浏览器的 pre-alpha 版本中。“ 这种 Java 的辅助语言必须「看起来像 Java」，保持易用性并「基于对象」，而不是像 Java 这样基于类 ”

* 🏠 Netscape & Sun  🕓1995-12-04   🦊LiveScript ( JavaScript 1.0 )   🏖 Netscape Navigator 2.0发布

> JavaScript 被描述为一种对象脚本语言，可用于编写脚本来动态地修改 Java 对象的属性和行为，作为Java 的补充方便进行在线应用开发。JavaScript 1.0 是一种简单的 *动态类型* 语言，它支持数字、字符串与布尔值、一等公民函数，以及对象数据类型。Brendan Eich在这个夏天将 Mocha 更全面地集成到浏览器中，这需要设计实现使 Mocha 程序能与网页交互的 API。

* 👱🏻‍♂️Brendan Eich  🕓1996-08  🦊 JavaScript 1.1  🏖 Netscape Navigator 3.0发布

> Brendan Eich 继续开发还不够完整或缺失的特性，此版本发布后，JavaScript 的初始定义和开发才算完成

* 👱🏻‍♂️Brendan Eich & Chris Houck  🕓1996-12  🦊 JavaScript 1.2 ( SpiderMonkey )

> Brendan Eich 重新设计实现了 JavaScript 引擎的核心并命名为 SpiderMonkey，它是一个更快、更可靠和更灵活的运行引擎，舍弃了将 JavaScript 值表示为差异联合体[g](https://cn.history.js.org/appendices.html#discriminated-union)的实践，改为使用包含即时原始值的标记指针（tagged pointer）；还实现了诸如嵌套函数、函数表达式和 switch 语句之类的特性，这些特性从未在原始引擎中实现过；基于引用计数的内存管理器也被替换成了基于标记 / 清除算法的垃圾收集器。

### JavaScript 1.0 与1.1

#### 特性

##### 对象继承

JavaScript 1.0：没有对象继承的概念，都使用 this. 来将所有属性添加到每个新对象上

JavaScript 1.1：通过函数对象名为 `prototype` 的属性，将原型对象与构造函数关联起来（***将通用的行为与一组对象相关联***）；原型是一种特殊的对象，其自身属性与所有「由构造函数创建的对象」所共享

⚠️：两者的不同之处在于，方法仅在原型对象上挂载了一次，而不是在构造每个实例对象时重复挂载。由原型对象提供给某个对象的属性称为继承属性，而直接在对象上定义的属性则称为自有属性，自有属性会遮盖同名的继承属性 🌟

```js
// 定义出作为方法被使用的函数
function ptSum(pt2) {
  return new Point(this.x + pt2.x, this.y + pt2.y);
}
function ptDistance(pt2) {
  return Math.sqrt(Math.pow(pt2.x - this.x, 2) + Math.pow(pt2.y - this.y, 2));
}
// 定义 Point 构造函数
function Point(x, y) {
  // 创建并初始化新对象的数据属性
  this.x = x;
  this.y = y;

  // 为每个对象实例添加方法
  this.sum = ptSum;
  this.distance = ptDistance;
}
var origin = new Point(0, 0); // 创建 Point 对象
```

```js
// 定义出作为方法被使用的函数
function ptSum(pt2) {
  return new Point(this.x + pt2.x, this.y + pt2.y);
}
function ptDistance(pt2) {
  return Math.sqrt(Math.pow(pt2.x - this.x, 2) + Math.pow(pt2.y - this.y, 2));
}
// 定义 Point 构造函数
function Point(x, y) {
  // 创建并初始化新对象的数据属性
  this.x = x;
  this.y = y;
}
// 添加方法到共享的原型对象
Point.prototype.sum = ptSum;
Point.prototype.distance = ptDistance;

var origin = new Point(0, 0); // 创建 Point 对象
```

##### 内置库

>  JavaScript设计的原因，JavaScript 被描述为「一种对象脚本语言」，可用于编写脚本来动态地「修改 Java 对象的属性和行为」。它将作为「Java 的补充，方便进行在线应用开发」。在 Netscape Navigator（网景浏览器） 中，宿主对象提供的模型表达了当前 HTML 文档的一部分，这些 API 最终被称为级别 0 的文档对象模型（DOM）

  🤯：通过区分 JavaScript 内置库与宿主提供的对象（宿主包括浏览器、服务端等），JavaScript之后也有了ECMAScript、BOM、DOM的划分

##### 迷惑行为

> JavaScript 有一些令人感到奇特或意外的特性。它们之中有些是故意为之，有些则是在最初的 Mocha 10 天冲刺期间做出的快速设计决策的产物。JavaScript 1.0 也有 bug 和未完成的半成品特性

#### 细节

##### JS语句不使用分号结尾的来源

> 如果每条语句之间用分号分隔，则可能在一行上出现多条语句 

不使用分号就可以编写 JavaScript 代码的特性，称为自动分号插入（ASI）

### JavaScript1.2

> JavaScript此次升级，借鉴了 Python、C语言、Perl 、Java 等流行语言的特性，增加&完善了一些诸如数组操作、字符串操作、循环语句等特性 ，所以语言中都有其它语言的影子🌟

#### 特性

#####   __proto__属性

 `__proto__` 伪属性（pesudo-property），这个伪属性使 JavaScript 程序能动态访问并修改每个对象（用来访问继承属性）的内部引用，通过使用 `__proto__`，程序可以动态构造任意深度的属性继承层次结构，并动态指定对象该从何处继承属性。🌟

## 创立标准

### Ecma

* Netscape 和 Sun 计划向万维网联盟（W3C）和互联网工程任务组（IETF）提议，将 JavaScript 作为开放的互联网脚本语言标准，但他们都不感兴趣 🤷‍♂️

* 接盘侠Ecma：Ecma国际（Ecma International）是一家国际性会员制度的信息和电信标准组织

  国际标准组织（ISO）认可 Ecma 国际，Ecma 的标准可以通过快速通道来成为 ISO 标准
  
* ECMA-262：ECMAScript规范的 Ecma 文档编号

> 备注：1994年之前，ECMA代表欧洲计算机制造商协会（European Computer Manufacturers Association），但因为计算机的国际化，组织的标准牵涉到很多其他国家，因此组织决定改名表明其国际性。现名称已不属于首字母缩略字

###  TC39  

TC39：39号技术委员会（Ecma 使用数字来标记旗下的技术委员会）

### 命名标准

* Netscape 最初命名为 ***LiveScript***，但基于它和 Sun 的战略合作伙伴关系而替换成了 ***JavaScript***

* 在第一次 TC39 会议上，由于商标控制权的问题，先使用了***ECMAScript*** 作为占位名称

* 一段时间内，***ECMAScript*** 仍在规范草案中使用

* 1997年9月，TC39 同意使用 ***ECMAScript*** 作为语言名称来发布标准

* 但结合它的使用情景，全世界一直在使用 ***JavaScript*** 名称来标识这门由浏览器实现的语言

  > 这种语言的任何实现都不太可能被称为 ECMAScript。这在现在和将来都会使用户感到困惑。困惑之处包括标准的含义，以及语言引擎对标准的支持

### ES 3

* 1997 年 9 月 16 日，《ECMA-262 第 1 版》在 TC39 会议上正式发布
* 1998 年 7 月，即将出镜的修订版规范发布到了 ISO/IEC，并寄给了各 Ecma 标准会员。后者批准了该修订规范，是为《ECMA-262 第 2 版》
* 1999 年 12 月 16 日，Ecma GA 大会批准了《ECMA-262 第 3 版》规范

### Web 1.0

早期期的交互式 Web 应用主要是基于表单的：用户会将数据输入 HTML 表单，然后由浏览器传输回 Web 服务器，在服务端处理数据并更新数据库，最后将更新的 HTML 文稿传输回浏览器显示。JavaScript 在浏览器端用于基本的输入数据验证，以及对服务端生成的 HTML 做简单的动态更改；这种 Web 应用的形式后来被表述为 Web 1.0。

### AJAX

> Asynchronous Javascript And XML

* 历史发展：微软在Outlook Web Access 的 Web 1.0 风格实现后的新版本中，使用了动态 HTML（使用 JavaScript 来动态修改活动网页中的 HTML 元素）和一个名为 XMLHTTP的新浏览器 API（就是现在我们熟知的 ***XMLHttpRequest()*** ）。XMLHTTP 使得网页上的 JavaScript 代码能与服务端来回异步传输数据，而无需完全重新加载网页。DHTML 和 XMLHTTP 的组合，使得 Web 页面在每个会话中只需加载一次，然后即可作为支持远程访问数据和服务的交互式应用而运行。
* Jesse James Garrett👱🏻‍♂️ 创造了 AJAX 一词来形容这种 Web 应用风格

### Web 2.0

* Web 2.0是一种 Web 应用风格它关注用户原创内容（UGC），具备高度的可交互性，并使用 AJAX 技术构建。 AJAX 和使用它构建的社交媒体应用，成为了 Web 2.0 时代的标志。Web 2.0 和 AJAX 的出现，是 JavaScript 在 Web 开发中用途的主要转折点。JavaScript 的角色逐渐由一门用来将动态元素添加到静态页面的语言，变为了一门用来对复杂的富互联网应用（RIA）进行编码的语言。

* 在 AJAX 应用复杂性与浏览器兼容问题的背景下，用于简化 Web 应用构建的框架和库应运而生。早期的框架包括 Prototype 、MooTools  和 Dojo，而其中最受欢迎的是 jQuery 。这些早期的框架与库通常为 AJAX 应用提供了基础结构支撑，并为简化编码实现常见任务提供了高层面的抽象。它们还通过内部处理和隐藏许多浏览器特性变体的方式，解决了许多兼容问题

### 浏览器博弈论

矛盾：Web 及其开放标准基础的兼容要求 <=> 浏览器开发者在单平台创新进行竞争的能力

- 破坏性变更（甚至 bug 修复）可能赶走用户
- 新浏览器必须遵从于现有的浏览器
- 如果仅在一个浏览器中进行创新，那将是浪费
- 第一个吃螃蟹的浏览器，可能反而会丢失市场份额

🤯：浏览器会在实现的质量（如性能、安全性、可靠性和可用性）上进行竞争，但要想提高浏览器作为应用平台的基本技术能力，通常需要所有主流浏览器之间的合作

##  改革失败

### TC39-TG1

2000年微软向 Ecma 建议扩展 TC39 的职责范围，并在其中将 .NET 标准化，故TC39 被重新定义为 Ecma 面向「编程环境」的技术委员会，正在进行的 ECMAScript 开发活动在 TC39 中被降级到了 TG 任务组（Task Group）状态，称为 TC39-TG1

### CommonJS 和 Node.js

通用的、领域独立的、可互操作的非浏览器 JavaScript 应用环境 -> CommonJS 模块规范 -> CommonJS 模块的早期使用者之一 Node.js (最早被设想为一种用于构建服务端应用的技术) ->推动 JavaScript 发展

## 继往开来

### ECMAScript 规范分类

- 一个 ECMAScript 虚拟机，包括各种运行时实体及其语义
- ECMAScript 语言的语法、语义，及其与虚拟机之间的映射
- 所有 ECMAScript 程序都可以使用的各种标准库对象

