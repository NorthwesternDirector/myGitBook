# npm 

## 常用命令

### 更新

``` js
// 更新生产环境依赖包
npm update <name> --save
// 更新开发环境依赖包
npm update <name> --save-dev
```

### 卸载

```js
// 删除指定模块
npm uninstall <name> 
```

### 安装

```js
//写入到 dependencies 对象 -> 用于生产环境&开发环境
npm i module_name -s => npm install module_name --save
//写入到 devDependencies 对象 -> 仅用于开发环境
npm i module_name -d => npm install module_name --save-dev   
//全局安装
npm i module_name -g 
```

