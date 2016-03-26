---
title: 一步一步写一个简单的js版quine程序
tags: [javascript,quine]
categories: javascript
---
早上看了[justjavac][1]大大的一篇文章：[javascript 的 quine 程序升级版][2]，了解了一下所谓[quine][3]程序的概念：

>一个 quine 是一个计算机程序，它不接受任何输入，且唯一的输出就是自身的源代码。

感觉挺有意思的，于是打算自己用js写一个试试。

首先确定输出方式为控制台，即通过`console.log`输出。

为有(guang)趣(gao)起见，准备从一个“打印本站域名”的“额外功能”开始：

```javascript
console.log("perichr.org")
```

这个命令可以在控制栏打印出一行`perichr.org`。接下来我准备一步一步改造这个命令，直至达成[quine][3]的目标。

首先试着打印上面这个命令：

```javascript
console.log('console.log("perichr.org")')
```

这样就在控制栏打印了一行`console.log("perichr.org")`。注意到源码里外用了不同的引号，这是为了规避转义符。

对了，别忘了需要执行自己，那么有：

```javascript
console.log("perichr.org");console.log('console.log("perichr.org")')
```

很好，这样即执行了“额外功能”，又打印了自己的源代……不对！压根没有打印自己的源代码对吧！
从简单的入手，不考虑打印完整源代码，先想想要怎样能在执行一个功能的同时打印功能代码呢？打印的是字符串，执行的是js源码……想到什么了？对了，`eval`！

```javascript
p='console.log("perichr.org")';eval(p);console.log(p)
```

很好，这次完美地打印了`perichr.org`和`console.log("perichr.org")`。要注意的是，这里的变量`p`没有做声明，严格模式下是通不过的哟！

不过这还远远不够！来做一点小调整：

```javascript
p='console.log("perichr.org");console.log(p)';eval(p)
```

发现了吗？这次输出了`perichr.org`和`console.log("perichr.org");console.log(p)`！进化了！接下来先讨个巧，利用 **赋值表达式返回右值** 的小技巧来简化一下代码，毕竟代码越精简心理压力越小嘛……

```javascript
eval(p='console.log("perichr.org");console.log(p)')
```

接下来怎么办呢……来对比下上面的源码和输出的代码：`console.log("perichr.org");console.log(p)`，其实已经很接近了有木有！输出码就是缺少了头`eval(p='`和尾`')`！来，补上！

```javascript
eval(p='console.log("perichr.org");console.log("eval(p=\'"+p+"\')")')
```

在补的时候就应该已经注意到了一个问题：转义符！这里已经无法避免转义符了！来看看输出的代码：`eval(p='console.log("perichr.org");console.log("eval(p='"+p+"')")')`

真好，已经十分接近了对吧！但还是有一点小问题：没错，转义符！输出时源码中的`\'`被转义成了`'`！怎么办？那就要把文本中的`'`反转义为`\'`！怎么转？可以使用`"'".replace(/'/g,"\\'")`这种方式。考虑到文本要在`p`的赋值时转义，那么就需要手动反转义为：`'.replace(/\'/g,"\\\\\'")'`。接下来把它插入源码中:

```javascript
eval(p='console.log("perichr.org");console.log("eval(p=\'"+p.replace(/\'/g,"\\\\\'")+"\')")')
```

等等……好像有什么不对？我刚才是不是把一大波 **有用的反斜杠** 加入源代码了？要知道，这货也是会被转义的啊！！怎么办？好嘛，来改造下反转义代码，这回单引号和反斜杠都要反转义一下：`.replace(/[\\']/g,"\\$&")`，这里的`$&`是指返回匹配字符串。手动反转义：`.replace(/[\\\\\']/g,"\\\\$&")`，再来一次：

```javascript
eval(p='console.log("perichr.org");console.log("eval(p=\'"+p.replace(/[\\\\\']/g,"\\\\$&")+"\')")')
```

在控制台试一试……成功！完美！强力！分毫不差地返回了自身！任务圆满完成！

怎么样，看起来很高大上的东西，其实入门很简单吧？



有人问，费劲心机写这么个东西，有啥意义？嘛，这个世界不是为了“某些人所谓的意义”而存在的。世界存在的意义在于“世界存在”这个现象本身；我折腾这个东西的意义也就在于“折腾代码”这件事本身。我觉得我获得了一些有意义的东西。你说呢？




[1]:http://justjavac.com/
[2]:http://justjavac.com/javascript/2013/10/11/javascript-quine-plus.html
[3]:http://en.wikipedia.org/wiki/Quine_(computing)

