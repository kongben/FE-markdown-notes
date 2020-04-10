#this指向

首先，记住一句话！万源归宗，函数调用只有一种方式

>fn.call(context, arguments)

下面是一道关于this指向面试题。

```javascript
var length = 10;
function fn() {
    console.log(this.length);
}
 
var obj = {
  length: 5,
  method: function(fn) {
    fn();
    arguments[0]();
  }
};
 
obj.method(fn, 1);
```

先不宣布答案，先看看如何只用一招就看出this的真正指向。

第一，首先this永远指向的是最后调用它的对象，所以只有在调用的时候才能知道当前this指向什么作用域。

其次方法调用,共有如下三种形式。

```js
fn(p1, p2) 
obj.method(p1, p2)
fn.call(context, p1, p2)
```

第一种：普通函数调用

```javascript
function fn(){
  console.log(this)
}
fn()
```

把他理解为：

```text
function fn(){
  console.log(this)
}

fn.call() // 当call的无参数时，默认undefined
```

第二种：

对象方法调用。

```text
var obj = {
  fn: function(){
    console.log(this)
  }
}

obj.fn()
```

把他理解为：

```text
var obj = {
  fn: function(){
    console.log(this)
  }
}

obj.fn.call(obj)
```

所以第三种才是最常见的调用方式，也就是说其实函数调用只有一种方式即：

`fn.call(context, arguments)`

> call、bind、apply功能都是手动改变this指向，仅以call举例

这里有一个规定：

> 传的 context 就 null 或者 undefined，那么 window 对象就是默认的 context（严格模式下默认 context 是 undefined）

还记得最开始说过的话吗？this永远指向的是最后调用它的对象，所以只有在调用的时候才能知道当前this指向什么作用域。所以运用上面三条转换规则来看看开头的题目

```javascript
var length = 10;
function fn() {
    console.log(this.length);
}
 
var obj = {
  length: 5,
  method: function(fn) {
    fn();
    arguments[0]();
  }
};
 
obj.method(fn, 1);
```

当执行fn()时，相当于调用

`fn.call(undefined)`

所以会输出10。

当执行arguments[0]()时，相当于调用

```js
arguments.0()
arguments = {
  0:fn,    //function fn(){console.log(this.length);}
  1:第二个参数 1，
  length:2
}
```

this指向了arguments，所以会输出2。

# 练习题

```javascript
this.x = 9;
var module = {
  x: 81,
  getX: function() { return this.x; }
};
module.getX(); // result1

var retrieveX = module.getX;
retrieveX();   //result2
var boundGetX = retrieveX.bind(module);
boundGetX(); // result3
```

答案：

result1:81

result2:9

result3:81

## 总结

1. this 指向的是 call 一个函数时，传入的第一个参数。不传则为window对象。
2. 如果函数调用的形式不是 call ，转换为 call 形式。