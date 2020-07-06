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

## 前期准备

在正式阅读之前，你需要知道`this`的5种绑定方式：

- 默认绑定(非严格模式下this指向全局对象, 严格模式下`this`会绑定到`undefined`)
- 隐式绑定(当函数引用有**上下文对象**时, 如 `obj.foo()`的调用方式, `foo`内的`this`指向`obj`)
- 显示绑定(通过`call()`或者`apply()`方法直接指定`this`的绑定对象, 如`foo.call(obj)`)
- new绑定
- 箭头函数绑定(`this`的指向由外层作用域决定的)

### 1. 默认绑定

先介绍一种最简单的绑定方式吧：**默认绑定**。

也就是我们常说的：在非严格模式下`this`指向的是全局对象`window`，而在严格模式下会绑定到`undefined`。

#### 1.1 题目一

老规矩，来看个最基本的案例：

```javascript
var a = 10;
function foo () {
  console.log(this.a)
}
foo();
```

上面的代码其实就相当于是这样:

```javascript
window.a = 10;
function foo() {
  console.log(this.a)
}
window.foo();
```

答案：

```javascript
10
```

#### 1.2 题目二

改造下题目一

```javascript
"use strict";
var a = 10;
function foo () {
  console.log('this1', this)
  console.log(window.a)
  console.log(this.a)
}
foo();
console.log(window.foo)
console.log('this2', this)
```

需要注意的点：

- 开启了严格模式，只是说使得函数内的`this`指向`undefined`，它并不会改变全局中`this`的指向。因此`this1`中打印的是`undefined`，而`this2`还是`window`对象。
- 另外，它也不会阻止`a`被绑定到`window`对象上。

所以最后的执行结果：

```javascript
'this1' undefined
10
Uncaught TypeError: Cannot read property 'a' of undefined
f foo() {...}
'this2' Window{...}
```

#### 1.3 题目三

```javascript
let a = 10
const b = 20

function foo () {
  console.log(this.a)
  console.log(this.b)
}
foo();
console.log(window.a)
```

如果把`var`改成了`let 或者 const`，变量是不会被绑定到`window`上的，所以此时会打印出三个`undefined`。

答案：

```javascript
undefined
undefined
undefined
```

#### 1.4 题目四

```javascript
var a = 1
function foo () {
  var a = 2
  console.log(this)
  console.log(this.a)
}
foo()
```

答案：

```javascript
Window{...}
1 
```

#### 1.5 题目五

把题目`1.4`改造一下 。

```javascript
var a = 1
function foo () {
  var a = 2
  function inner () { 
    console.log(this.a)
  }
  inner()
}

foo()
 
```

在`inner`中，`this`指向的还是`window`。

答案：

```javascript
1
```

### 2. 隐式绑定

#### 2.1 题目一

```javascript
function foo () {
  console.log(this.a)
}
var obj = { a: 1, foo }
var a = 2
obj.foo()
```

在这里`foo`函数内的`this`指向的就是`obj`。

答案都是：

```
1
```

### 3. 隐式绑定的隐式丢失问题

#### 3.1 题目一

使用另一个变量来给函数取别名会发生隐式丢失。

```javascript
function foo () {
  console.log(this.a)
};
var obj = { a: 1, foo };
var a = 2;
var foo2 = obj.foo;

obj.foo();
foo2();
```

答案：

```javascript
1
2
```

这是因为虽然`foo2`指向的是`obj.foo`函数，不过调用它的却是`window`对象，所以它里面`this`的指向是为`window`。

其实也就相当于是`window.foo2()`。

#### 3.2 题目二

```javascript
function foo () {
  console.log(this.a)
};
var obj = { a: 1, foo };
var a = 2;
var foo2 = obj.foo;
var obj2 = { a: 3, foo2: obj.foo }

obj.foo();
foo2();
obj2.foo2();
```

这三种不同的`foo()`打印出来的分别是什么呢？

答案：

```javascript
1
2
3
```

#### 3.3 题目三

```javascript
function foo () {
  console.log(this.a)
}
function doFoo (fn) {
  console.log(this)
  fn()
}
var obj = { a: 1, foo }
var a = 2
doFoo(obj.foo)
```

这里我们将`obj.foo`当成参数传递到`doFoo`函数中，在传递的过程中，`obj.foo()`函数内的`this`发生了改变，指向了`window`。

因此结果为：

```javascript
Window{...}
2
```

注意，我这里说的是`obj.foo()`函数，而不是说`doFoo()`。`doFoo()`函数内的`this`本来就是指向`window`的，因为这里是`window`调用的它。

#### 3.4 题目四

```javascript
function foo () {
  console.log(this.a)
}
function doFoo (fn) {
  console.log(this)
  fn()
}
var obj = { a: 1, foo }
var a = 2
var obj2 = { a: 3, doFoo }

obj2.doFoo(obj.foo)
```

现在调用`obj2.doFoo()`函数，里面的`this`指向的应该是`obj2`，因为是`obj2`调用的它。

但是`obj.foo()`打印出来的`a`依然是`2`，也就是`window`下的。

执行结果为：

```javascript
{ a:3, doFoo: f }
2
```

一样的代码，试试严格模式下：

```javascript
"use strict"
function foo () {
  console.log(this.a)
}
function doFoo (fn) {
  console.log(this)
  fn()
}
var obj = { a: 1, foo }
var a = 2
var obj2 = { a: 3, doFoo }
obj2.doFoo(obj.foo)
```

执行结果：

```
{ a:3, doFoo: f }
Uncaught TypeError: Cannot read property 'a' of undefined
```

### 4. 显式绑定

#### 4.1 题目一

```javascript
function foo () {
  console.log(this.a)
}
var obj = { a: 1 }
var a = 2

foo()
foo.call(obj)
foo.apply(obj)
foo.bind(obj)
```

答案：

```javascript
2
1
1
```

如果`call、apply、bind`接收到的第一个参数是空或者`null、undefined`的话，则会忽略这个参数。

例如🌰：

```javascript
function foo () {
  console.log(this.a)
}
var a = 2
foo.call()
foo.call(null)
foo.call(undefined)
```

输出的是：

```javascript
2
2
2 
```

#### 4.2 题目二

```javascript
var obj1 = {
  a: 1
}
var obj2 = {
  a: 2,
  foo1: function () {
    console.log(this.a)
  },
  foo2: function () {
    setTimeout(function () {
      console.log(this)
      console.log(this.a)
    }, 0)
  }
}
var a = 3

obj2.foo1()
obj2.foo2()
```

而对于`setTimeout`中的函数，这里存在隐式绑定的隐式丢失，也就是当我们将函数作为参数传递时会被隐式赋值，回调函数丢失`this`绑定，因此这时候`setTimeout`中的函数内的`this`是指向`window`的。

所以最终的结果是：

```javascript
2
Window{...}
3
```

#### 4.3 题目三

```javascript
var obj1 = {
  a: 1
}
var obj2 = {
  a: 2,
  foo1: function () {
    console.log(this.a)
  },
  foo2: function () {
    setTimeout(function () {
      console.log(this)
      console.log(this.a)
    }.call(obj1), 0)
  }
}
var a = 3
obj2.foo1()
obj2.foo2()
```

现在的执行结果就是：

```javascript
2
{ a: 1 }
1
```

但是看看我这里的写法，我是将`.call`运用到`setTimeout`里的回调函数上，并不是运用到`obj2.foo2()`上。

所以有小伙伴就会问了，我下面的这种写法不可以吗？

```javascript
obj2.foo2.call(obj1)
```

**注意⚠️**：如果是这种写法的话，我改变的就是`foo2`函数内的`this`的指向了，但是我们知道，`foo2`函数内`this`的指向和`setTimeout`里函数的`this`是没有关系的，因为调用定时器的始终是`window`。

并且这里使用`.bind()`也是可以的，因为定时器里的函数在时间到了之后本就是会自动执行的。

#### 4.4 题目四

OK👌，我们不用定时器，把它干掉，换成一个函数：

```
var obj1 = {
  a: 1
}
var obj2 = {
  a: 2,
  foo1: function () {
    console.log(this.a)
  },
  foo2: function () {
    function inner () {
      console.log(this)
      console.log(this.a)
    }
    inner()
  }
}
var a = 3
obj2.foo1()
obj2.foo2()
 
```

其实这里有点像题目`1.5`有木有，都是函数内包裹着函数。

调用`inner`函数的依然是`window`，所以结果为：

```
2
Window{...}
3
 
```

如果给`inner()`函数显式绑定的话：

```
inner.call(obj1)
 
```

结果为

```
2
{ a: 1 }
1
 
```

#### 4.5 题目五

其实在实际面试中，面试官喜欢以这样的方式考你：

看看这道题，会输出什么呢 🤔️？

```
function foo () {
  console.log(this.a)
}
var obj = { a: 1 }
var a = 2

foo()
foo.call(obj)
foo().call(obj)
 
```

也就是使用`.call()`方法位置的不同。

结果：

```javascript
2
1
2
Uncaught TypeError: Cannot read property 'call' of undefined
```

- `foo()`会正常打印出`window`下的`a`，也就是`2`
- `foo.call(obj)`由于显式绑定了`this`，所以会打印出`obj`下的`a`，也就是`1`
- `foo().call(obj)`开始会执行`foo()`函数，打印出`2`，但是会对`foo()`函数的返回值执行`.call(obj)`操作，可是我们可以看到`foo()`函数的返回值是`undefined`，因此就会报错了。

所以我们可以看到`foo.call()`和`foo().call()`的区别了，一个是针对于函数，一个是针对于函数的返回值。

下面我会用更多的例题来帮助大家了解这类题目的解法。

#### 4.6 题目六

OK👌，既然刚刚`4.5`是因为函数没有返回值才报的错，那我现在给它加上返回值看看：

```
function foo () {
  console.log(this.a)
  return function () {
    console.log(this.a)
  }
}
var obj = { a: 1 }
var a = 2

foo()
foo.call(obj)
foo().call(obj)
 
```

你能想到现在会输出什么吗？

答案是会输出`3`个数，还是`4`个数，亦或者`6`个数呢？

😁 嘻嘻，不逗你了，结果竟然是：

```
2
1
2
1
 
```

- 第一个数字`2`自然是`foo()`输出的，虽然`foo()`函数也返回了一个匿名函数，但是并没有调用它呀，只有写成`foo()()`，这样才算是调用匿名函数。
- 第二个数字`1`是`foo.call(obj)`输出的，由于`.call()`是紧跟着`foo`的，所以改变的是`foo()`内`this`的指向，并且`.call()`是会使函数立即执行的，因此打印出`1`，同理，它也没有调用返回的函数。
- 第三个数字`2`是`foo().call(obj)`先执行`foo()`时打印出来的，此时`foo()`内`this`还是指向`window`。
- 在执行完`foo()`之后，会返回一个匿名函数，并且后面使用了`.call(obj)`来改变这个匿名函数的`this`指向并调用了它，所以输出了`1`。

#### 4.7 题目七

想想我们把`call`换成`bind`会怎么样呢？

先来回忆一下它们的区别：`call`是会直接执行函数的，`bind`是返回一个新函数，但不会执行。

```
function foo () {
  console.log(this.a)
  return function () {
    console.log(this.a)
  }
}
var obj = { a: 1 }
var a = 2

foo()
foo.bind(obj)
foo().bind(obj)
 
```

结果自然就是：

```javascript
2
2
```

- `foo()`会执行没错，打印出了`2`。
- 但是`foo.bind(obj)`却不会执行，它返回的是一个新函数。
- `foo().bind(obj)`只会执行前面的`foo()`函数，打印出`2`，`.bind(obj)`只是将`foo()`返回的匿名函数显式绑定`this`而已，并没有调用。

#### 4.8 题目八

说实话，做上面这类题目，会让我有一种疑惑。

这种函数内返回的函数，它的`this`会和它外层的函数有关吗？

也就是内层函数它的`this`到底是谁呢？

还是那句话，谁最后调用的它，`this`就指向谁。

```
function foo () {
  console.log(this.a)
  return function () {
    console.log(this.a)
  }
}
var obj = { a: 1 }
var a = 2

foo.call(obj)()
 
```

就像是这道题，`foo()`函数内的`this`虽然指定了是为`obj`，但是调用最后调用匿名函数的却是`window`。

所以结果为：

```
1
2
 
```

（请注意这个例子🌰，后面它会与箭头函数做对比）

#### 4.9 题目九

一直都在做函数返回函数的题目，让我们来看看把它们加到对象里，会有哪些有趣的题目吧。

```
var obj = {
  a: 'obj',
  foo: function () {
    console.log('foo:', this.a)
    return function () {
      console.log('inner:', this.a)
    }
  }
}
var a = 'window'
var obj2 = { a: 'obj2' }

obj.foo()()
obj.foo.call(obj2)()
obj.foo().call(obj2)
 
```

现在，没和你玩文字游戏了，每个`foo`返回的函数我都调用了，但是你能知道每次调用，打印出的都是什么吗？

- `obj.foo()`自然是打印出`foo: obj`和`inner: window`，这个没什么疑惑的。
- `obj.foo.(obj2)()`其实也没啥可疑惑的了，打印出`foo: obj2`和`inner: window`(类似`4.8`)。
- 那么`obj.foo().call(obj2)`就更没啥可疑惑的了，打印出`foo: obj`和`inner: obj2`。

完了，都没啥可疑惑的了。就这点东西，你都掌握了...

OK，小fo子，请记住你现在的自信，等会做综合题的时候希望你还能如此清醒 🙏。

#### 4.10 题目十

一直做这种题目是不是没意思，让我们加几个参数来玩玩。

[阴笑]~

```javascript
var obj = {
  a: 1,
  foo: function (b) {
    b = b || this.a
    return function (c) {
      console.log(this.a + b + c)
    }
  }
}
var a = 2
var obj2 = { a: 3 }

obj.foo(a).call(obj2, 1)
obj.foo.call(obj2)(1)
```

执行结果：

```javascript
6
6
```

- 开始调用`obj.foo(a)`将`2`传入`foo`函数并赋值给型参`b`，并且由于闭包的原因，使得匿名函数内能访问到`b`，之后调用匿名函数的时候，用`call()`改变了`this`的指向，使得匿名函数内`this.a`为`3`，并传入最后一个参数`1`，所以第一行输出的应该是`3 + 2 + 1`，也就是`6`。
- 而第二行，`obj.foo.call(obj2)`这里是将`foo`函数内的`this`指向了`obj2`，同时并没有传递任何参数，所以`b`开始是`undefined`的，但是又因为有一句`b = b || this.a`，使得`b`变为了`3`；同时最后一段代码`(1)`，是在调用匿名函数，且和这个匿名函数内的`this`应该是指向`window`的，因此输出也为`3+2+1`，为`6`。

这道题其实是我自己编的 😂，编完之后还觉得挺好玩的，即考到了闭包，又考到了`call`方法，还考到了算数...

哈哈哈哈～

(小伙子，年纪轻轻就能出如此心机的题目，让你当面试官还得了？)

### 5. 显式绑定的其它用法

除了上面👆那几道题的用法之外，我们还可以有一些其它的用法。

例如，我们可以在一个函数内使用`call`来显式绑定某个对象，这样无论怎样调用它，其内部的`this`总是指向这个对象。(可见题目`5.1`)

#### 5.1 题目一

```javascript
function foo1 () {
  console.log(this.a)
}
var a = 1
var obj = {
  a: 2
}

var foo2 = function () {
  foo1.call(obj)
}

foo2()
foo2.call(window)
 
```

这里`foo2`函数内部的函数`foo1`我们使用`call`来显式绑定`obj`，就算后面再用`call`来绑定`window`也没有用了。

结果为：

```javascript
2
2
 
```

#### 5.2 题目二

```javascript
function foo1 (b) {
  console.log(`${this.a} + ${b}`)
  return this.a + b
}
var a = 1
var obj = {
  a: 2
}

var foo2 = function () {
  return foo1.call(obj, ...arguments)
}

var num = foo2(3)
console.log(num)
 
```

答案：

```javascript
'2 + 3'
5
 
```

#### 5.3 题目三

接下我想要介绍一个比较冷门的知识。

相信大家对`forEach、map、filter`都不陌生吧，它们是`JS`内置的一些函数，但是你知道它们的第二个参数也是能绑定`this`的吗？ 😁

来看看下面👇的题目：

```javascript
function foo (item) {
  console.log(item, this.a)
}
var obj = {
  a: 'obj'
}
var a = 'window'
var arr = [1, 2, 3]

// arr.forEach(foo, obj)
// arr.map(foo, obj)
arr.filter(function (i) {
  console.log(i, this.a)
  return i > 2
}, obj)
 
```

这里的答案为：

```javascript
1 "obj"
2 "obj"
3 "obj"
 
```

如果我们没有传递第二个参数`obj`的话，`this.a`打印出来的肯定就是`window`下的`a`了，但是传入了之后将`obj`显示绑定到第一个参数函数上。

(关于`arr.filter`为什么也会打印出`1, 2, 3`，那是因为虽然我们使用了`return i > 2`，不过在执行阶段`filter`还是把每一项都打印出来)

#### 总结

总结一下这部分的知识点好了：

- `this` 永远指向最后调用它的那个对象
- 匿名函数的`this`永远指向`window`
- 使用`.call()`或者`.apply()`的函数是会直接执行的
- `bind()`是创建一个新的函数，需要手动调用才会执行
- 如果`call、apply、bind`接收到的第一个参数是空或者`null、undefined`的话，则会忽略这个参数
- `forEach、map、filter`函数的第二个参数也是能显式绑定`this`的

### 6. new 绑定

好滴，让我们来看看另一种`this`的绑定形式，也就是`new`绑定。

使用`new`来调用一个函数，会构造一个新对象并把这个新对象绑定到调用函数中的`this`。

例如第一题。

#### 6.1 题目一

使用`new`来调用`Person`，构造了一个新对象`person1`并把它(`person1`)绑定到`Person`调用中的`this`。

```javascript
function Person (name) {
  this.name = name
}
var name = 'window'
var person1 = new Person('LinDaiDai')
console.log(person1.name)
 
```

答案：

```javascript
'LinDaiDai'
 
```

#### 6.2 题目二

构造函数中不仅可以加属性，也可以加方法：

```javascript
function Person (name) {
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
  }
  this.foo2 = function () {
    return function () {
      console.log(this.name)
    }
  }
}
var person1 = new Person('person1')
person1.foo1()
person1.foo2()()
 
```

这道题的写法不得不让我想到题目`4.9`：

```javascript
var obj = {
  a: 'obj',
  foo: function () {
    console.log('foo:', this.a)
    return function () {
      console.log('inner:', this.a)
    }
  }
}
 
```

好像都是函数包裹着函数，没错，其实它们的解法都差不多。😁

所以这道题的结果为：

```javascript
'person1'
''
 
```

- 第一个`this.name`打印的肯定是`person1`对象中的`name`，也就是构造`person1`对象时传递进去的`person1`字符串。
- 第二个`this.name`打印的应该就是`window`下的`name`了，但是这里`window`对象中并不存在`name`属性，所以打印出的是空。

在做这道题时，我发现了一个有意思的现象。

我将这道题用浏览器打开，控制台输出的竟然是:

```javascript
'person1'
'window'
 
```

咦 🤔️ ？`window`下明明没有`name`这个属性啊，它怎么会打印出`window`，别和我说我电脑的浏览器这么吊...给`window`对象自动加了一个属性值为`window`的`name`属性...

思考了一会，我想起来了，之前我在代码里确实定义了一个变量`name`，`var name = 'window'`。

可是这段代码我已经删掉了啊，并且强制刷新了浏览器，难道它还是存在于`window`中吗？

为了验证我的想法，我又重新定义了一个变量`var name = 'LinDaiDai'`，然后打开浏览器刷新，之后再删除这段代码再刷新。

果然，`window.name`打印出来还是`LinDaiDai`。它会记住这个`name`属性不被回收，直到你关闭此页签。

但是如果把`name`属性名换成别的(比如`dd`)，它就不会有这种情况。

有兴趣的小伙伴可以去尝试一下哈，这里我就不深究了...

#### 6.3 题目三

使用`new`函数创建的对象和字面量形式创建出来的对象好像没什么大的区别，如果对象中有属性是函数类型的话，并且不是箭头函数，那么解法都一样。在后面说到箭头函数的时候就有区别了，不过我们一步一步来。

先看看下面👇这道题：

```javascript
var name = 'window'
function Person (name) {
  this.name = name
  this.foo = function () {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
var person2 = {
  name: 'person2',
  foo: function() {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
  
var person1 = new Person('person1')
person1.foo()()
person2.foo()()
 
```

在这道题中，`person1.foo`和`person2`就没有什么区别。

打印出来的结果为：

```javascript
'person1'
'window'
'person2'
'window'
 
```

#### 6.4 题目四

当`new`绑定结合显示绑定，例如`call`函数的话，解起来其实也不难。

来看看下面👇的题目。

```javascript
var name = 'window'
function Person (name) {
  this.name = name
  this.foo = function () {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
var person1 = new Person('person1')
var person2 = new Person('person2')

person1.foo.call(person2)()
person1.foo().call(person2)
 
```

在做这类题的时候，你就把`Person`生成的`person1`脑补成：

```javascript
var person1 = {
	name: 'person1',
	foo: function () {
		console.log(this.name)
		return function () {
			console.log(this.name)
		}
	}
}
 
```

所以答案很容易就出来了：

```
'person2'
'window'
'person1'
'person2'
 
```

解题分析：

- `person1.foo.call(person2)()`将`foo()`函数内的`this`指向了`person2`，所以打印出`person2`，而内部返回的匿名函数是由`window`调用的，所以打印出`window`。(类似题目`4.9`)
- `person1.foo().call(person2)`是将匿名函数的`this`显式绑定到了`person2`上，所以打印出来的会是`person2`。

### 7. 箭头函数绑定

终于到了期待已久的箭头函数绑定 😁。

在上面👆，我们有学到一个诀窍：**this 永远指向最后调用它的那个对象**。

但是对于箭头函数就不是这样咯，**它里面的`this`是由外层作用域来决定的，且指向函数定义时的this而非执行时**。

`它里面的this是由外层作用域来决定的`啥意思呢？来看看这句话：

> 箭头函数中没有 this 绑定，必须通过查找作用域链来决定其值，如果箭头函数被非箭头函数包含，则 this 绑定的是最近一层非箭头函数的 this，否则，this 为 undefined。

而`且指向函数定义时的this而非执行时`这句话可以等会看题目`7.4`。

读了这句话相信你已经能解决80%的题目了，让我们看完了第一题`7.1`之后，再来看看箭头函数可以分为哪几类题目来说吧，这是目录：

- 字面量对象中普通函数与箭头函数的区别: 只有一层函数的题目
- 字面量对象中普通函数与箭头函数的区别：函数嵌套的题目
- 构造函数对象中普通函数和箭头函数的区别：只有一层函数的题目
- 构造函数对象中普通函数和箭头函数的区别：函数嵌套的题目
- 箭头函数结合`.call`的题目

#### 7.1 题目一

```javascript
var obj = {
  name: 'obj',
  foo1: () => {
    console.log(this.name)
  },
  foo2: function () {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
}
var name = 'window'
obj.foo1()
obj.foo2()()
 
```

这道题就非常有代表性，它明确了箭头函数内的`this`是由外层作用域决定的。

- 对于`obj.foo1()`函数的调用，它的外层作用域是`window`，对象`obj`当然不属于作用域了(我们知道作用域只有全局作用域`window`和局部作用域函数)。所以会打印出`window`
- `obj.foo2()()`，首先会执行`obj.foo2()`，这不是个箭头函数，所以它里面的`this`是调用它的`obj`对象，因此打印出`obj`，而返回的匿名函数是一个箭头函数，**它的`this`由外层作用域决定**，那也就是函数`foo2`咯，那也就是它的`this`会和`foo2`函数里的`this`一样，就也打印出了`obj`。

答案：

```javascript
'window'
'obj'
'obj'
 
```

做完了这道题心里是不是有点谱了，感觉也不是那么难嘛...😁

让我们来拆分一下看看区别。

#### 7.2 题目二

**字面量对象中普通函数与箭头函数的区别: 只有一层函数的题目**

```javascript
var name = 'window'
var obj1 = {
	name: 'obj1',
	foo: function () {
		console.log(this.name)
	}
}

var obj2 = {
	name: 'obj2',
	foo: () => {
		console.log(this.name)
	}
}

obj1.foo()
obj2.foo()
 
```

解题分析：

- 不使用箭头函数的`obj1.foo()`是由`obj1`调用的，所以`this.name`为`obj1`。
- 使用箭头函数的`obj2.foo()`的外层作用域是`window`，所以`this.name`为`window`。

答案：

```javascript
'obj1'
'window'
 
```

#### 7.3 题目三

**字面量对象中普通函数与箭头函数的区别：函数嵌套的题目**

如果用普通函数和箭头函数来做一层嵌套关系的话，一共有四种情况，让我们把每种情况都考虑一遍 😁：

```javascript
var name = 'window'
var obj1 = {
  name: 'obj1',
  foo: function () {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
var obj2 = {
  name: 'obj2',
  foo: function () {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
}
var obj3 = {
  name: 'obj3',
  foo: () => {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
var obj4 = {
  name: 'obj4',
  foo: () => {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
}

obj1.foo()()
obj2.foo()()
obj3.foo()()
obj4.foo()()
 
```

解题分析：

- `obj1.foo()()`两层都是普通函数，类似于题目`4.6`，分别打印出`obj1`和`window`。
- `obj2.foo()()`外层为普通函数，内层为箭头，类似于题目`7.1`，都是打印出`obj2`。
- `obj3.foo()()`外层为箭头函数，内层为普通函数，箭头函数的`this`由外层作用域决定，因此为`window`，内层普通函数由调用者决定，调用它的是`window`，因此也为`window`。
- `obj4.foo()()`两层都是箭头函数，第一个箭头函数的`this`由外层作用域决定，因此为`window`，第二个箭头函数的`this`也由外层作用域决定，它的外层作用域是第一个箭头函数，而第一个箭头函数的`this`是`window`，因此内层的`this`也是`window`。

答案：

(额，题目太长，为了你好看，所以答案我会把题目复制一遍... 放心... 我这绝对不是为了凑字数...  😅)

```javascript
var name = 'window'
var obj1 = {
  name: 'obj1',
  foo: function () {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
var obj2 = {
  name: 'obj2',
  foo: function () {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
}
var obj3 = {
  name: 'obj3',
  foo: () => {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
var obj4 = {
  name: 'obj4',
  foo: () => {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
}

obj1.foo()() // 'obj1' 'window'
obj2.foo()() // 'obj2' 'obj2'
obj3.foo()() // 'window' 'window'
obj4.foo()() // 'window' 'window'
 
```

#### 7.4 题目四

**构造函数对象中普通函数和箭头函数的区别：一层函数的题目**

```javascript
var name = 'window'
function Person (name) {
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
  }
  this.foo2 = () => {
    console.log(this.name)
  }
}
var person2 = {
  name: 'person2',
  foo2: () => {
    console.log(this.name)
  }
}
var person1 = new Person('person1')
person1.foo1()
person1.foo2()
person2.foo2()
 
```

解题思路：

- `person1.foo1()`是个普通函数，**this由最后调用它的对象决定**，即`person1`。
- `person1.foo2()`为箭头函数，**this由外层作用域决定，且指向函数定义时的this而非执行时**，在这里它的外层作用域是函数`Person`，且这个是构造函数，并且使用了`new`来生成了对象`person1`，所以此时`this`的指向是为`person1`。
- `person2.foo2()`字面量创建的的对象`person2`中的`foo2`是个箭头函数，由于`person2`是直接在`window`下创建的，你可以理解为它所在的作用域就是在`window`下，因此`person2.foo2()`内的`this`应该是`window`。

答案：

```javascript
'person1'
'person1'
'window'
 
```

#### 7.5 题目五

**构造函数对象中普通函数和箭头函数的区别：函数嵌套的题目**

```javascript
var name = 'window'
function Person (name) {
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
  this.foo2 = function () {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
  this.foo3 = () => {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
  this.foo4 = () => {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
}
var person1 = new Person('person1')
person1.foo1()()
person1.foo2()()
person1.foo3()()
person1.foo4()()
 
```

解题分析：

- `person1.foo1()()`两层都是普通函数，这个不再重复说了，打印出`person1`和`window`。(类似题目`6.2`)
- `person1.foo2()()`第一层普通函数，它的`this`是由最后调用它的对象决定也就是`person1`，第二层为箭头函数，它的`this`由外层作用域决定，也就是`foo2`这个函数，因此也为`person1`。
- `person1.foo3()()`第一层为箭头函数，`this`由外层作用域决定，因此为`person1`，第二层为普通函数，由最后调用者决定，因此为`window`。
- `person1.foo4()()`两层都是箭头函数，`this`由外层作用域决定，所以都是`person1`。

答案：

```javascript
var name = 'window'
function Person (name) {
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
  this.foo2 = function () {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
  this.foo3 = () => {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
  this.foo4 = () => {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
}
var person1 = new Person('person1')
person1.foo1()() // 'person1' 'window'
person1.foo2()() // 'person1' 'person1'
person1.foo3()() // 'person1' 'window'
person1.foo4()() // 'person1' 'person1'
 
```

#### 7.6 题目六

**箭头函数结合`.call`的题目**

箭头函数的`this`无法通过`bind、call、apply`来**直接**修改，但是可以通过改变作用域中`this`的指向来间接修改。

```javascript
var name = 'window'
var obj1 = {
  name: 'obj1',
  foo1: function () {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  },
  foo2: () => {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
var obj2 = {
  name: 'obj2'
}
obj1.foo1.call(obj2)()
obj1.foo1().call(obj2)
obj1.foo2.call(obj2)()
obj1.foo2().call(obj2)
 
```

解题分析：

- `obj1.foo1.call(obj2)()`第一层为普通函数，并且通过`.call`改变了`this`指向为`obj2`，所以会打印出`obj2`，第二层为箭头函数，它的`this`和外层作用域中的`this`相同，因此也是`obj2`。
- `obj1.foo().call(obj2)`第一层打印出`obj1`，第二层为箭头函数，使用了`.call`想要修改`this`的指向，但是并不能成功，因此`.call(obj2)`对箭头函数无效，还是打印出`obj1`。
- `obj1.foo2.call(obj2)()`第一层为箭头函数，并且想要通过`.call(obj2)`改变`this`指向，但是无效，且它的外层作用域是`window`，所以会打印出`window`，第二层为普通函数，`this`是最后调用者`window`，所以也会打印出`window`。
- `obj1.foo2().call(obj2)`第一层为箭头函数，外层作用域是`window`，打印出`window`，第二层为普通函数，且使用了`.call(obj2)`来改变`this`指向，所以打印出了`obj2`。

答案：

```javascript
var name = 'window'
var obj1 = {
  name: 'obj1',
  foo1: function () {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  },
  foo2: () => {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
var obj2 = {
  name: 'obj2'
}
obj1.foo1.call(obj2)() // 'obj2' 'obj2'
obj1.foo1().call(obj2) // 'obj1' 'obj1'
obj1.foo2.call(obj2)() // 'window' 'window'
obj1.foo2().call(obj2) // 'window' 'obj2'
 
```

在这道题中，`obj1.foo1.call(obj2)()`就相当于是通过改变作用域间接改变箭头函数内`this`的指向。

#### 总结

OK👌，来总结一下箭头函数需要注意的点吧：

- 它里面的`this`是由外层作用域来决定的，且指向函数定义时的`this`而非执行时
- 字面量创建的对象，作用域是`window`，如果里面有箭头函数属性的话，`this`指向的是`window`
- 构造函数创建的对象，作用域是可以理解为是这个构造函数，且这个构造函数的`this`是指向新建的对象的，因此`this`指向这个对象。
- 箭头函数的`this`是无法通过`bind、call、apply`来**直接**修改，但是可以通过改变作用域中`this`的指向来间接修改。

**优点**

- 箭头函数写代码拥有更加简洁的语法(当然也有人认为这是缺点)
- `this`由外层作用域决定，所以在某些场合我们不需要写类似`const that = this`这样的代码

**避免使用的场景**

根据箭头函数的特性，所以我们应该**避免**在以下四种场景中使用它：

1. 使用箭头函数定义对象的方法

```javascript
let obj = {
    value: 'LinDaiDai',
    getValue: () => console.log(this.value)
}
obj.getValue() // undefined
 
```

1. 定义原型方法

```javascript
function Foo (value) {
    this.value = value
}
Foo.prototype.getValue = () => console.log(this.value)

const foo1 = new Foo(1)
foo1.getValue() // undefined
 
```

1. 构造函数使用箭头函数

```javascript
const Foo = (value) => {
    this.value = value;
}
const foo1 = new Foo(1)
// 事实上直接就报错了 Uncaught TypeError: Foo is not a constructor
console.log(foo1);
 
```

1. 作为事件的回调函数

```javascript
const button = document.getElementById('myButton');
button.addEventListener('click', () => {
    console.log(this === window); // => true
    this.innerHTML = 'Clicked button';
});
 
```

### 8. 综合题

#### 8.1 题目一

字面量对象中的各种场景

```javascript
var name = 'window'
var person1 = {
  name: 'person1',
  foo1: function () {
    console.log(this.name)
  },
  foo2: () => console.log(this.name),
  foo3: function () {
    return function () {
      console.log(this.name)
    }
  },
  foo4: function () {
    return () => {
      console.log(this.name)
    }
  }
}
var person2 = { name: 'person2' }

person1.foo1()
person1.foo1.call(person2)

person1.foo2()
person1.foo2.call(person2)

person1.foo3()()
person1.foo3.call(person2)()
person1.foo3().call(person2)

person1.foo4()()
person1.foo4.call(person2)()
person1.foo4().call(person2)
 
```

这里我就不写题解了，因为如果你认真看了前面的题目的话，我相信一定能做的来，其实就是将原本分散的知识点汇总在一起。😁

- `person1.foo1()`类似题目`4.5`
- `person1.foo2()`类似题目`7.1`
- `person1.foo3()`类似题目`7.3`
- `person1.foo4()`类似题目`7.3`

答案：

```javascript
var name = 'window'
var person1 = {
  name: 'person1',
  foo1: function () {
    console.log(this.name)
  },
  foo2: () => console.log(this.name),
  foo3: function () {
    return function () {
      console.log(this.name)
    }
  },
  foo4: function () {
    return () => {
      console.log(this.name)
    }
  }
}
var person2 = { name: 'person2' }

person1.foo1() // 'person1'
person1.foo1.call(person2) // 'person2'

person1.foo2() // 'window'
person1.foo2.call(person2) // 'window'

person1.foo3()() // 'window'
person1.foo3.call(person2)() // 'window'
person1.foo3().call(person2) // 'person2'

person1.foo4()() // 'person1'
person1.foo4.call(person2)() // 'person2'
person1.foo4().call(person2) // 'person1'
 
```

#### 8.2 题目二

构造函数中的各种场景

```javascript
var name = 'window'
function Person (name) {
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
  },
  this.foo2 = () => console.log(this.name),
  this.foo3 = function () {
    return function () {
      console.log(this.name)
    }
  },
  this.foo4 = function () {
    return () => {
      console.log(this.name)
    }
  }
}
var person1 = new Person('person1')
var person2 = new Person('person2')

person1.foo1()
person1.foo1.call(person2)

person1.foo2()
person1.foo2.call(person2)

person1.foo3()()
person1.foo3.call(person2)()
person1.foo3().call(person2)

person1.foo4()()
person1.foo4.call(person2)()
person1.foo4().call(person2)
 
```

- `person1.foo1()`类似题目`7.4`
- `person1.foo2()`类似题目`7.4`
- `person1.foo3()`类似题目`7.5`
- `person1.foo4()`类似题目`7.5`

答案：

```
var name = 'window'
function Person (name) {
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
  },
  this.foo2 = () => console.log(this.name),
  this.foo3 = function () {
    return function () {
      console.log(this.name)
    }
  },
  this.foo4 = function () {
    return () => {
      console.log(this.name)
    }
  }
}
var person1 = new Person('person1')
var person2 = new Person('person2')

person1.foo1() // 'person1'
person1.foo1.call(person2) // 'person2'

person1.foo2() // 'person1'
person1.foo2.call(person2) // 'person1'

person1.foo3()() // 'window'
person1.foo3.call(person2)() // 'window'
person1.foo3().call(person2) // 'person2'

person1.foo4()() // 'person1'
person1.foo4.call(person2)() // 'person2'
person1.foo4().call(person2) // 'person1'
 
```

#### 8.3 题目三

```
var name = 'window'
function Person (name) {
  this.name = name
  this.obj = {
    name: 'obj',
    foo1: function () {
      return function () {
        console.log(this.name)
      }
    },
    foo2: function () {
      return () => {
        console.log(this.name)
      }
    }
  }
}
var person1 = new Person('person1')
var person2 = new Person('person2')

person1.obj.foo1()()
person1.obj.foo1.call(person2)()
person1.obj.foo1().call(person2)

person1.obj.foo2()()
person1.obj.foo2.call(person2)()
person1.obj.foo2().call(person2)
 
```

这道题还是蛮有意思的，可以仔细说下。

首先是定义了一个构造函数`Person`，不过它与前面几题的区别就是，函数是放在其中的一个叫`obj`的对象里面。

**在这里我提醒一句：this 永远指向最后调用它的那个对象**。

解题分析：

- `person1.obj.foo1()()`返回的是一个普通的匿名函数，调用它的是`window`，所以打印出`window`。
- `person1.obj.foo1.call(person2)()`中是使用`.call(person2)`改变第一层函数中的`this`，匿名函数和它没关系，依旧是`window`调用的，所以打印出`window`。
- `person1.obj.foo1().call(person2)`是通过`.call(person2)`改变匿名函数内的`this`，所以绑定有效，因此打印出`person2`。
- `person1.obj.foo2()()`第一层为普通函数，第二层为匿名箭头函数。首先让我们明确匿名箭头函数内的`this`是由第一层普通函数决定的，所以我们只要知道第一层函数内的`this`是谁就可以了。而这里，第一层函数最后是由谁调用的呢 🤔️？是由`obj`这个对象，所以打印出`obj`。
- `person1.obj.foo2.call(person2)()`中使用`.call(person2)`改变了第一层函数中的`this`指向，所以第二层的箭头函数会打印出`person2`。
- `person1.obj.foo2().call(person2)`中使用`.call(person2)`想要改变内层箭头函数的`this`指向，但是失败了，所以还是为外层作用域里的`this`，打印出`obj`。

答案

```
var name = 'window'
function Person (name) {
  this.name = name
  this.obj = {
    name: 'obj',
    foo1: function () {
      return function () {
        console.log(this.name)
      }
    },
    foo2: function () {
      return () => {
        console.log(this.name)
      }
    }
  }
}
var person1 = new Person('person1')
var person2 = new Person('person2')

person1.obj.foo1()() // 'window'
person1.obj.foo1.call(person2)() // 'window'
person1.obj.foo1().call(person2) // 'person2'

person1.obj.foo2()() // 'obj'
person1.obj.foo2.call(person2)() // 'person2'
person1.obj.foo2().call(person2) // 'obj'
 
```

#### 8.4 题目四

来看看这里会打印出什么呢？

```javascript
function foo() {
  console.log( this.a );
}
var a = 2;
(function(){
  "use strict";
  foo();
})();
 
```

答案并不是`undefined`，也不会报错，而是打印出了`2`。

哈哈😄，其实这里是有一个迷惑点的，那就是`"use strict"`。

我们知道，使用了`"use strict"`开启严格模式会使得`"use strict"`以下代码的`this`为`undefined`，也就是这里的立即执行函数中的`this`是`undefined`。

但是调用`foo()`函数的依然是`window`，所以`foo()`中的`this`依旧是`window`，所以会打印出`2`。

如果你是使用`this.foo()`调用的话，就会报错了，因为现在立即执行函数中的`this`是`undefined`。

或者将`"use strict"`放到`foo()`函数里面，也会报错。


## 总结

1. this 指向的是 call 一个函数时，传入的第一个参数。不传则为window对象。
2. 如果函数调用的形式不是 call ，转换为 call 形式。