---
layout: post
category: star
title: 读书笔记:JavaScript 函数式编程
---

书籍名称：

JavaScript 函数式编程

从 React 魅力四射的 hook 过来，之前备受 JavaScript 中不伦不类的面向对象的折磨，来看看 JavaScript 中函数式编程的实现，希望看完之后能对 hook 和无处不在的高阶组件加深一点理解。

拓展阅读：

[函数式编程指北](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/)

---

# 目录

- [JavaScript 函数式编程简介](#javascript-函数式编程简介)
- [一等函数与 Applicative 编程](#一等函数与-applicative-编程)
- [变量的作用域和闭包](#变量的作用域和闭包)
- [由函数构建函数](#由函数构建函数)
- [纯度与不变性与更改政策](#纯度与不变性与更改政策)
- [常用 Lodash 函数列表](#常用-lodash-函数列表)

---

## JavaScript 函数式编程介绍

本书采用的工具库是 Underscore，但是我查了一下发现这个工具库因为更新较慢的原因被逐渐弃用了，但是好消息是 Lodash 在 Underscore 基础上增加了许多新的 api，同时保持着良好的更新和维护。两个库的 api 基本一样，所以就打算用 Lodash 来替代 UnderScore，同时还会记录一下使用到的 Lodash 接口。

[_.toArray(value)](https://www.lodashjs.com/docs/lodash.toArray)：*超强的转换接口，支持对象、字符串、数字、空转换为数组。可以用来把 arguments 转换为数组*

[实现](https://github.com/lodash/lodash/blob/2f79053d7bc7c9c9561a30dda202b3dcd2b72b90/toArray.js)：*分别实现了类数组转数组、字符串转数组、迭代器转数组、map 转数组、set 转数组的方法，然后调用 getTag 检查类型，一波 if-else 解决问题*

> 函数式编程通过使用函数来将值转换成抽象单元，接着用于构建软件系统。

> 面向对象编程的主要目标是问题分解......与面向对象方法将问题分解成多组“名词”或对象不同，函数式方法将相同的问题分解成多组“动词”或函数。与面向对象编程类似的是，函数式编程也通过“黏结”或“组合”其他函数的方式来构建更大的函数，以实现更加抽象的行为。

> 一种将函数式的部件组成一个完整系统的方法，是取一个值，逐渐将它“改变”——通过一个原始的或组合的函数——变成另一个值。

[_.isString(value)](https://www.lodashjs.com/docs/lodash.isString)：*检查 value 是否是原始字符串String或者**对象**。*

[实现](https://github.com/lodash/lodash/blob/2f79053d7bc7c9c9561a30dda202b3dcd2b72b90/isString.js)：*字符串就直接 typeof，对象就先检查是不是对象，再看是不是空，再看是不是数组，再看标记是不是‘[object string]'*

注意这里是检查是不是字符串以及字符串对象，String()、new String()、Object()、new Object() 表示欣喜......

_.isArray 和 _isNaN 估计代码就一行所以没有单独写出来，可以参见这个博客：[[lodash.isArray](https://segmentfault.com/q/1010000038482379)]

[_.reduce](https://www.lodashjs.com/docs/lodash.reduce)：*可以对数组或者对象使用一个迭代函数，对数组的功能和 Array.prototype.reduce 一样，但是增加了对于对象的支持*

[实现](https://github.com/lodash/lodash/blob/master/reduce.js)：*首先判断传入的是数组还是对象，是数组的话调用 arrayReduce 方法，是对象的话调用 baseReduce 方法。[arrayReduce 方法](https://github.com/lodash/lodash/blob/2da024c3b4f9947a48517639de7560457cd4ec6c/.internal/arrayReduce.js#L12) 的原理是首先检查 array 是不是为空，然后看 accumulator 参数设置了没有，没设置的话把第一个数拿出来设置，然后遍历下标执行传入的迭代函数。[baseReduce 方法](https://github.com/lodash/lodash/blob/master/reduce.js) 的逻辑稍微复杂一点，就是首先对这个对象做判断，看是不是类数组，然后调用迭代方法对该对象做循环*

>“函数式编程”包括以下技术：
>
>- 确定抽象，并为其构建函数。
>- 利用已有的函数来构建更为复杂的抽象。
>- 通过将已有的函数传给其他函数来构建更复杂的抽象。

---

## 一等函数与 Applicative 编程

第二章介绍函数作为 Javascript 的一等公民，与数字一样具有很多良好的性质，例如：

- 函数与数字一样可以被存储为变量
- 函数与数字一样可以被存储为数组的一个元素
- 函数与数字一样可以作为对象的成员变量
- 函数和数字一样可以在使用时直接创建出来
  - 立即执行的匿名函数：42 + (( ) => 42)( ))
- 函数与数字一样可以被传递给另一个函数
- 函数与数字一样可以被另一个函数返回

高阶函数至少具有下面的一项性质：

- 以一个函数作为参数
- 返回一个函数作为结果

Applicative 编程定义为函数 A 作为参数提供给函数 B 。

Applicative 编程的三个典型的例子是 map、reduce 和 filter。

### 常见错误

函数式编程指北中讲述了一种编程的常见错误：

```javascript
const hi = name => `Hi ${name}`;
const greeting = name => hi(name);
```

greeting 这样的写法是存在很大的问题的，因为实际上 greeting 只是简单的调用了 hi ，如果之后 hi 的参数接口发生了变化，则 greeting 的参数接口也同样要发生变化。

正确的写法是：

```javascript
const greeting = hi;
```

在实际的项目代码中，充斥着大量类似的问题：

```javascript
httpGet('/post/2', json => renderPost(json));

// 把整个应用里的所有 httpGet 调用都改成这样，可以传递 err 参数。
// httpGet('/post/2', (json, err) => renderPost(json, err));
```

```javascript
httpGet('/post/2', renderPost); 
```

以上这个例子再次展示了这种写法的好处。

---

## 变量的作用域和闭包

在 JavaScript 中任何没有使用 var 关键字声明的变量都是全局变量。

介绍了词法作用域、动态作用域、函数作用域，然后介绍了一下闭包，感觉翻译的有点乱，不如去看其他的书或者博客。

---

## 由函数构建函数

### 柯里化

curry 的概念非常简单：

> 只传递函数的一部分参数来调用它，让它返回一个函数去处理剩下的参数。

自动柯里化参数：

```javascript
function curry(fun){
  return function(arg){
    return fun(arg);
  };
}
```

由于在 JavaScript 中，函数可以接受期望数量的参数加上一些额外的“特殊”参数，所以可以使用这样的 curry 函数来明确函数所接受的参数，类似的，可以构造一个接受两个参数的 curry 函数。

```javascript
function curry2(fun){
  return function(secondArg){
    return function(firstArg){
      return fun(firstArg, secondArg);
    };
  };
}
```

通过这样的方法可以构造需要的柯里化函数：

```javascript
function div(n, d){ return n / d}

var div10 = curry2(div)(10);

div(50);
//=> 5
```

注意这样构造的方法需要注意参数的顺序。

### 函数组合

以下的代码就是函数的组合范例：

```javascript
var compose = function(f,g) {
  return function(x) {
    return f(g(x));
  };
};
```

函数的组合具有一个良好的性质，组合律：

```javascript
var associative = compose(f, compose(g, h)) == compose(compose(f, g), h);
// true
```

运用这一性质可以写出参数数量可变的 compose 函数来。

组合律的另一大好处是，一个组合出来的函数可以再次被组合，不会有什么限制。

```javascript
var last = compose(head, reverse);
var angry = compose(exclaim, toUpperCase);
var loudLastUpper = (angry, last);
```

多使用函数组合构造 pointfree 的代码是一种良好的代码实践方式：

```javascript
var snakeCase = function (word) {
  return word.toLowerCase().replace(/\s+/ig, '_');
};

// pointfree
var snakeCase = compose(replace(/\s+/ig, '_'), toLowerCase);
```

**需要注意的是拼接函数 Underscore 用的接口是 _.compose , 而 lodash 使用的接口是 _.flow**

三个改造函数的得力手段：

1. 柯里化 - 对应 Lodash 的 curry 方法
2. 部分组合函数 - 对应 Lodash 的 partical 方法
3. 函数组合 - 对应 Lodash 的 flow 方法

---

## 纯度与不变性与更改政策

### 纯函数

纯函数具有以下属性：

- 其结果只能通过参数来计算（不能不使用参数，那样不“随机”计算结果不可变，算不上函数了）
- 不能依赖于能被外部操作改变的数据
- 不能改变外部状态

JavaScript 中包含很多可能让函数不“纯”的因素：

- Math.rand 方法
- Date.now 方法
- console.log 方法
- this
- 全局变量
- 参数为对象或数组（因为可以传递对象的引用，所以可能导致不“纯”）

良好的实践是将不“纯”的函数进行拆分，针对纯的部分进行常规的测试，针对不纯的部分测试它的一些特性。

可以通过延迟执行的方式来将不纯的函数转化为纯函数：

```javascript
var pureHttpCall = memoize(function(url, params){
  return function() { return $.getJSON(url, params); }
});
```

这样的做法单独看起来用处不大，但是结合一些其他的技巧将会产生很大的作用。

纯函数具有很多优秀的特性：

- 纯函数具有较好的可移植性和自文档性。
- 纯函数可以针对输入进行缓存拓展。
- 纯函数具有较好的可移植性和自文档性。
- 纯函数的代码可以并行执行。

### 不变性

JavaScript 的 Object 提供一个 freeze 方法，可以“冻结”对象，还提供 isFrozen 来帮助查看对象是否被冻结。但使用 freeze 来确保不变性有两个问题：

- 在与第三方 api 交互时可能产生错误
- freeze 是一个浅操作

不可变对象在设计时应该注意：

- 不可变对象在构造时应该固定它们的值，之后就不能继续修改
- 不可变对象的操作应该返回新的对象

---

## 常用 Lodash 函数列表

转换类

- [_.toArray(value)](https://www.lodashjs.com/docs/lodash.toArray)

判断类：

- [_.isString(value)](https://www.lodashjs.com/docs/lodash.isString)
- _.isEqual
- _.isEmpty
- _.isElement
- _.isArray
- _.isObject
- _.isArguments
- _.isFunction
- _.isNumber
- _.isFinite
- _.isBoolean
- _.isDate
- _.isRegExp
- _.isNaN
- _.isNull
- _.isUndefined

特殊判断类：

- [_.every(collection, predicate)](https://www.lodashjs.com/docs/lodash.every)
  - _.some

计算类：

- [_.size(collection)](https://www.lodashjs.com/docs/lodash.size)
- [_.max(array)](https://www.lodashjs.com/docs/lodash.max)
  - _.maxBy(array, iteratee)

操作类：

- [_.reduce(collection, iteratee, accumulator)](https://www.lodashjs.com/docs/lodash.reduce)
  - _.reduceRight
- [_.map(collection, iteratee)](https://www.lodashjs.com/docs/lodash.map)
- [_.filter(collection, predicate)](https://www.lodashjs.com/docs/lodash.filter)
  - _.reject
- [_.find(collection, predicate, fromIndex)](https://www.lodashjs.com/docs/lodash.find)
- [_.sortBy(collection, iteratees)](https://www.lodashjs.com/docs/lodash.sortBy)
  - _.groupBy
  - _.countBy

数组操作类：

- [_.head(array)](https://www.lodashjs.com/docs/lodash.head)
- [_.tail(array)](https://www.lodashjs.com/docs/lodash.tail) 这个在 Underscore 名字是 rest， 但是 Lodash 中也有一个 rest 函数干完全不一样的事情，天坑。

对象操作类：

- [_.keys(object)](https://www.lodashjs.com/docs/lodash.keys)
  - _.value
- [_.assign(object, [sources])](https://www.lodashjs.com/docs/lodash.assign)
  - _.assignIn
- [_.clone(value)](https://www.lodashjs.com/docs/lodash.clone)
  - .cloneDeep
  - .cloneWith
  - .cloneDeepWith

特殊的包装操作：

- [_.chain(value)](https://www.lodashjs.com/docs/lodash.chain#_chainvalue)
- [_.tap(value, interceptor)](https://www.lodashjs.com/docs/lodash.tap#_tapvalue-interceptor)
- [_.prototype.value()](https://www.lodashjs.com/docs/lodash.prototype.value)

创建类：

- [_.range(start, end, step)](https://www.lodashjs.com/docs/lodash.range)
- [_.random(lower, upper, floating)](https://www.lodashjs.com/docs/lodash.random)

其他：

- [_.flow([funcs])](https://www.lodashjs.com/docs/lodash.flow)

  - _.flowRight

- [_.curry(func, arity)](https://www.lodashjs.com/docs/lodash.curry)

  - _.curryRight

- [_.partial(func, [partials])](https://www.lodashjs.com/docs/lodash.partial)

  - _.partialRight

  

