##### 谈一下nextTick的实现原理？
* Vue.js在默认情况下，每次触发某个数据的 setter 方法后，对应的 Watcher 对象其实会被 push 进一个队列 queue 中，
在下一个 tick 的时候将这个队列 queue 全部拿出来 run（ Watcher 对象的一个方法，用来触发 patch 操作） 一遍。
* 因为目前浏览器平台并没有实现 nextTick 方法，所以 Vue.js 源码中分别用 Promise、setTimeout、setImmediate 等方式在 microtask（或是task）中创建一个事件，目的是在当前调用栈执行完毕以后（不一定立即）才会去执行这个事件。
* nextTick方法主要是使用了宏任务和微任务,定义了一个异步方法.多次调用nextTick 会将方法存入队列中，通过这个异步方法清空当前队列
1、首先会调用nextTick并传入cb
2、接下来会定义一个callbacks 数组用来存储 nextTick，在下一个 tick 处理这些回调函数之前，所有的 cb 都会被存在这个 callbacks 数组中
3、下一步会调用timerFunc函数
1）、 我们知道异步任务有两种，其中 microtask 要优于 macrotask ，所以优先选择 Promise 。因此这里先判断浏览器是否支持 Promise。
2)、 如果不支持再考虑 macrotask 。对于 macrotask 会先后判断浏览器是否支持 MutationObserver 和 setImmediate 。
3)、 如果都不支持就只能使用 setTimeout 。这也从侧面展示出了 macrotask 中 setTimeout 的性能是最差的。

##### Vue中computed是怎么实现的吗?
* 计算属性computed的本质是 computed Watcher，其具有缓存
1、initComputed 函数拿到 computed 对象然后遍历每一个计算属性。
2、判断如果不是服务端渲染就会给计算属性创建一个 computed Watcher 实例赋值给watchers[key]（对应就是vm._computedWatchers[key]）。
3、然后遍历每一个计算属性调用 defineComputed 方法，将组件原型，计算属性和对应的值传入。

https://juejin.cn/post/6844904138208182285
