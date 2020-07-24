---
title: Event Loop学习笔记
date: 2020-03-29 21:10:52
tags: ''
---
Event Loop是计算机系统的一种运行机制。想要理解Event Loop，就要从程序的运行模式讲起，运行以后的程序叫做[进程](https://zh.wikipedia.org/wiki/%E8%A1%8C%E7%A8%8B)。一般情况下，一个进程只能执行一个任务，如果有很多任务需要执行，有三种解决方法。
```
（1）排队
（2）新建进程
（3）新建线程：进程太耗资源，一个进程包含多个线程，由线程去执行任务
```
## 前言
### 为什么javascript是单线程
javascript的单线程，与它的用途有关。作为浏览器脚本语言，javascript的主要用途是和用户互动操作dom，由于多线程可能会修改彼此运行的结果，所以为了避免复杂性，javascript从诞生起就是单线程（HTML5提出的Web Worker，允许创建多个线程，但子线程受主线程控制且不得操作dom，所以并未改变javascript单线程的本质）

### 任务队列
单线程就意味着，所有任务都需要排队，前一个任务结束才会执行后一个任务。如果排队是因为计算量大，CPU忙不过来倒也算了，但因为IO设备（输入输出慢）排队的话就有些过分了。javascript的设计者于是将任务分为**同步任务**（主线程上的任务）和**异步任务**（任务队列的任务，主线程上的任务执行完毕就会读取任务队列）

### 事件和回调函数
任务队列就是一个事件的队列（IO设备的事件、用户操作产生的事件、定时事件等）。IO设备完成一个任务，就在“任务队列”添加一个事件。执行事件就是执行对应的回调函数（被主线程挂起来的代码）。某些事件只有到了规定的时间，才能返回主线程。

### Event Loop
主线程从“任务队列”读取事件，这个过程是循环不断的，所以又称**事件循环**。
![](/images/event_loop.jpg)
主线程运行的时候，产生各种堆和栈，栈中的代码调用各种外部API，它们在“任务队列”中加入各种事件。只要栈中的代码执行完毕，主线程就会去读取“任务队列”，依次执行那些事件所对应的回调函数。

### Node.js的Event Loop
![](/images/event_loop2.jpg)
```
（1）V8引擎解析javascript代码
（2）解析后的代码调用Node API
（3）libux库负责Node API的执行。它将不同的任务分配给不同的线程，形成一个Event Loop，以异步的方式将任务的执行结果返回给V8引擎
（4）V8引擎再将结果返回给用户
```
Node.js提供了另外两个与任务队列有关的方法：process.nextTick在当前执行栈的尾部触发回调，setImmediate在任务队列的尾部添加事件。所以nextTick执行效率更高（因为不用读取任务队列）
**setTimeout对比setImmediate**：如果都在主模块调用，先后顺序取决于进程性能。如果在I/O队列调用，则setImmediate的回调先执行，因为会先到check队列。

## Event Loop
分浏览器和NodeJS的Event Loop。浏览器是先微后宏（执行一个宏任务就读取微队列），Nodejs分为四个宏任务队列和2个微任务队列（执行一个宏任务队列就读取微队列）
![](/images/event_loop3.jpg)
```
微队列（microtask）
  Promise
  process.nextTick(Node独有)
  MutationObserver
  Object.observe
宏队列（macrotask）
  setTimeout、setInterval、setImmediate(Node独有)
  requestAnimationFrame(浏览器独有)
  UI rendering(浏览器独有)
  I/O
```

参考链接：
[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)
[带你彻底弄懂Event Loop](https://juejin.im/post/5b8f76675188255c7c653811#heading-4)
