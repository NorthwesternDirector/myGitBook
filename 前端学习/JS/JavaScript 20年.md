# 1995-2015 

阅读提示： ⚠️ 注意事项    🤯 个人思考   🌟 重要内容   

## 语言诞生

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

> 备注：1994年之前，ECMA代表欧洲计算机制造商协会（European Computer Manufacturers Association），但因为计算机的国际化，组织的标准牵涉到很多其他国家，因此组织决定改名表明其国际性。现名称已不属于首字母缩略字

###  TC39  

TC39：39号技术委员会（Ecma 使用数字来标记旗下的技术委员会）

##  改革失败

## 继往开来

