### 什么是new？

类与构造函数和对象与实例。

在传统的OPP语言中，对象是一个实物的抽象，例如一只狗或者一个人，一本书。

那所谓**类就是对象的模版，对象就是类的实例。**

比如我们有一个类是Dog，可以实例化一个对象就可以是一个叫yck的狗。

而对于JavaScript来说，OPP并不是基于类（class），而是基于构造函数和原型链的。

简单来说，通过将抽象的类里面封装的属性和方法（Dog）赋予给实例化的对象（yck），就可以表达某一类实物（Dog）的共同特征。

那么如果**赋予**呢？

答案就是**new**

下面的🌰：

```
function Dog(name) {
  this.name = name
}
Dog.prototype.sayName = function () {
    console.log(this.name)
}
let myDog = new Dog('yck')
console.log(myDog.name) // 'yck'
myDog.sayName() // 'yck'
```
```
function Dog(name) {
  this.name = name
  return {name:name}
}
Dog.prototype.sayName = function () {
    console.log(this.name)
}
let myDog = new Dog('yck')
console.log(myDog) // {name:'yck’}
```

可以看出当js运行

`let myDog = new Dog()`

主要就是做了两件事，把类的属性赋予给实例，把类的方法赋予给实例。既然实例是一个对象，当然要返回一个对象。

实际上做了下面几件事

- 创建一个新对象
- 通过原型链的方式把新对象的____proto____指向构造函数（也就是类）的prototype，以达到实例可以调用类的方法
- 通过调用构造函数本身，并把构造函数的this指向新对象，来达到构造函数的属性，实例能够调用。
- 最后就是如果构造函数有返回值，就返回构造函数的返回值，如果没有就返回这个新对象。

形象点来说就是实例对象通过原型链获得了构造函数的方法的使用权，又通过运行构造函数并改变this指向实例对象的方式来拿到构造函数的属性。就这样通过模版，生成了无数多实例。

### 如何手写一个**new**操作符呢？

1.创建一个新对象

```
let obj = {};
```

2.将obj的proto指向构造函数的原型,可以继承Object的自己的若干属性和方法。

```javascript
obj.__proto__ = Dog.prototype
```

3.调用构造函数，并将构造函数的this指向obj，可以将实例参数赋值给属性。

```javascript
const res = Dog.apply(obj,args)
```

4.表面上已经完了，实际上new操作符还会返回构造函数返回对象，如果是null或者undefined，就返回obj。

```javascript
return res instanceof Object ? res : obj
```

### 手写new

```js
function _new(fn,...args){
  let obj = Object.create(fn.prototype)
  const res = fn.apply(obj,args)
  return res instanceof Object ? res : obj
}
```