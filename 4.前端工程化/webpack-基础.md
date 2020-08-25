>基础知识重新学习，若有不对欢迎指正，感恩。
# 什么是webpack？

> 将一个项目js、css、img、font等各种模块打包的模块打包器。

# 为什么需要webpack？

当我们写好代码后还需要做如下事情，才能将代码上传到服务器上供用户使用。

+ 转换es6代码
+ 打包所有asset资源
+ 转换jsx语法
+ css补全前缀、预处理器编译
+ 代码压缩
+ 图片压缩

webpack就是这样一个工具，可以在写完整个项目后，不再需要手动一步一步做以上工作，彻底提升效率。

# 如何使用webpack？

## 环境搭建

> node.js环境和npm包管理

### 初始化项目

> npm init -y

### 安装webpack

> npm install webpack webpack-cli --save-dev

### 初始化配置webpack

`package.json`

![](https://user-gold-cdn.xitu.io/2020/4/13/171733deafc53914?w=1278&h=770&f=png&s=137021)
含义：

新增一个运行`webpack`命令的脚本`serve`

`根目录`新建`webpack.config.js`--wenback默认的配置文件


![](https://user-gold-cdn.xitu.io/2020/4/13/17173421460c06fc?w=990&h=842&f=png&s=145528)
`/src`目录新建`index.js`

运行脚本`npm run serve`

会在`/dist`下得到一个webpack打包后的`index.js`文件。

## 核心概念

### 1.entry

打包的入口参数，接受三种形式的值(字符串、数组、键值对)

![](https://user-gold-cdn.xitu.io/2020/4/13/1717349fa9c7fa99?w=1042&h=626&f=png&s=113859)

字符串形式仅支持单入口，如需多入口就需要使用数组和键值对形式。

### 2.output

打包完成后的输出出口

![](https://user-gold-cdn.xitu.io/2020/4/13/17173421460c06fc?w=990&h=842&f=png&s=145528)

主要参数包括：

+ path：输出文件夹路径
+ filename：输出文件名（[name]表示使用输入文件名表示）

### 3.loader
由于webpack只支持js文件，故需要loader来将非js文件（css、font、img）转换成webpack能识别的模块。然后添加到依赖图中。loader本身是一个函数，接受一个文件，返回转化结果。

常用模块

![](https://user-gold-cdn.xitu.io/2020/4/13/17173525ba1614c5?w=780&h=633&f=png&s=192551)

主要参数：
+ test：正则匹配对应文件
+ use：使用何种loader及其配置项

![](https://user-gold-cdn.xitu.io/2020/4/13/1717355e7ff6de46?w=1262&h=1382&f=png&s=230877)

### plugins
处理bundle（打包后文件）的优化、资源管理、环境变量注入的工作。比如压缩css文件、形成雪碧图。**作用于整个构建过程**。

常见的plugins

![](https://user-gold-cdn.xitu.io/2020/4/13/171735adf39274e6?w=792&h=653&f=png&s=264202)

主要参数：

+ plugins：用来接受插件的数组

![](https://user-gold-cdn.xitu.io/2020/4/13/171735c182cbebcb?w=1412&h=482&f=png&s=102546)

### mode

用来指定当前环境开发环境、生产环境。

主要参数：

+ mode：production（默认）、devlopment、none

通过`process.env.NODE.env`来判断当前环境用来操作不同webpack步骤。

## loader的使用

### ES6、jsx解析

安装babel、babel-loader、react、react-dom

>npm i @babel/core @babel/preset-env @babel/preset-react babel-loader react react-dom -D

根目录新建`.babelrc`

![](https://user-gold-cdn.xitu.io/2020/4/13/1717363a80551dd7?w=806&h=590&f=png&s=91383)

修改`package.json`，添加babel-loader

![](https://user-gold-cdn.xitu.io/2020/4/13/1717373fdd47cd65?w=990&h=1310&f=png&s=203293)

修改 `./src/index.js`代码，加入ES6特性。

新建`./src/search.js`，加入jsx代码。

![](https://user-gold-cdn.xitu.io/2020/4/13/17173680efb50679?w=1330&h=842&f=png&s=165038)

`npm run serve`

在`./dist`下新建`index.html`,引入`index.js`和`search.js`

![](https://user-gold-cdn.xitu.io/2020/4/13/171736dc129e0fde?w=1548&h=842&f=png&s=178673)

会发现 babel将ES6代码转化成ES5代码,将jsx代码转化成js

![](https://user-gold-cdn.xitu.io/2020/4/13/171736c6cda180b2?w=498&h=195&f=png&s=6977)

### css-lodaer&less-loader的使用

安装less、css-loader、less-loader

>npm i less css-loader less-loader -D

修改`package.json`，添加css-loader和less-loader。

![](https://user-gold-cdn.xitu.io/2020/4/13/1717374d3c04dbee?w=990&h=1922&f=png&s=256371)

less/css loader的顺序是use数组的逆序，即先将less加载成css文件，再将css 加载成style，再将style插入head标签中。

新增`search.css`和`search.less`，填写样式，在jsx中引入。`npm run serve`

![](https://user-gold-cdn.xitu.io/2020/4/13/171737897ca582b3?w=362&h=78&f=png&s=4816)

### 图片和字体-file-loader的使用

安装file-loader

>npm i file-loader -D

修改`package.json`，添加file-loader。

![](https://user-gold-cdn.xitu.io/2020/4/13/1717385c4447db65?w=1142&h=2210&f=png&s=305414)

在`/src`中添加字体文件和图片然后在`search.js`这个jsx中引入图片，`search.less`中引入字体。

>search.less
>![](https://user-gold-cdn.xitu.io/2020/4/13/171737d471999e18?w=754&h=662&f=png&s=110431)

>search.js
>![](https://user-gold-cdn.xitu.io/2020/4/13/171737ef87762e97?w=1750&h=914&f=png&s=209246)

最后效果：

![](https://user-gold-cdn.xitu.io/2020/4/13/171737f9c22d359e?w=322&h=134&f=png&s=9118)

### url-loader的使用

安装url-loader

>npm i url-loader -D

修改`package.json`，添加url-loader。

![](https://user-gold-cdn.xitu.io/2020/4/13/17173880dda52d4a?w=1142&h=1130&f=png&s=160376)

url-loader也可以加载img和font，还可以通过设置，将小图片直接转成base64。