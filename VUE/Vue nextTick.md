1.Vue中 dom更新是异步的。

>可能你还没有注意到，`Vue`异步执行`DOM`更新。只要观察到数据变化，`Vue`将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个`watcher`被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和`DOM`操作上非常重要。然后，在下一个的事件循环“`tick`”中，`Vue`刷新队列并执行实际 (已去重的) 工作。

在数据变化时，Vue的Watcher会调用微任务，如果被多次调用时会替换旧数据，当当前宏任务完成后，会调用Watcher的微任务，更新dom。所以在开发层面，修改了data后，在dom中是无法实时拿到更新后的数据，所以需要this.$nextTick()再用一个微任务，当data更新到dom后，再回调代码。以免出现拿不到对应的DOM的情况。
