Vue响应式系统-就是当数据发生变化的时候，自动更新渲染。

**当我们讨论MVVM响应式原理时，我们在讨论什么？**

1. Vue是如何知道数据发生了改变？
2. 当数据发生改变时，怎么知道通知哪些视图更新？
3. 当数据发生改变时，视图怎么知道什么时候更新？

### 1.Vue是如何知道数据发生了改变？-defineProperty

答案：

通过Object.defineProperty，使用get和set两个钩子方法。在对象属性初始化或者发生改变时就会触发。

get 值是一个函数，当属性被访问时，会触发 get 函数

set 值同样是一个函数，当属性被赋值时，会触发 set 函数

**那么Vue是如何知道数据发生改变？**

Vue 在 属性的 set 方法中做了手脚，因而当数据改变时，触发 属性的 set 方法，Vue 就能知道数据有改变。

### 2.当数据发生改变时，视图怎么知道通知哪些视图更新？-依赖收集

简单说data 中的声明的每个属性，都会维护一个数组，保存着谁依赖（使用）了它。因为它知道谁依赖它之后，它就可以在发生改变的时候，通知依赖它的地方，让页面完成更新。

**watcher 是什么，每个 Vue 实例都会拥有一个专属的 watcher，可用于实例更新**

**总结一下**

1、data 中每个声明的属性，都会有一个 专属的依赖收集器 subs

2、当页面使用到某个属性时，页面的 watcher 就会被放到依赖收集器 subs 中

**什么时候进行收集依赖的呢？**

`Object.defineProperty -get`

当 页面 A 读取了 name 时，会触发 name 的 get 函数，此时，name 就会保存 页面A 的 watcher 啦！

**问题2:在数据改变时，怎么知道通知哪些视图更新？**

答案：通知那些存在 依赖收集器中的 视图

### 3.当数据发生改变时，视图怎么知道什么时候更新？-更新依赖

我们都知道，每个属性都会保存有一个 依赖收集器 **subs** ，而这个 依赖收集器，是用来在 数据变化时，通知更新。

**什么时候进行更新依赖的呢？**

`Object.defineProperty -set`

当 name 改变的时候，name 会遍历自己的 依赖收集器 subs，逐个通知 watcher，让 watcher 完成更新

这里 name 会通知 页面A，页面A 重新读取新的 name ，然后完成渲染

**问题3:Vue 在数据改变时，视图怎么知道什么时候更新?**

答案：在数据变化触发 set 函数时，通知视图，视图开始更新

### 总结

1、Object.defineProperty - get ，用于 依赖收集

2、Object.defineProperty - set，用于 依赖更新

3、每个 data 声明的属性，都拥有一个的专属依赖收集器 subs

4、依赖收集器 subs 保存的依赖是 watcher

5、watcher 可用于 进行视图更新

### 一个简易的Vue原理响应式源码

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>

</body>
<script>
    function Vue(data) {
        if (isObject(data)) {
            Object.keys(data).forEach(key => {
                defineReactive(data, key)
            })
        }
        return data
    }
    function defineReactive(data, key) {
        let val = data[key]
        //依赖收集器
        const dep = new Dep()

        Object.defineProperty(data, key, {
            // 依赖收集
            get() {
                dep.depend()
                return val
            },
            // 更新依赖
            set(newVal) {
                val = newVal
                dep.notify()
            }
        })

        // 递归data对象
        if (isObject(val)) {
            Vue(val)
        }
    }

    class Watcher {
        constructor(getter) {
            this.getter = getter
            this.get()
        }

        get() {
            saveWatcherThis(this)
            this.getter() //render funciton
        }

        update() {
            this.get()
        }
    }

    class Dep {

        constructor() {
            this.subs = new Set()
        }

        // 收集依赖
        depend() {
            //subs 收集依赖的数组
            // subs.add 收集依赖
            if (watcherThis) {
                this.subs.add(watcherThis)
            }
        }

        // 更新依赖
        notify() {
            this.subs.forEach(watcher => watcher.update())
        }
    }

    // 全局存起来的watcher引用
    let watcherThis = null

    //更新watcher引用
    function saveWatcherThis(_target) {
        watcherThis = _target
    }

    function isObject(obj) {
        return obj !== null && typeof obj === 'object'
    }
    const data = Vue({
        name: 'hello world'
    })
    new Watcher(() => {
        document.querySelector('body').innerHTML = `name is : ${data.name}`
    })
</script>

</html>
```

使用方法：

在控制台设置`data.name`的值，视图会跟着变化。形成响应式渲染。