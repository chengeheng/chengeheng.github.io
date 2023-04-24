---
title: React Protal(传送门)
date: 2023-4-24 14:47:21
tag:
  - svg
categories:
  - 可视化
cover: https://img0.baidu.com/it/u=122675538,3473446391&fm=253&fmt=auto&app=120&f=JPEG?w=1422&h=800
top_img: https://img0.baidu.com/it/u=122675538,3473446391&fm=253&fmt=auto&app=120&f=JPEG?w=1422&h=800
---

正常的 JSX 书写，从外到内，父组件到子组件顺序，生成的 DOM 结构也是子组件的节点肯定在父组件里面的结构。而在某些情况下，需要我们在代码逻辑中写入的 JSX 渲染到跳出当前组件的 DOM 结构中，比如类似 antd 中 Modal 组件的全局提示框，我们写一个对话框（Dialog）组件：

```jsx
<div class="father">
  <div class="brother"></div>
  {showDialog ? <Dialog /> : null}
</div>
```

生成的 DOM 结构如下：

```html
<div class="father">
  <div class="brother"></div>
  <div class="dialog">Dialog</div>
</div>
```

对于用户来说，这个对话框应该是一个独立的组件，在所有元素的最外面，屏幕的最中间。而在页面的 DOM 结构树中，Dialog 组件被其他 dom 包围，因此就需要我们通过 CSS 的 position 属性控制 Dialog 的位置，还得要求从当前节点一直到 body 都没有其他的 position 是 relative 的干扰，这个风险肯定是很大的；而且 dialog 的其他样式也会收到父元素的干扰，不利于样式的编写。

## react 的解决方案--Portal

为了解决上面的问题，在 React16 之后的版本，React 提供了一种将子节点渲染到父组件以外的 DOM 节点的解决方案 Portal（传送门）。它可以指定 react 组件的渲染位置，跳出当前的 DOM 结构，因此常用来解决以下问题：

- 模态对话框
- 工具提示
- 悬浮卡
- 加载动画

使用的方法是通过`react-dom`这个库提供的`createPortal(child, container)`方法，它接受两个参数：

- child：需要渲染的子组件
- container：指定渲染组件的 dom 节点

```jsx
import ReactDOM from "react-dom";

const Dialog = ({ message, children }) => {
  return ReactDOM.createProtal(
    <div className="dialog">{children}</div>,
    domNode
  );
};
```

这样，就可以将 Dialog 组件渲染到我们指定的 domNode 中，比如 body 或者 root 下面。即使 Portal 是在父级元素之外呈现的，它的表现行为也跟平常我们在 React 组件中使用是一样的，同样可以接受 props 以及 context API，这是因为在 React Tree 中，Portal 还是在当前父组件下面。

## 注意点

使用 Portal 时我们需要注意这几个方面：

- 事件冒泡会正常工作--通过将事件传播到 React 树的根节点，事件冒泡将按照 react tree 的结构正常触发，与最终 DOM 中 portal 的位置无关
- Portal 节点的生命周期同正常组件一样
- Portal 只影响 DOM 结构
- 预定义的 HTML 挂载点--使用 React Portal 时，需要提前定义一个 HTML DOM 元素作为 Portal 组件的挂载点

## 总结

在我们需要在正常 DOM 层次结构之外呈现子组件而又不通过 React 组件树层次结构破坏事件传播的默认行为时，React Portal（传送门）会派上用场。比如在渲染模态框，工具提示，弹出消息之类的组件时，这很有用。
