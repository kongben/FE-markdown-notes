# 我知道的事件循环（event loop）

> 众所周知，js是一门单线程的语言，作为单线程语言为什么能够实现异步操作呢？

说起线程，就要提进程。一个浏览器应用是有多个进程。

![img](https://user-gold-cdn.xitu.io/2019/10/21/16ded5b21bd8abf4?w=790&h=222&f=png&s=76000)

包括主进程(Google Chrome)、第三方插件进程(Google Chrome helper)、GPU进程(GPU)、渲染进程（浏览器内核 Renderer）。

其中主要的是渲染进程，也就是浏览器内核。浏览器内核有多个线程其中包括

- GUI渲染线程
- js引擎线程
- 事件触发线程
- 定时器线程
- http请求线程

众所周知js（也就是js引擎）是单线程的。因为js可以操作DOM，为了避免渲染不可控，所以将js引擎个GUI渲染设计成互斥，一个运行，另一就必须挂起。所有有了不是那么众所周知规则：

> GUI渲染进程和js引擎线程是互斥的。

### js引擎是如何执行同步和异步任务？

同步任务是js引擎线程上顺序执行的任务，这样就形成了执行栈()。

![img](https://user-gold-cdn.xitu.io/2019/10/21/16ded6c26d178305?w=279&h=373&f=png&s=20459)

那异步任务呢？譬如定时器，http请求和事件呢？

当最开始代码执行到setTimeout之流时（同步任务），会运行对应 事件触发线程、定时器线程、http请求线程  当满足对应条件后产生一个回调函数，把callback推入到事件队列中。当执行了所有执行栈中的任务后, js引擎线程就空闲下来去读取事件队列。根据队列FIFO原则。往执行栈中推入，并开始运行。

![image-20191021164039046](/Users/juanpi/Library/Application Support/typora-user-images/image-20191021164039046.png)

### 什么事宏任务和微任务？

执行栈每次执行直到执行栈栈空为止，则称为一次宏任务。

又因为js引擎和GUI渲染时互斥的线程，所以为了让DOM和宏任务有序进行，会在宏任务结束后进行GUI渲染。

> 宏任务-->渲染-->宏任务-->渲染-->宏任务-->渲染

例子：￼￼

![image-20191021164439748](/Users/juanpi/Library/Application Support/typora-user-images/image-20191021164439748.png)

结果:

<center>只会显示grey，理由:同属于一次宏任务，导致渲染前GUI优化了改动。</center>
例子2:

![image-20191021164612815](/Users/juanpi/Library/Application Support/typora-user-images/image-20191021164612815.png)

结果：

<center>先blue然后迅速black，理由，属于不用宏任务，导致渲染了两次（但间隔很短）</center>
微任务：在当前宏任务执行完成后 ，渲染完成前，立即执行的任务。

#### 宏任务与微任务的种类

宏任务：

| #                     | 浏览器 | Node |
| --------------------- | :----: | ---: |
| I/O                   |   ✅    |    ✅ |
| setTimeout            |   ✅    |    ✅ |
| setInterval           |   ✅    |    ✅ |
| setImmediate          |   ❌    |    ✅ |
| requestAnimationFrame |   ✅    |    ❌ |

微任务：

| #                          | 浏览器 | Node |
| -------------------------- | :----: | ---: |
| process.nextTick           |   ❌    |    ✅ |
| MutationObserver           |   ✅    |    ❌ |
| Promise.then catch finally |   ✅    |    ✅ |

注：Promise，process.nextTick都属于微任务。但是同为微任务,在node中的process.nextTick的优先级比promise高。及不遵循FIFO，强行插队。

例子：

![image-20191021165107035](/Users/juanpi/Library/Application Support/typora-user-images/image-20191021165107035.png)

结果：

> 1
> 2
> 6
> 4
> 3
> 5

#### 总结

当js引擎线程执行当前所以执行栈的代码即为完成了一个宏任务。
碰到异步任务，就会交给对应的线程，当满足条件后，会把回调函数推入事件队列，等待执行栈下次推入执行。
碰到微任务，就将它添加到 微任务的任务队列中，当前宏任务完成后，就会运行微任务，再运行渲染线程。
再次执行执行栈的代码（如果有的话）

> 宏任务-->微任务-->渲染-->宏任务-->微任务-->渲染-->宏任务-->微任务-->渲染

![image-20191021165928269](/Users/juanpi/Library/Application Support/typora-user-images/image-20191021165928269.png)



### 习题

1.为什么是55555，答案是闭包，那么为什么会产生闭包呢？

```javascript
for(var i = 0; i < 5; i++){
    setTimeout(function after() {
        console.log(i);
    }, 0);
}
```

![image-20191021170207723](/Users/juanpi/Library/Application Support/typora-user-images/image-20191021170207723.png)

所以，上面代码等价于

```javascript
for(var i = 0; i < 5; i++){
    
}
//渲染
//下一个宏任务
console.log(i); //因为var 声明的i产生了闭包，所以输出5
console.log(i); 
console.log(i); 
console.log(i); 
console.log(i); 
```

2.那么async和await呢？

首先async 返回promise

```javascript
async function test() {
    console.log(1)
    return 1
}
// 等价于下面的代码
function test() {
   console.log(1)
   return new Promise(function(resolve, reject) {
       resolve(1)
   })
}
```

然后await,只能在async内部使用，其次使用await时，从右往左执行，遇到await时，会阻塞后面代码，直到完成全部同步任务时，再执行剩余代码，之后再执行微任务队列代码，再执行渲染。

> 宏任务-->await-->微任务-->渲染-->宏任务-->await-->微任务-->渲染

那么终极问题来了：

```javascript
async function async1() {
  console.log(1);
  await async2();
  console.log(2);
}
async function async2() {
  console.log(3);
}
console.log(4);
setTimeout(function() {
  console.log(5);
}, 0);
async1();
new Promise(function(resolve) {
  console.log(6);
  resolve();
}).then(function() {
  console.log(7);
});
console.log(8);
```

答案：

+ 4
+ 1
+ 3
+ 6
+ 8
+ 2
+ 7
+ 5