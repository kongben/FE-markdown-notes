### 什么是new？

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

做了下面几件事

- **`new` 创建出来的实例myDog继承了Dog的属性和方法**
- **`new` 创建出来的实例myDog可以访问到构造函数原型链中的属性，也就是把Dog的原型链接上了**
- **如果构造函数有返回就返回**

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
return res ? instanceof Object ? res : obj
```

### 手写new

```js
function _new(fn,...args){
  let obj = Object.create(fn.prototype)
  const res = fn.apply(obj,args)
  return res instanceof Object ? res : obj
}
```