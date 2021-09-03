---
layout: post
category: star
title: 笔记：How browers work
---

# [How browers work](http://taligarsiel.com/Projects/howbrowserswork1.htm)

这是一篇非常经典的解释浏览器工作原理的论文，网上很多相关的文章都是翻译之后再演绎的结果，今天花点时间读一下原文，写一下相关的笔记。笔记只会记录我读完每个部分觉得有必要记录的东西，而不会翻译文稿，所以希望更详细的了解可以去读原文。

# 目录

- [概述](#概述)
- [编译](#编译)
- [建立 render 树](#建立render树)
- [布局和绘制](#布局和绘制)

---

# [概述](#概述)

这一部分需要知道的是浏览器的工作大致可以分为四个阶段，解析 DOM 树和样式树->根据两者生成 render 树->计算布局->绘制

有一点很多文章可能忽略了，就是这个步骤并不是线性的进行的，并不是等全部 HTML 解析完成之后才进行下一步，浏览器会尽可能快的展示内容，所以会先让其一部分内容展示出来，其他的部分可能还在前面的步骤。原文是这么描述的：

> It's important to understand that this is a gradual process. For better user experience, the rendering engine will try to display contents on the screen as soon as possible. It will not wait until all HTML is parsed before starting to build and layout the render tree. Parts of the content will be parsed and displayed, while the process continues with the rest of the contents that keeps coming from the network.

---

# [编译](#编译)

这一章节主要先介绍了一下编译的过程，有编译原理基础的同学可以跳过介绍编译的部分。

需要注意的是，HTML 不能采用常规的自上而下的分析或者自下而上分析。

- HTML 不是上下文无关语法，这决定大多数语言的编译器无法发挥作用。
- 同时相比 XML 有着较为宽松的语法风格，为了方便网页编写者，文档中会对一些不符合规范的写法兼容，这导致 XML 的编译器也无法发挥作用。
- 除此之外，多数编译器默认代码段是不会发生变动的，而在 HTML 中，document.write 方法使得在编译过程中也可以修改代码。

HTML 编译词法分析的过程就是通过一个 DFA 来识别各个 token 的属性，是开始、文本或者是结束；建 DOM 树的过程则是利用一个类似栈的结构来看是否匹配。

还有一点在很多文章中被误传的就是加载过程的分析。当 HTML、CSS 和 JS 同时需要加载的时候。JS 阻塞 HTML 没有问题，但是 CSS 阻塞 JS 这个过程 Firefox 和 Webkit 处理的方式不同，Firefox 是先要求加载 CSS 再加载 JS,但是 Webkit 只在发生冲突时才阻塞。原文如下:

> It seems to be an edge case but is quite common. Firefox blocks all scripts when there is a style sheet that is still being loaded and parsed. Webkit blocks scripts only when they try to access for certain style properties that may be effected by unloaded style sheets.

---

# [建立 render 树](#建立render树)

在建立 render 树的时候，有一点容易被忽视，那就是浮动元素和绝对定位的元素会安排在特殊的地方，render 树原本的地方只会安排一个占位框（ frame ）。

建立 CSS 的段落理解的不是非常清楚，但是大致过程应该是首先需要建立一个 CSS 的规则树，然后从上到下确认每个部分使用的样式。确定之后按照 id、class 等等建立几个 map，然后查询的时候从右向左去 map 里面查。

当样式发生冲突时，就按照优先级规则来处理。

---

# [布局和绘制](#布局和绘制)

这部分就写的没前面那么详细了，稍微总结一下重要的点。

首先是在重排的事情上，实际上是存在全局重排和局部重排的概念的，一个 render 节点中存在一个标记位( dirty bit )，当触发重排时，会根据情况不同仅仅修改自己的标记位或者同时修改自己和子孙元素的标记位。

在计算布局的时候，往往遵循一个固定的步骤：

1. 计算父元素的宽度
2. 递归计算子元素，主要计算子元素的高度
3. 根据子元素高度计算自身高度
4. 设置 dirty bit

最后介绍了一下盒模型和 CSS 相关内容就结束了。

---

这篇文章总体而言有点虎头蛇尾，对于布局和绘制的部分讲的太简略了，但是前面的例子讲的很清楚，后面的东西之后有好的材料的话还要继续学习。
