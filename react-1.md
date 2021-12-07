###  Hooks
##### 1、为什么不要在循环、条件语句或者嵌套函数中调用hooks
* react用链表来严格保证hooks的顺序
* Hooks 的本质其实是链表
* useState首次渲染的路径
  * mountState：这个过程初始化了一个hooks，并且将其追加到链表结尾
  * updateState： 依次遍历链表并渲染
* 循环、条件和嵌套函数是可能扰乱钩子(Hook)执行顺序的常见地方

##### 2、为什么只能在React函数组件中调用hooks
##### 3、React 事件与 DOM 事件有何不同
* React 的事件系统沿袭了事件委托的思想。
* 在 React 中，除了少数特殊的不可冒泡的事件（比如媒体类型的事件）无法被事件系统处理外，绝大部分的事件都不会被绑定在具体的元素上，而是统一被绑定在页面的 document 上。
* 当事件在具体的 DOM 节点上被触发后，最终都会冒泡到 document 上，document 上所绑定的统一事件处理程序会将事件分发到具体的组件实例。


### React应用性能优化
#### 函数组件的性能优化：React.memo 和 useMemo
1、 React.memo 会帮我们“记住”函数组件的渲染结果，在组件前后两次 props 对比结果一致的情况下，它会直接复用最近一次渲染的结果。如果我们的组件在相同的 props 下会渲染相同的结果，那么使用 React.memo 来包装它将是个不错的选择。
```
 React.memo(Component, areEqual);

```
* React.memo 会自动为你的组件执行 props 的浅比较逻辑
* React.memo 只负责对比 props

2、useMemo: 更加“精细”的 memo React.memo 控制是否需要重渲染一个组件，而 useMemo 控制的则是是否需要重复执行某一段逻辑
