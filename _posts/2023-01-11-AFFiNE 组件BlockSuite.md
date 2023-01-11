---
layout: post
category: star
title: AFFiNE 组件 - BlockSuite
---

Github 仓库地址：[BlockSuite](https://github.com/toeverything/BlockSuite)

# 介绍

* 在数据形式方面，BlockSuite 不是按照***事件源（event sourcing）***模式来实现的。而是采用一种***CRDT-based block tree***的数据形式实现的，这个能力由 [Yjs](https://github.com/yjs/yjs) 这个开源库来提供。通过这种数据结构，BlockSuite 具有了“时间旅行”和协同的能力，同时也具有***本地优先（local-first）***的特点。

> Event sourcing  的概念可以看这篇文章：[Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html) ，大致就是对于一个软件的状态改变转化为一系列的事件存储起来，然后不仅仅可以利用事件日志来查询，还可以还原过去的状态。
> 这种模式的核心在于软件的每一个改变需要用一个事件对象捕获，然后在软件的整个生命周期中，这些事件都会被存储在序列中。

> CRDT-based block tree 和 local-first 的概念可以参考这篇论文：[Local-First Software](https://martin.kleppmann.com/papers/local-first.pdf)，CRDT 全称 Conflict-free Replicated Data Type，大致含义就是解决协同中遇到数据格式冲突的问题，local-first 是指数据不存储在云端而是依赖本地，这两种模式相结合是协同软件流行的解决方案。


* 对于富文本编辑器而言，多种不同的节点在 BlockSuite 树中可以对应不同的富文本编辑组件，由此对应不同的 UI 组件来取代单一的 UI ，最终会导致无穷尽可变性的危险。
* 在渲染层面，BlockSuite 不假定内容只会经过 DOM 来渲染。不仅仅经过 Web 组件实现，同时可以支持混合 canvas-based 渲染作为白板内容的一部分。不同的渲染方式可以在同一个页面上协作，并且通过相同的存储结构来更新。

## 小结

 BlockSuite 不致力于成为一个插件式的富文本编辑框架，而是**鼓励在此基础上开发不同形式的协同程序。**最后，会考虑开源更多 BlockSuite 中的基础模块。

# 例子

* [Latest Playground](https://block-suite.pages.dev/?init)
* [AFFiNE Alpha Editor](https://pathfinder.affine.pro/)
* [Multiple Workspace Example with React](https://blocksuite-react.vercel.app/)



