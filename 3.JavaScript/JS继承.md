## JS继承

### 原型链的问题

```javascript
function superType() {
    this.arr = [1,2,3,4]
}

function subType() {}

subType.prototype = new superType()

let a = new subType()
a.arr.push(5)
console.log(a.arr) //[1, 2, 3, 4, 5]
let b = new subType()
console.log(b.arr)//[1, 2, 3, 4, 5]

```

可以看出来原型链继承有一个致命的缺点，当原型中包含引用值的时候，原型中的引用值会被所有子类实例公用。其次子类实例化时是不能给父类的构造函数传值的。所以基本上原型链不会单独拿出来使用。

### 借用构造函数继承

基本思路：在子类用调用父类构造函数。

```javascript
function superType(name) {
    this.name = name
    this.arr = [1,2,3,4]
    this.getName = ()=>{
        return this.name
    }
}

function subType() {
    superType.call(this,'subType')
}

let a = new subType()
a.arr.push(5)
console.log(a.arr) //[1, 2, 3, 4, 5]
let b = new subType()
console.log(b.arr)//[1, 2, 3, 4]

console.log(a.name) //subType
```

借用构造函数继承通过使用call或者apply方法使得__superType__的构造函数在__subType__的实例创建的新对象的上下文中执行了。解决了原型链的两个问题.

1.引用值被所有子类实例公用。

2.子类实例化的时候不能往父类传值的问题

#### 借用构造函数继承的问题

+ 必须在构造函数里面定义方法，以至于每个实例都有一份方法的拷贝，不能复用。
+ 子类无法使用父类原型上定义的方法，所以只能全部类都是用构造函数来继承。

### 组合继承

组合的原型链和构造函数的继承。

原型链继承的缺点不是属性值不能是引用值嘛，那我就把所有方法通过原型链来继承。

借用构造函数继承的缺点是方法都有多份拷贝和无法使用父类原型上的方法，那就只通过构造函数继承属性。



```javascript
function SuperType(name){
    this.name =name;
    this.arr = [1,2,3,4]
}
SuperType.prototype.sayName = function(){
    console.log(this.name)
}
function SubType(name,age){
    //借用构造函数继承属性
    SuperType.call(this,name);
    this.age = age
}

//原型链继承方法
SubType.prototype = new SuperType()

SubType.prototype.sayAge = function(){
    console.log(this.age)
}

let a = new SubType('a',17)
a.arr.push(5)
console.log(a.arr) //[1,2,3,4,5]
a.sayName() //a
a.sayAge() //17

let a = new SubType('b',27)
console.log(b.arr) //[1,2,3,4]
b.sayName() //b
b.sayAge() //27
```

上面代码创建的两个`SubType`实例`a`和`b`都有自己的属性和共享的方法。

组合继承弥补了原型链和借用构造函数继承的缺点，是Javascript使用最多的继承模式。

#### 缺馅

在子类构造函数被调用时

```javascript
let a = new SubType('a',17)
```

父类构造函数被调用一次

```javascript
SuperType.call(this,name);
```

父类构造函数在创建子类原型时又被调用一次

```javascript
SubType.prototype = new SuperType()
```

继承功能实现，但是造成性能上的浪费。

有两组sh

### 寄生继承

本质上，组合继承，子类原型最终是要包含父类的所有实例属性的，子类构造函数子需要在执行时重写自己的原型就行了。

