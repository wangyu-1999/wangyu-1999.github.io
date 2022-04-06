---
layout: post
category: save
title: ProseMirror 笔记
---

接触 ProseMirror 有一段时间了，稍微把收藏夹里面的相关站点博客整理一下。

### [What’s different about the new Google Docs?](https://drive.googleblog.com/2010/05/whats-different-about-new-google-docs.html)

这个是 Google Doc 在 2010 年的一篇文章，主要介绍为什么要放弃 editable HTML 方案开发自己的在线文档处理，文章说明了原有方案的便捷性和问题，主要的问题可以概括为编辑器的特性和行为必须基于浏览器提供的接口来做，同时也会受到浏览器自身 bug 的影响，所以 Google 开发了一套完全基于 JavaScript 的文档处理引擎。

### [ContentEditable 困境与破局](https://zhuanlan.zhihu.com/p/123341288)

一篇对于现有 contentEditable 的文本编辑器的梳理文章，写的比较通俗易懂，可以拿来上手。

对于 ProseMirror 的原理比官方文档讲的还要简洁清楚一些，可以拿来当作入门的文档。

### [Why ContentEditable is Terrible](https://medium.engineering/why-contenteditable-is-terrible-122d8a40e480)

“所见即所得”（WYSIWYG）需要满足的三条公理：

1. 有一个良好的 DOM 到视图的映射。
2. 有一个良好的 DOM 选区到视图选区的映射。
3. 视图-操作-新视图，这个其中的所有视图应该在一个封闭的集合中。

文章基于上面三条公理提出了一些 ContentEditable 存在的问题。

关于公理 1 存在以下的问题：

- 唯一的视图可能同时可以对应多个 DOM 映射。
- 编辑器中可能存在一些不可视字符或者空的 &lt;span&gt; 标签，导致用户看似对同样的视图使用了同样的操作，但得到的结果完全不一样。

关于公理 2 存在以下的问题：

- 视图选区到 DOM 选区的映射是多对多的（比破坏公理 1 的那个多对一还糟糕...）

例如一段文本：

> his name was <strong><em>Baggins</em></strong>

本身就有多个 DOM 结构和它对应了，即使我们确定它的 DOM 结构是：

> his name was &lt;strong&gt;&lt;em&gt;Baggins&lt;/em&gt;&lt;/strong&gt;

当光标选中 Baggins 前面时候，它仍然有三种情况：在 strong 标签前面；在 em 标签前面；在字母 B 前面。

由此造成的结果就是输入新的字符是斜体加粗字符？还是加粗字符？还是没有特殊样式的字符。

- 光标选择在一个段落的结尾，与下一个段落的开头，实际上它们在 DOM 选区上是等价的。

关于公理 3 存在以下的问题：

- cointentEditable 在不同浏览器下的不同表现。

后续主要就是作者关于这些问题打补丁的方案，基本上也是类似 ProseMirror 这样框架设计的理论基础。

### [从流行的编辑器架构聊聊富文本编辑器的困境](http://yoyoyohamapi.me/2020/03/01/%E4%BB%8E%E6%B5%81%E8%A1%8C%E7%9A%84%E7%BC%96%E8%BE%91%E5%99%A8%E6%9E%B6%E6%9E%84%E8%81%8A%E8%81%8A%E5%AF%8C%E6%96%87%E6%9C%AC%E7%BC%96%E8%BE%91%E5%99%A8%E7%9A%84%E5%9B%B0%E5%A2%83/)

基本上全文翻译了上面一篇文章，还指出了 beforeinput 和输入法合成事件的问题。

输入这个真的是天坑，不做就想不到和不理解的那种。

### [关于 ProseMirror 概念介绍的系列文章](https://marvinsblog.net/tags/prosemirror/)

来自一个名叫 Marvin‘s Blog 的个人博客，写的挺多概念解读的，ProseMiiror 的资料太难找了，把它也列在这里吧。

