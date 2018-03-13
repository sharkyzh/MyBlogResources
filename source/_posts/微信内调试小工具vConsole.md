---
title: 微信内调试小工具vConsole
category:
  - 微信
tags:
  - 微信
abbrlink: c295aa6a
date: 2018-01-11 12:14:50
---

vConsole是腾讯出的一个轻量、可拓展、针对手机网页的前端开发者调试面板。

#### 特性

1. 查看 console 日志
2. 查看网络请求
3. 查看页面 element 结构
4. 查看 Cookies 和 localStorage
5. 手动执行 JS 命令行
6. 自定义插件

#### 上手
下载 vConsole 的最新版本。（不要直接下载 dev 分支下的 dist/vconsole.min.js）

或者使用 npm 安装：

```
npm install vconsole
```
 <!-- more -->
引入 dist/vconsole.min.js 到项目中：

```
<script src="path/to/vconsole.min.js"></script>
<script>
  // 初始化
  var vConsole = new VConsole();
  console.log('Hello world');
</script>
```

#### 使用方法
##### 初始化 & 配置
引入后, 需要手动初始化 vConsole：


```
var vConsole = new VConsole(option);
```

option 是一个选填的 object 对象，具体配置定义请参阅 [公共属性及方法](https://github.com/Tencent/vConsole/blob/dev/doc/public_properties_methods_CN.md)。

使用 setOption() 来更新 option：


```
vConsole.setOption('maxLogNumber', 5000);
// 或者：
vConsole.setOption({maxLogNumber: 5000});


### 打印日志

与 PC 端打印 log 一致，可直接使用 `console.log()` 等方法直接打印日志：

```javascript
console.log('Hello World');
```

未加载 vConsole 模块时，console.log() 会直接打印到原生控制台中；加载 vConsole 后，日志会打印到页面前端+原生控制台。

##### 日志类型
支持 5 种不同类型的日志，会以不同的颜色输出到前端面板：

```
console.log('foo');   // 白底黑字
console.info('bar');  // 白底紫字
console.debug('oh');  // 白底黄字
console.warn('foo');  // 黄底黄字
console.error('bar'); // 红底红字
```
##### Object/Array 结构化展示
支持打印 Object 或 Array 变量，会以结构化 JSON 形式输出（并折叠）：


```
var obj = {};
obj.foo = 'bar';
console.log(obj);
/*
Object
{
  foo: "bar"
}
*/
```
##### 多态
支持传入多个参数，会以空格隔开：


```
var uid = 233;
console.log('UserID:', uid); // 打印出 UserID: 233
```
##### 特殊格式
支持使用 [system] 作为第一个参数，来将 log 输出到 System 面板：


```
console.log('[system]', 'foo'); // 'foo' 会输出到 System 面板
console.log('[system] bar'); // 这行日志会输出到 Log 面板而非 System 面板
```


#### 内置插件
Network 网络

所有 XMLHttpRequest 请求都会被显示到 Network tab 中。

若不希望一个请求显示在面板中，可添加属性 _noVConsole = true 到 XHR 对象中：


```
var xhr = new XMLHttpRequest();
xhr._noVConsole = true; // 不会显示到 tab 中
xhr.open("GET", 'http://example.com/');
xhr.send();
```
#### 文档索引
[文档索引](https://github.com/Tencent/vConsole/blob/dev/doc/a_doc_index_CN.md)

以上部分来源于github，本人只做了抄录。详细可进[项目](https://github.com/Tencent/vConsole)查看！