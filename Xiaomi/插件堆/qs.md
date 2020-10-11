

### qs

> 一个具有附加安全性的字符串解析和字符串化库。

​	[github地址&使用说明](https://github.com/ljharb/qs)

#### 数据工厂常用

##### 1. 解析url参数（字符串->对象）

```js
//qs.parse(string, [options]);
var prefixed = qs.parse('?a=b&c=d', { ignoreQueryPrefix: true })//{ a: 'b', c: 'd' }ignoreQueryPrefix:忽略问号
//qs.stringify(object, [options]);
var prefixed = qs.stringify({ a: 'b' }) //'a=b'
```

##### 2. qs.parse(string, [options]) 

###### 2.1 Parsing Objects

string类型：

(1) []

```js
assert.deepEqual(qs.parse('foo[bar]=baz'), {
    foo: {
        bar: 'baz'
    }
});
```

(2)URI encoded strings 

```jsx
assert.deepEqual(qs.parse('a%5Bb%5D=c'), {
    a: { b: 'c' }
});
```

(3)任意分隔符

```jsx
var prefixed = qs.parse('?a=b&c=d', { ignoreQueryPrefix: true });//{ a: 'b', c: 'd' }
var delimited = qs.parse('a=b;c=d', { delimiter: ';' });//{ a: 'b', c: 'd' }
var regexed = qs.parse('a=b;c=d,e=f', { delimiter: /[;,]/ }); //{ a: 'b', c: 'd', e: 'f' }
//特：用点做分隔符
var withDots = qs.parse('a.b=c', { allowDots: true });// { a: { b: 'c' }
```

###### 2.2 Parsing Arrays

​	...

##### 3. qs.stringify(object, [options])

```jsx
qs.stringify({ a: 'b' })//'a=b'
qs.stringify({ a: { b: 'c' } })//'a%5Bb%5D=c'
qs.stringify({ a: { b: 'c' } }, { encode: false });//'a[b]=c' 阻止url编码
qs.stringify(
    { a: 'b', c: ['d', 'e=f'], f: [['g'], ['h']] },
    { encodeValuesOnly: true }
) //'a=b&c[0]=d&c[1]=e%3Df&f[0][0]=g&f[1][0]=h'阻止keys的进行url编码
```

