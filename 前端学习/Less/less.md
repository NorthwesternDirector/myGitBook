### Less

*LESS 将 CSS 赋予了动态语言的特性，如 变量， 继承， 运算， 函数*

##### 变量

```css
 // LESS
@color: #4D926F; /* 定义变量 */
#header {
  color: @color;
}
h2 {
  color: @color;
}
/* 生成的 CSS */
#header {
  color: #4D926F;
}
h2 {
  color: #4D926F;
}
```

##### 混合

在 LESS 中我们可以定义一些 class 作为属性的集合，然后在另一个 class 中去调用这个类名，就等同于调用这些属性

```css
// LESS
.rounded-corners (@radius: 5px) {
  border-radius: @radius;
  -webkit-border-radius: @radius;
  -moz-border-radius: @radius;
}
#header {
  .rounded-corners; /* 类似函数调用的感觉 */
}
#footer {
  .rounded-corners(10px); /* 支持参数传递 */
}

/* 生成的 CSS */
#header {
  border-radius: 5px;
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
}
#footer {
  border-radius: 10px;
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
}
```

##### 嵌套规则

```css
// LESS
#header {
  h1 {
    font-size: 26px;
    font-weight: bold;
  }
  p { font-size: 12px;
    a { text-decoration: none;
      &:hover { border-width: 1px }
    }
  }
}

/* 生成的 CSS */
#header h1 {
  font-size: 26px;
  font-weight: bold;
}
#header p {
  font-size: 12px;
}
#header p a {
  text-decoration: none;
}
#header p a:hover {
  border-width: 1px;
}
```



