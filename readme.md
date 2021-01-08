[个人简历](./Aboutme.md)

# JavaScript

## 基础

- [ ] JavaScript 执行上下文

- [ ] JavaScript 作用域链

  - [ ] 作用域链
  - [x] [闭包](./3.JavaScript/闭包.md)

- [ ] JavaScript 面向对象编程
  
  - [x] [new](./3.JavaScript/关于new.md)
  - [x] [JavaScript 原型及原型链](./3.JavaScript/原型和原型链.md)
  
- [ ] JavaScript 继承

- [ ] JavaScript this 指向和 call apply bind
  - [x] [this 指向](./3.JavaScript/this指向.md)
  - [ ] call apply bind
  
- [x] [事件循环](./3.JavaScript/事件循环（event loop）.md)
  
- [ ] JavaScript 模块化的发展史

## ES6

- [ ] let const 和声明提升
- [ ] 解构赋值
- [ ] 箭头函数
- [ ] Vue 3.0 与反射和代理
- [ ] JavaScript 异步编程
  - [ ] [异步编程演变史(待完善)](./3.JavaScript/前端异步编程.md)
  - [ ] Promise 是什么
  - [ ] 手写一个/A+ Promise
  - [ ] Generator 作用和影响
  - [ ] async 和 await
- [ ] Map 和 Set
- [ ] symbol
- [ ] 新增数组方法
- [ ] 新增对象方法

## DOM

- [ ] 事件机制
  - [ ] 发布订阅
- [ ] DOM 操作
- [ ] 事件委托和代理
- [ ] 冒泡和捕获
  - [ ] 事件机制 冒泡有什么用 除了事件委托呢？

## BOM

- [ ] wait more.....

# CSS

## 选择器

- [ ] 优先级
- [ ] 常用选择器类型

## 布局与定位

- [ ] position
- [x] [flex](./5.HTML&CSS/flex.md)
- [ ] grid
- [ ] [z-index 于层叠上下文](./5.HTML&CSS/z-index.md)

## 盒模型

- [ ] box-sizing
- [ ] padding
- [ ] margin
- [ ] border
- [ ] outline

- [ ] 实际问题

  - [ ] 行内元素和块级元素

## 样式(loading)

- [ ] background

  - [ ] background-image

- [ ] font

  - [ ] font-family 问题

- [ ] color

  - [ ] 各颜色模式的差异

- [ ] text
- [ ] 一行...和多行...原理

## CSS3

- [ ] canvas
- [ ] animation
- [ ] loading

## 实际问题

- [ ] 外边距合并问题
- [ ] vue 字体引入解决方案
- [ ] 如何隐藏一个元素
- [ ] 移动端兼容

  - [ ] 1px 问题
  - [ ] rem 和 em 和 vw 各解决方案的优劣
  - [ ] 键盘弹出问题

- [ ] link 和@import 有什么区别

# VUE 框架（loading）

## MVVM 模式

- [ ] 什么是 MVVM 及其优势

## 生命周期

- [ ] vue2.x 和 3.0 的生命周期介绍
- [ ] 父子组件生命周期顺序及有异步方法时的顺序

## 数据绑定

- [ ] 绑定原理
  - [vue2.x绑定原理](./8.源码分析/Vue的MVVM原理.md)
  - vue3.0绑定原理
  - 弃用 defineProperty 的原因

## Vuex

- [ ] [介绍一下 Vuex(待补充&复习源码)](./7.VUE全家桶/Vuex.md)
  - 什么是状态管理
  - 为什么需要状态管理
  - Vuex 和 redux 的区别与联系
- [ ] 实现一个简单的 Vuex

## 组件间通信

- [ ] 父子组件通信方法
- [ ] 爷孙组件通信方法
- [ ] 兄弟组件通信方法

## virtual DOM

- [ ] 介绍一下 vdom
  - 什么是虚拟 DOM
  - 为什么需要虚拟 DOM
  - Vue 的虚拟 DOM 解决了什么
- [ ] vdom 源码解析

## diff

- [ ] 介绍一下 diff
  - 什么是 diff
  - 介绍一下 Vue diff 策略
  - 介绍一下 React diff 策略
  - key 作用
- [ ] 源码解析

## computed 和 watch

- [ ] 源码实现
- [ ] 解析 computed 引用其他 computed 属性是如何实现的？

## Hook

- [ ] 介绍一下 Vue3.0 的 composition-api
- [ ] 什么是 React Hook

## Mixin

- [ ] mixin 介绍
  - Vue mixin 是什么
  - React Hoc 是什么
  - Hoc 和 mixin 有什么区别
  - 什么是高阶组件
  - 为什么 vue 没有高阶组件

## vue 和 React 对比

- [ ] 一个项目如何判断使用 vue 还是 React
- [ ] 从不同角度讲讲 vue 和 React 的不同

## 源码分析

* [x] [nextTick](./8.源码分析/nextTick.md)

## 其他（loading）

- [ ] serveless
- [x] [scoped](./7.VUE全家桶/scoped.md)
- [ ] 如何看待前端和后端

# 工程化

## webpack

- [ ] 介绍一下 wenpack 构建流程
- [ ] webpack 与 rollup 区别与联系
- [ ] 介绍一下 loader
  - 常用的 loader
  - 介绍一两个 loader 实现思路
  - 实现一个自己 loader
- [ ] 介绍一下 plugin
  - plugin
  - 介绍一两个 plugin 思路
  - 实现一个自己的 plugin
- [ ] webpack 热更新是如何实现的
- [ ] 如何做 webpack 层面性能优化
- [ ] 介绍一下 webpack 的 dll
- [ ] 介绍一下 webpakck 的 tree-shaking
- [ ] 介绍一下 webpack 的 scope hoisting

## babel

- [ ] 介绍一下 babel 原理

## 模版引擎

- [ ] 如何实现一个简单的模版引擎

## 前端发布

- [ ] 介绍一下 cdn
- [ ] 增量发布

## 正则表达式

## 单元测试

- [x] [基本概念及 Jest在Vue 单文件的用例写法](./4.前端工程化/单元测试及其基本概念.md)

# 性能优化

## 打包优化

- [ ] 解决方案
  - loader
  - dll
  - compress
  - happypack
  - tree-shaking
  - scope hoisting
  - code splitting

## 网络优化

- [ ] 解决方案
  - dns
  - CDN
    - CDN 回流是什么？
  - 缓存
  - preload 和懒加载
    - 减少 HTTP 请求
    - 图片、base64
  - ssr
- [ ] 代码优化
  - 骨架屏
  - web worker
  - 虚拟列表
  - 懒加载
  - DOM 和 style 批量更新
  - ...

# 计算机网络

## 基础网络知识

- [x] [计算机网络的分层和协议-什么是 TCP/UDP/HTTP](./1.计算机网络/TCPIP四层协议模型.md)

## 传输层

- [x] [TCP 是如何可靠传输的(三次握手和四次挥手)](./1.计算机网络/TCP和UDP的区别-TCP如何进行可靠传输？.md)
- [ ] 一个 TCP 可以建立多少 HTTP 链接

## 应用层

- HTTP
  - [ ] HTTP 发展史(待拆分细节)
    - [HTTP/1 的发展史](./1.计算机网络/HTTP发展史.md)
    - HTTP/2 新特性
    - HTTP/3 面向未来
  - [x] [状态码](./1.计算机网络/状态码.md)
  - [x] [HTTP 缓存机制](./1.计算机网络/HTTP缓存机制.md)
  - [x] [HTTP 通信性能优化](./1.计算机网络/HTTP通信性能优化.md)
- DNS

  - [ ] 介绍一下 DNS

- 网络安全及计算机网络攻击漏洞
  - HTTPS
    - [x] [HTTPS 是如何加密传输](./1.计算机网络/HTTPS安全性原理.md)
  - 漏洞攻击
    - [x] [Cookie 的 HttpOnly 和 XSS 攻击](./XSS攻击.md)
    - [x] [Cookie 的 sameSite 和 CSRF 攻击](./CSRF攻击.md)

# 浏览器工作原理

- [x] [chrome 架构演变历史](./2.浏览器工作原理/浏览器多进程架构的进化史.md)
- [x] [浏览器同源策略](./2.浏览器工作原理/同源策略.md)
- [ ] 从 url 到显示的全部流程
  - [x] [浏览器导航流程](./2.浏览器工作原理/URL导航流程.md)
  - [ ] 浏览器渲染流程
- [ ] [强缓存和协商缓存](./1.计算机网络/HTTP缓存机制.md)
- [ ] [同源策略及跨域问题](./2.浏览器工作原理/同源策略.md)
- [ ] cookie 和 sessionStorage 和 localStorage 各自优劣

# 设计模式

## 各种设计模式

- [ ] test

## 常见设计模式用法

- [ ] test

# 数据结构与算法

## 数据结构

- [ ] 栈、队列、链表
- [ ] 树
- [ ] 图

## 算法

- 常见排序算法
  - [ ] test
- 如何计算复杂度
- DFS 和 BFS 的应用场景
- 动态规划
- ...

# 跨端解决方案集合

* [x] [微信小程序 日签 canvas2img到相册](./跨端解决方案集合/微信小程序 日签 canvas2img到相册.md)