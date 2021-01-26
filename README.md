# React 设计原理解密及核心源码解读

## 1.请简述 React 16 版本中初始渲染的流程

React 元素渲染到页面，分为两个阶段，render 阶段和 commit 阶段。

render 阶段负责创建 Fiber 数据结构并为 Fiber 节点打标记，标记当前 Fiber 节点要进行的 DOM 操作。

commit 阶段负责根据 Fiber 节点标记 ( effectTag ) 进行相应的 DOM 操作。

## 2.为什么 React 16 版本中 render 阶段放弃了使用递归

React 16 之前的版本比对更新 VirtualDOM 的过程是采用循环加递归实现的，这种比对方式一旦任务开始进行就无法中断，如果应用中组件数量庞大，主线程被长期占用，直到整棵 VirtualDOM 树比对更新完成之后主线程才能被释放，主线程才能执行其他任务。这就会导致一些用户交互，动画等任务无法立即得到执行，页面就会产生卡顿, 非常影响用户体验。 

## 3.请简述 React 16 版本中 commit 阶段的三个子阶段分别做了什么事情

第一个子阶段：调用类组件的 getSnapshotBeforeUpdate 生命周期函数

第二个子阶段：根据 effectTag 执行 DOM 操作

第三个子阶段：dom 操作已完成，所作的工作就是类组件处理生命周期函数和函数组件处理钩子函数

## 4.请简述 workInProgress Fiber 树存在的意义是什么

React 使用双缓存技术完成 Fiber 树的构建与替换，实现DOM对象的快速更新。

在 React 中最多会同时存在两棵 Fiber 树，当前在屏幕中显示的内容对应的 Fiber 树叫做 current Fiber 树，当发生更新时，React 会在内存中重新构建一颗新的 Fiber 树，这颗正在构建的 Fiber 树叫做 workInProgress Fiber 树。在双缓存技术中，workInProgress Fiber 树就是即将要显示在页面中的 Fiber 树，当这颗 Fiber 树构建完成后，React 会使用它直接替换 current Fiber 树达到快速更新 DOM 的目的，因为 workInProgress Fiber 树是在内存中构建的所以构建它的速度是非常快的。