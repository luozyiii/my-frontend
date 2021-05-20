#### 1.`React` 中 `keys` 的作用是什么？
>Keys 是 React 用于追踪List中元素被修改、被添加或者被移除的`辅助标识`，类似于数据库中的索引或者ID，可以快速找到list中改变的元素，节省开销。
>它是一个`特殊的属性`，不是给开发者使用，而是React自己使用。
>元素的`key`在其同级元素中具有唯一性。
>`key`的设计有点像`空间换时间`，多写了一点代码，却大幅度提升了`性能`。比如：我们需要给对象添加一些额外的属性，用来提高某些操作的性能以及便捷性。

```javascript
render () {
  return (
   ‹ul›
     {
      this.state.list.map(({item, key}) =› {
        return ‹li key={key}›{item}‹/li›
      })
     }
    ‹/ul›
  )
}
```

尽管key看起来像props的一部分，可事实上我们无法通过`this.props.key`获取到key值；React会在`判断元素更新的时候自动使用`key。
在 `React Diff` 算法中 `React` 会借助元素的 `Key` 值来判断该元素是最近创建的还是被移动而来的元素，从而减少不必要的元素重渲染。此外，`React` 还需要借助 `Key` 值来判断元素与本地状态的关联关系，因此我们绝不可忽视转换函数中 `Key` 的重要性。


#### 2.当你调用 `setState` 的时候，发生了什么事？

将传递给 `setState` 的对象合并到组件的当前状态，这将启动一个和解的过程，构建一个新的 `react` 元素树，与上一个元素树进行对比（ `diff` ），从而进行最小化的重渲染。

#### 3.我应该如何更新那些依赖于当前的 state 的 state 呢？

给 `setState` 传递`一个函数`，而不是一个对象，就可以确保每次的调用都是使用最新版的 state

#### 4.给 setState 传递一个对象与传递一个函数的区别是什么？
传递一个函数可以让你在函数内访问到当前的 state 的值。因为 setState 的调用是分批的，所以你可以链式地进行更新，并确保它们是一个建立在另一个之上的，这样才不会发生冲突：

```javascript
incrementCount() {
  this.setState((state) => {
    // 重要：在更新的时候读取 `state`，而不是 `this.state`。
    return {count: state.count + 1}
  });
}

handleSomething() {
  // 假设 `this.state.count` 从 0 开始。
  this.incrementCount();
  this.incrementCount();
  this.incrementCount();

  // 如果你现在在这里读取 `this.state.count`，它还是会为 0。
  // 但是，当 React 重新渲染该组件时，它会变为 3。
}
```

#### 5.状态(`state`)和属性(`props`)之间有何区别

`props`（“properties” 的缩写）和 `state` 都是`普通的 JavaScript 对象`。
它们都是用来保存信息的，这些信息可以控制组件的渲染输出，而它们的一个重要的不同点就是：
`props` 是`传递给组件`的（类似于函数的形参），而 state 是在`组件内`被组件自己管理的（类似于在一个函数内声明的变量）。

#### 6.`React` 中的三种构建组件的方式？

`React.createClass()`、`ES6 class` 和无状态函数（函数组件）。
函数组件在16.8版本拥有hooks新特性，拥有了state

#### 7.React 中 refs 的作用是什么？
Refs 提供了一种方式，允许我们访问DOM节点或者render方法中创建React 元素

核心：createRef 和 forwardRef

何时使用Ref ？
下面是几个适合使用 refs 的情况：

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库。

举个例子，避免在 Dialog 组件里暴露 open() 和 close() 方法，最好传递 isOpen 属性。（？？？）

#### 8.React Diff 的原理
- 传统diff算法（对比A、B树的差异）
>传统的diff算法，需要遍历整棵树的节点然后进行比较，是一个深度递归的过程，运算复杂度常常是O(n^3)，这样的低效率在react中肯定是不能接受的，那么react对于diff算法进行了哪些优化呢？
例子：展示 1000 个元素则需要 10 亿次的比较。这个开销实在是太过高昂。

- React 在两个假设的基础上提出了一套O(n)的算法（React Diff）
1.两个不同类型的元素会产生出不同的树；
2.开发者可以通过设置 key 属性，来告知渲染哪些子元素在不同的渲染下可以保存不变；

- Diff 算法的实现
比较类型 => 类型一样 => 比较属性 => 记录差异patch

##### 对比不同类型的元素
>当根节点为不同类型的元素时，React 会拆卸原有的树并且建立起新的树。

##### 对比同一类型的元素
>当对比两个相同类型的 React 元素时，React 会保留 DOM 节点，仅比对及更新有改变的属性。

##### 对比同类型的组件元素
当一个组件更新时，组件实例会保持不变，因此可以在不同的渲染时保持 state 一致。React 将更新该组件实例的 props 以保证与最新的元素保持一致，并且调用该实例的 UNSAFE_componentWillReceiveProps()、UNSAFE_componentWillUpdate() 以及 componentDidUpdate() 方法。

下一步，调用 render() 方法，diff 算法将在之前的结果以及新的结果中进行递归。

##### 对子节点进行递归
>默认情况下，当递归 DOM 节点的子元素时，React 会同时遍历两个子元素的列表；当产生差异时，生成一个 mutation。

##### 列表节点的比较
在渲染列表节点时，它们一般都有相同的结构，只是内容有些不同而已。

依据假设对于同一层次的一组子节点，它们可以通过唯一的 key 进行区分，通过给每个节点添加唯一的 key，可以极大的简化 diff 算法，减少对 DOM 的操作。列表节点的比较主要有添加节点、删除节点、排序三种场景进行：



