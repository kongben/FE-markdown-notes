#  1.HTML
### 1.1什么是盒子模型？

1. 标准盒子模型

   宽度width = 内容(content)

2. IE盒子模型

   宽度 = 内容（content）+ border + padding

3. 相互转换

   标准盒子

   ```css
    box-sizing:content-box; 
   ```

   IE盒子

   ```css
   box-sizing:border-box;
   ```

# 2.CSS 
### 1.如何实现三栏布局？

```html
 <div class="box">
        <div class="left"></div>
        <div class="center"></div>
        <div class="right"></div>
 </div>
```

1. flex布局

   ```css
           .box{
               display: flex;
               height: 200px;
           }
           .left{
               background-color: aqua;
               width: 100px;
           }
           .center{
               background-color: red;
               flex-grow: 1;
           }
           .right{
               width: 100px;
               background-color: blue;
           }
   ```

2. 绝对定位布局

   ```css
   				.box {
               position: relative;
               height: 200px;
           }
   
           .left {
               position: absolute;
               left: 0;
               width: 100px;
               height: 100%;
               background-color: aqua;
           }
   
           .center {
               position: absolute;
               left: 100px;
               right: 100px;
               height: 100%;
               background-color: red;
           }
   
           .right {
               position: absolute;
               right: 0;
               width: 100px;
               height: 100%;
               background-color: blue;
           }
   ```

3. float布局

   ```css
   				.box {
               height: 200px;
           }
   
           .left {
               width: 100px;
               height: 100%;
               float: left;
               background-color: aqua;
           }
   
           .center {
               right: 100px;
               height: 100%;
               background-color: red;
           }
   
           .right {
               width: 100px;
               height: 100%;
               float: right;
               background-color: blue;
           }
   ```

   ```html
   <div class="box">
           <div class="left"></div>
           <div class="right"></div>
           <div class="center"></div>
   </div>
   ```

### 2.如何垂直水平居中？

1. 通过positon和margin-left和margin-right居中(必需知道自身长度)

   ```css
   						position: relative;
               height: 50px;
               width: 100px;
               top: 50%;
               left: 50%;
               margin-left: -50px; //自身宽度的一半
               margin-top: -25px; //自身高度的一半
   ```

2. 通过positon和margin-left和margin-right居中(必需知道自身长度)

   ```css
   position: relative;
   background-color: yellow;
   top: 50%;
   left: 50%;
   transform: translate(-50%,-50%)
   ```

   transform: translate 和margin-left、margin-top的区别在于transform: translate可以不需要知道自身的长宽自动计算，而margin不能自动计算。

3. 通过flex(设置在父元素上)

   ```css
   display: flex;
   align-items: center; //垂直轴居中
   justify-content: center; //水平轴元素居中
   ```

### 3.关于BFC？

1. 什么是BFC？

   BFC是**块格式化上下文**的简称。即一个元素如果具有BFC，那其内部元素无论如何都不会影响外部元素。仿佛有结界一般。

2. 如何产生BFC？

   - html元素
   - float不为none
   - overflow值为auto、scroll、hidden
   - display值为table-cell、table-caption和inline-block
   - position值不为relative和static

### 4.选择器优先级

分A、B、C、D四个等级

- A代表**内联样式** 有+1，没有则为0
- B代表**ID选择器**出现的次数
- C代表**类选择器**、**伪类**、**属性选择器**出现的次数
- D代表**标签选择器**、**伪元素**出现的总次数

从A开始依次比较，数字大的优先级高。若全部相等，之后的会覆盖之前的。

为了覆盖内联样式而产生的**!important**,如果不是为了覆盖内联样式，请尽量不要使用**!important**

### 5.浏览器储存及区别

|   分类   | localStorage | sessionStorage |          Cookie          |
| :------: | :----------: | :------------: | :----------------------: |
| 生命周期 |     永久     |    关掉页面    |       设置过期时间       |
| 储存大小 |     5MB      |      5MB       |           4KB            |
| 应用场景 |   长期登陆   | 一次性敏感登陆 | 跟随请求发给服务器做判断 |

### 6.如何隐藏一个元素

1. display-不占据空间

   ` display: none;`

2. width为0-不占据空间

   ```css
   width: 0;
   height: 0;
   font-size: 0
   ```

3. visibility-占据空间

   ` visibility: hidden;`

4. opacity-占据空间

   `opacity: 0;`

5. position-占据空间

   ```css
   position: relative;
   left: -9999px;
   top: -9999px;
   ```

6. rotate-占据空间

   `transform: rotateX(90deg)`

7. scale-占据空间

   `transform: scale(0)`

### flex布局

### 层叠上下文

### 响应式布局
### 如何清除浮动?
### display可以取那些值
### 如果创建三角形？
### 如何实现0.5px border？
### rem的优缺点
### css预处理器
### css动画

# js
### 1.原型对象和原型链

1、什么是构造函数？

在js里，使用__new__关键词调用的函数就是构造函数。

2、为什么要用构造函数？

可以预设某些参数，达到封装的效果。

```javascript
//没有封装
var p1 = { name: 'zs', age: 6, gender: '男', hobby: 'basketball' };
var p2 = { name: 'ls', age: 6, gender: '女', hobby: 'dancing' };
//通过构造函数模式封装
function Person(name,age,gender,hobby){
  this.name = name
  this.age = age
  this.gender = gender
  this.hobby = hobby
  this.getName = function(){
    return this.name
  }
}
let p1 = new Person('zs',6,'男','basketball')
p1.getName = function(){return this.age}
p1.getName() //6
let p2 = new Person('ls',6,'女','dancing')
p2.getName() //ls
```

3、什么是原型对象(prototype)？

函数(包括构造函数)天生自带一个prototype属性，他是一个指针，指向的是一个对象。

```javascript
let p1 = new Array()
p1.prototype //返回的是一个对象
```

4、为什么需要原型对象(pototype)？

```javascript
function Person(name,age,gender,hobby){
  this.name = name
  this.age = age
  this.gender = gender
  this.hobby = hobby
  this.getName = function(){
    return this.name
  }
}
let p1 = new Person('zs',6,'男','basketball')
p1.getName = function(){return this.age}
p1.getName() //6
let p2 = new Person('ls',6,'女','dancing')
p2.getName() //ls
```

上述代码每个Person实例对象都有一份__getName__方法的拷贝，在程序上是不划算的，所以引入的prototype【原型对象】可以把这个对象当作一个公共的区域，所有同一个类的实例对象都可以访问到这个原型对象，我们可以将对象中共有的内容，统一设置到原型对象中。

```javascript
function Person(name,age,gender,hobby){
  this.name = name
  this.age = age
  this.gender = gender
  this.hobby = hobby
}
Person.prototype.getName = function(){
    return this.name
  }
let p1 = new Person('zs',6,'男','basketball')
p1.getName() //zs
let p2 = new Person('ls',6,'女','dancing')
p2.getName() //ls
//修改person的getName方法
Person.prototype.getName = function(){
    return this.name+this.age
  }
p1.getName() //zs6

```

5、什么是原型链？

对于一个构造函数A，继承自一个构造函数B，现在A的实例对象想调用B构造函数定义的方法怎么办呢？所以js内置对于所有对象内置了一个属性 __ proto __ ，这个属性的指针指向__构造函数的prototype__,而构造函数也是对象，也有__ proto __ 属性指向构造函数的构造函数(一般是顶层是Object），所以规定顶层的Object的prototype是null，所以当一个实例对象要使用某一个方法时，如果本身找不到，会从自身的构造函数的prototype对象里面找，一层层的，直到找到Object.prototype对象里面，如果还没有就是null。上述过程就是原型链。

![原型图](https://images2015.cnblogs.com/blog/575868/201611/575868-20161116160959857-1841679069.png)

*需要注意（__函数是一等公民__）

+ Function.__ proto __  === Function.prototype //因为Function这个函数是由自身构造的。
+ Function.prototype.__ proto __ ===  Object.prototype // Function.prototype这个对象是有Object这个函数对象构造出来的
+ Object.__ proto __  === Function.prototype //因为在js中 内置的函数对象Object(__函数是一等公民__)所以是函数构造的

*注意点

对于一个构造函数function Foo(）来说，他的原型链是

Foo>Function.prototype>Object.prototype>null

对于这个构造函数的实例来说，他的原型链是

foo>Foo.prototype>Object.prototype>null

### 2.new一个对象的过程会发生什么？

例子

```javascript
let a = new Object()
```

1. 创建一个新对象obj

   ```javascript
   var obj = {};
   ```

2. 将obj的proto属性指向构造函数的prototype

2. ```javascript
obj.__proto__ = Object.prototype
  ```
  
3. 将使用obj调用构造函数，使this指向obj

   ```javascript
   Object.call(obj)
   ```

3. 返回构造函数返回的对象若无返回obj对象

###3.typeof和instanceof的区别

1、typeof的作用

  typeof的作用是判断参数是什么基本类型即"number"、"string"、"boolean"、"object"、"function" 和 "undefined"

```javascript
  typeof(1) //number
  typeof('1') //string
  typeof(true) //boolean
  typeof({}) //object
  typeof(function(){}) //function
  typeof(undefined) //undefined
```

2、instanceof的作用

  typeof的局限性就是不能判断引用类型的数据类型

```javascript
  typeof(new Array()) //object
```

  所以需要instanceof来做引用类型数据的判断，instance of是判断对象是否是一个对象的子类

```javascript
  let a = new Array()
  a instanceof Array //true
  a instanceof Object //true 因为Array是Object的子类
```

  也可以判断当前实例是否是某一个类的子类

```javascript
  function Foo(){} 
  function Aoo(){} 
  Foo.prototype = new Aoo();//JavaScript 原型继承 
  var foo = new Foo(); 
  foo instanceof Aoo //true
```

### 4.call和apply和bind？

1、为什么需要?

```javascript
  var a = {
      user:"汪某",
      fn:function(){
          console.log(this.user);
      }
  }
  var b = a.fn;
  b(); //undefined
```

  当前this只想为window，所以访问不了this.user,所以我们需要一个功能把this指向a这个作用域，这个工具就是call和apply和bind。

2、区别在于？

call和apply的区别

```javascript
  var a = {
      user:"汪某",
      fn:function(a,b){
          console.log(this.user+a+b);
      }
  }
  var b = a.fn;
  b.call(a,1,2) //汪某12
  b.apply(a,[1,2]) //汪某12
```

call与bind的不同

```javascript
var a = {
    user:"汪某",
    fn:function(a,b){
        console.log(this.user+a+b);
    }
}
var b = a.fn;
b.bind(a,1,2) // function(a,b){console.log(this.user+a+b);}
b.bind(a,1,2)() // 汪某12
        

```

即bind方法参数和call一致，直接传参但只是返回对应函数，而没有直接运行。

| 方法  | 参数                 | 调用                   |
| ----- | -------------------- | ---------------------- |
| call  | (object,arguments)   | 立即调用               |
| apply | (object,[arguments]) | 立即调用               |
| bind  | (object,arguments)   | 返回函数，稍后手动调用 |

### 5.关于this

1. 为什么需要this？

   ```javascript
   function getNameByThis(){
   	return this.name
   }
   function getNameByObjContext(obj){
   	return obj.name
   }
   let obj ={ name:'Tim' }
   
   //
   let a = getNameByThis.call(obj) //Tim
   let b = getNameByObjContext(obj) //Tim
   ```

   使用this可以隐式传递一个对象的引用，可以使API设计的更加优雅。而传递上下文对象当使用的模式越来越复杂的时候，会让代码越来越混乱。

2. 什么是this[this的指向]？

   this并不是见文知意的指向自身，而是根据调用的不同，决定this的指向。具体可以参考文章[this指向问题](https://zhuanlan.zhihu.com/p/71773266)

   根据《你不知道的javascript上册》总结出判断this

   - 如果函数的被**new**出来的，this指向这个歌新创建的对象。

   - 函数通过call、apply（显示绑定），this指向对应对象

   - 如果在某个上下文中（隐式绑定），则this指向这个上下文对象

     `let a = obj.foo() //this指向obj` 

   - 如果都不是的话，在严格模式是undefined，否则就是全局对象

### 6.闭包

1. 什么是闭包？

   当函数可以记住并访问所在词法作用域时，就产生了闭包。即便这个函数时在当前函数作用域之外调用。

   ```javascript
   function foo(){
   	let a = 1;
   	function bar(){
   		console.log(a)
   	}
     return bar
   }
   let baz = foo()
   baz() //1 闭包
   ```

   通常在foo()执行后，foo内部的作用域会被销毁（js的垃圾回收机制），而闭包阻止了销毁，foo内部的作用域依旧存在，因为bar()任然在饮用foo()的作用域。这个引用就称为闭包。

2. 闭包的缺点

   因为闭包阻止了销毁作用域，会造成内存泄漏

3. 闭包的作用

   实现模块暴露

   ```javascript
   function foo(){
     let something = 'cool'
     function doSomething(){
       return something
     }
     function doAnother(){
       ...
     }
       return {
         doSomething:doSomething,
         doAnother:doAnother
       }
   }
   let foo = foo() //创建模块实例
   foo.doAnother() //调用执行暴露出来的方法
   foo.doSomething() //调用执行暴露出来的方法
   ```

### 7.防抖函数和节流函数

1. 防抖函数

   **防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。**

2. 节流函数

   **指连续触发事件但是在 n 秒中只执行一次函数**

### 作用域链

### js对象

### js继承

### 宏任务和微任务是什么？

### 事件循环

### [for..in和for..of的区别](https://www.jianshu.com/p/c43f418d6bf0)

### array去重

### XMLHttpRequest原生方法

### [深拷贝和浅拷贝](https://zhuanlan.zhihu.com/p/48269135)

### 立即执行函数

### 同源策略和跨域问题

### 如何判断两个对象相等

### 隐式类型转换

### setInterval，setTimeout

### 函数式编程

### 函数柯里化


# DOM
### DOM操作
### 事件委托和代理
### 冒泡和捕获

# ES6
### 1.Promise
1. 什么是Promise？

   promise-诺言，会给一个承诺，承诺在异步操作完成后能根据异步任务完成与否执行对应操作。

   ```javascript
   var promise1 = new Promise(function(resolve, reject) {
     setTimeout(function() {
       resolve('foo');
     }, 300);
   });
   
   promise1.then(function(value) {
     console.log(value);
     // expected output: "foo"
   });
   ```

   上面代码，有一个Promise对象,使用then后会在300ms后接受到一个value。

2. 为什么需要promise？

   1. 通过链式调用，可以避免回调地狱（回调函数里面套回调函数）
   2. 可以并行处理异步任务。（通过all，race等方法）

3. 如何使用Promise？

   1. Promise参数是一个函数，这个函数有两个参数resovle和reject。

      then和catch方法是返回一个新的Promise对象。

   2. reject和resolve是什么？

      promise有三种状态，__pending__（等待），__resolve__（解决）是异步操作后把成功的结果传递出去，相反如果异步操作报错，就用__reject__（拒绝）传递。当状态从pending到resovle就可以使用primise实例对象的then来承接resovle传递的值，同理用catch来承接reject的值。

   3. all,race这个两个方法是什么？

      需要并行处理任务时使用，区别在于all是等所有异步任务有了相应之后才能调用then，race是当任务有任何一个完成就是调用then。

      ```javascript
      var promise1 = new Promise(function(resolve, reject) {
        setTimeout(resolve, 3000, 'test');
      });
      var promise2 = 42;
      var promise3 = new Promise(function(resolve, reject) {
        setTimeout(resolve, 100, 'foo');
      });
      
      Promise.all([promise1, promise2, promise3]).then(function(values) {
        console.log(values);
      });
      // expected output: Array [3, 42, "foo"]
      ```

   4. Promise.resolve()和Promise.reject()

      其实只是封装了的语法糖

      ```javascript
      Promise.resolve(42).then(value=>{
      	console.log(value) //42
      })
      //等价于
      let promise = new Promise((resolve,reject)=>{
        resolve(42)
      })
      promise.then(value=>{
        console.log(value) //42
      })
      //reject同理
      ```

   5. 使用Promise封装ajax get请求的例子

      ```javascript
      function axios(url){
        return new Promise((resolve,reject)=>{
          let req = new XMLHttpRequest() || new ActiveXObject('Microsoft.XMLHTTP')
          req.open('GET',url,true);
          req.onload = function(){
             if (req.readyState == 4 && req.status == 200) {
                      resolve(req.response)
                  } else {
                      reject(Error(req.statusText))
                  }
          }
          req.onerror = function(){
            reject(Error('Network Error'))
          }
          req.send();
        })
      }
      axios(yourUrl).then(resolve=>{
        console.log(resolve);
      }).catch(reject=>{
        console.log(reject)
      })
      ```

      

### 2.箭头函数

1. 什么是箭头函数？

   箭头函数是ES6提供的一种定义函数的一种方式。

   ```javascript
   //ES5
   function add(a,b){
     return a+b
   }
   //ES6
   let add = (a,b)=>a+b
   add(1,2) //3
   ```

2. 怎么用？

   语法：

   ```
   (参数)=> { 函数体 }
   ```

   1. 当没有参数时

   ```
   ()=> 1
   ```

   2. 当只有一个参数时

   ```javascript
   a=> a
   ```

   3. 当有多个参数时

   ```javascript
   (a,b,c)=> a+b+c
   ```

   4. 当代码多余一行时需要使用__{}__包裹

   ```javascript
   (a,b)=>{
   	let c = a*b;
   	return c
   }
   ```

3. 注意事项

   1. 不能使用有new 因为箭头函数没有构造函数

   2. 没有原型即没有prototype对象

   3. __箭头函数没有this指向，是继承自定义时所处的对象。所以 this 对象就是定义时所在的对象，而不是使用时所在的对象。__

      ```javascript
      //es6
      function fn(){
        let f = ()=>{
          console.log(this)
        }
      }
      
      //转换成es5兼容代码
      function fn(){
        let _this = this
        let f = function(){
          console.log(_this)
        }
      }
      ```

      3.1 当箭头函数有外层函数时，this指向这个定义时的对象。

      ```javascript
      let obj ={
      	fn:function(){
          console.log(this === obj)
        }
      }
      obj.fn() //true
      ```

      3.2 当箭头函数没有外层函数时时，this指向全局对象。

      ```javascript
      let obj ={
      	fn:()=>{
          console.log(this === window)
        }
      }
      obj.fn() //true
      ```

      3.3 当箭头函数嵌套箭头函数时，不管嵌套多少层，都是指向最外层的对象

      ```javascript
      let obj ={
      	fn:function(){
            console.log('普通函数',this === obj)
            return ()=>{
              console.log('第一层箭头函数',this === obj)
              return ()=>{
                console.log('第二层箭头函数',this === obj)
                return ()=>{
                  console.log('第三层箭头函数',this === obj)
                }
              }
            }
        }
      }
      obj.fn()()()() 
      //普通函数 true
      //第一层箭头函数 true
      //第二层箭头函数 true
      //第三层箭头函数 true
      ```

   4. 箭头函数碰上call、apply、bind

      ```javascript
      window.name = 'window'
      obj={
      	name:'obj'
      }
      f1 = function(){
        return this.name
      }
      f2 = ()=> this.name
      
      f1.call(obj) //obj
      f2.call(obj) //window
      ```

### 声明提升和块级作用域和暂时性死区

### 解构赋值
### async/await
### Array方法
### Map
### Set
### class
### generator

# VUE全家桶
### 1. Vue2.0双向绑定原理

  vue.js 2.0 使用object.defineProperty进行数据劫持再结合发布者-订阅者模式，通过劫持对象所以属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

代码

  ```javascript
  <!DOCTYPE html>
  <html>
  <head>
      <meta charset="utf-8">
  </head>
  <body>
      <div id="test"></div>
      <div id="myapp">
          <input v-model="message" /><br>
          <span v-bind="message"></span>
      </div>
      <script type="text/javascript">
          var model = {
              message: ""
          };
  
          var models = myapp.querySelectorAll("[v-model=message]");
          for (var i = 0; i < models.length; i++) {
              models[i].onkeyup = function() {
                  model[this.getAttribute("v-model")] = this.value;
              }
          }
          //劫持对象属性 通知变化
          Object.defineProperty(model, "message", {
              set: function(newValue) {
                  watcher(newValue) //通知watcher变化
                  this.value = newValue;
              },
              get: function() {
                  return this.value;
              }
          })
  
          //watcher更新视图
          function watcher(newValue){
              var binds = myapp.querySelectorAll("[v-bind=message]");
                  for (var i = 0; i < binds.length; i++) {
                      binds[i].innerHTML = newValue;
                  };
                  var models = myapp.querySelectorAll("[v-model=message]");
                  for (var i = 0; i < models.length; i++) {
                      models[i].value = newValue;
                  };
          }
      </script>
  </body>
  </html>
  ```
### 2.v-for 为什么需要key

**就地复用**原则，当列表发生改变时可以通过key来判断当前渲染是否改变，来决定是否复用之前的渲染还是重新渲染。不能使用数组下标的原因是使用数组下标不能监听列表改变后那些值能复用。

​	例子

`[1,2,3,4,5]`

自动复用1，2，3，4，5

当在第二个中插入一个值

`[1,a,2,3,4,5]`

其中2，3，4，5的下标index从原本的1->2,2->3,3->4,4->5,导致2，3，4，5在列表中被重新渲染，可以正常使用，但是加大了开销。

### 3.vue watch和computed和methods的区别

1. computed和methods的区别

   computed和methods都可以计算data的

   ```html
   <div id="example">
     <p>Reversed message: "{{ reversedMessage() }}"</p>
     <p>Computed reversed message: "{{ reversedMessage }}"</p>
   </div>
   ```

   ```js
   var vm = new Vue({
     el: '#example',
     data: {
       message: 'Hello'
     },
     computed: {
       // 计算属性的 getter
       reversedMessage: function () {
         // `this` 指向 vm 实例
         return this.message.split('').reverse().join('')
       }
     }
     methods:{
     	methodsReversedMessage:function(){
     		return this.message.split('').reverse().join('')
   		}
   	}
   })
   ```

   区别在于computed会缓存之前的结果，如果message没有发生改变就不能计算，直接返回。methods则会每次调用的时候进行计算。

   **为何说呢吗需要缓存呢？**

   当计算量比较大的时候，会有比较大的性能开销。通过缓存，可以减少大量数据的运算。

2.  computed和watcher的区别？

   + computed有缓存

   + watcher是把需要依赖的值当作键，computed是把需要依赖其他值计算的值当作键。

   + watcher执行异步操作，或高性能消耗的操作，computed是计算结果，需要立马得到计算后的值

### 4.Vue生命周期

1. 什么是生命周期？

   Vue实例从创建到销毁的全过程，开始创建，初始化数据，编译模板，挂载dom，渲染，更新，渲染，卸载等一系列过程，成为Vue的生命周期。

2. 为什么需要生命周期？

   在从创建到销毁的整个过程中，会提供对应的钩子函数，可以使用这些钩子函数对Vue实例化过程中，实现功能的对应逻辑。

3. 有哪些生命周期钩子？

   beforeCreate->created->beforeMount->mounted->beforeUpdate->updated->beforeDestroy->destroyed

4. 何时使用对应生命周期钩子？

   - beforeCreate

     挂载元素 `$el`和数据对象`data`都为`undefined`，还未初始化。

   - created

     挂载元素 `$el`为`undefined`，数据对象`data`有值。

   - beforeMount

     `$el`有值，但挂载之前为虚拟的dom节点，`$el`里面的`message`还是{{ message }},还没有被替换。

   - mounted

     `$el`里面的值被替换成为对应data的值,vue实例挂载完成，data.message成功渲染。

   - beforeUpdate

     当data变化,虚拟dom渲染和匹配之前时，会触发beforeUpdate

   - updated

     当data变化,虚拟dom渲染和匹配之后时，会触发beforeUpdate

   - beforeDestroy

     在销毁watcher和子组件和事件监听函数之前

   - destroyed

     销毁了watcher和子组件和事件监听函数之后。

   ![生命周期图](https://cn.vuejs.org/images/lifecycle.png '生命周期图')

### Vue组件传值

### nextTick

### Vuex状态管理

### vue插槽

```

```



### SEO

### watch深度监听

### vue-router定义和获取

### vue-router路由权限

### vue-router hash和histroy模式及其根本区别

### Vue项目如何作权限管理

### ats树

# Node
### export和import
### webpack
### nginx 

# 手写代码
### 1.数组拍平

```javascript
flatten([1, 2, 3, [4, 5], [[6], 7]])
function flatten(arr){
    return arr.reduce((pre,cur)=>{
        if(Array.isArray(cur)){
            return [...pre,...flatten(cur)]
        }else{
            return [...pre,cur]
        }
    },[])
  }
```

### 实现call和apply、实现bind

### 实现继承
### 实现parseint
### 实现Promise
### 实现防抖和节流
### 手写深拷贝
### 手写Vue双向绑定
### 数组检测（新增的 减少的）

# 计算机网络
### 输入网站的那一刻计算机发生了什么？
### Content-type和formdata
### http三次握手
### http四次挥手
### http状态码
### keep-alive

#计算机相关
### 正则表达式
### 单元测试

# typescript