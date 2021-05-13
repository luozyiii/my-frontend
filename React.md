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


