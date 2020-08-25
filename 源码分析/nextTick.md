nextTick源码

nextTick是Vue的一个核心功能，源码加上注释也才100+行，很适合新手尝试阅读。

查看文档，看看nextTick功能：

> 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

解决比如修改数据后，不能马上获取DOM的问题。

官方文档用法：

```javascript
// 修改数据
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})

Vue.nextTick()
  .then(function () {
    // DOM 更新了
  })
```



需要实现这个功能，需要对Event Loop有一些了解。

宏任务和宏任务之间会经历渲染。实现nextTick只需要创建一个宏任务来专门执行需要的一个或多个回调就行。

```javascript
let callbacks = []
let pending = false

function nextTick (cb) {
    callbacks.push(cb)

    if (!pending) {
        pending = true
        setTimeout(flushCallback, 0)
    }
}

function flushCallback () {
    pending = false
    let copies = callbacks.slice()
    callbacks.length = 0
    copies.forEach(copy => {
        copy()
    })
}
```

### 知识点

#### 异步锁

里面添加了一个pending，异步锁，作用是只用一个宏任务就可以执行所有的回调。相当于第一个callback执行时创建宏任务，剩下的callback都在当前宏任务里执行。

> macroTask->macroTask(callback1,callback2,callback3)

如果不使用异步锁的效果:

> macroTask->macroTask(callback1)->macroTask(callback2)->macroTask(callback2)

使用异步锁，效率更高。

#### 能力检测

源码灵魂就是在于兼容性和性能优化。通过检测当前环境的能力来使用不同方式运行回调。

优化思路：可以使用微任务替换宏任务，微任务的效率比宏任务好，但是兼容性低。所以需要在能使用微任务的地方使用，实在不行再使用宏任务。

```javascript
//优化后的flushCallback
let timerFunc 

//优化开始

//如果支持Promise,就使用promise执行回调
if (typeof Promise !== 'undefined' && isNative(Promise)){
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    //ios debug
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} 
//如果支持MutationObserver,就使用MutationObserver执行回调
else if (!isIE && typeof MutationObserver !== 'undefined' && ( isNative(MutationObserver) || MutationObserver.toString() === '[object MutationObserverConstructor]' )) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} 
//如果IE，使用setImmediate,就使用setImmediate执行回调
else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
}
//实在不行再使用宏任务执行回调
else {
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
  
//如果不存在回调并且支持promise返回一个空promise
if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
     _resolve = resolve
    })
  }
}
```

完整源码：

```javascript
import { noop } from 'shared/util'
import { handleError } from './error'
import { isIE, isIOS, isNative } from './env'

//是否正在使用微任务
export let isUsingMicroTask = false

const callbacks = []

// 异步锁
let pending = false

// 优化后的回调队列
let timerFunc 

// 优化开始

// 如果支持Promise
if (typeof Promise !== 'undefined' && isNative(Promise)){
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    //ios debug
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} 
// 如果支持MutationObserver
else if (!isIE && typeof MutationObserver !== 'undefined' && ( isNative(MutationObserver) || MutationObserver.toString() === '[object MutationObserverConstructor]' )) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} 
// 如果IE，使用setImmediate
else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
}
// 实在不行再使用宏任务
else {
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}

export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve  
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  
  if (!pending) {
    pending = true
    timerFunc()
  }
  
  // 如果不存在回调并且支持promise返回一个空promise
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}

// 回调队列
function flushCallbacks () {
  // 重置异步锁
  pending = false
  // 防止nextTick包含nextTick的情况出现，执行前先重置备份
  const copies = callbacks.slice(0)
  // 清空回调
  callbacks.length = 0
  // 遍历运行回调队列
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}


```


### 总结
+ nextTick的目的是在dom更新后进行回调以完成对应操作。
+ 使用异步锁，一次性运行回调列表。
+ 对不同运行环境进行能力检测，以达到源码级别的效率

以上就是nextTick的源码解析。