---
title: 如何使用React Hooks重构HOC组件
date: 2020-05-19 14:46:42
tags:
---
相信大家都知道，React有两种类型的组件，Class组件和函数式组件。在React早期采用的是面向对象的编程思想，所以无论是React.createClassAPI还是ES6的class类的声明方式，绝大部分组件都是Class组件类型的。每个组件都是一个类，内部状态和函数都被挂载在类的实例上。而随着React逐渐拥抱函数式编程，函数式组件因为缺少内部状态和生命周期，限制了函数式组件在更多场景下的应用。而React 16.8中引入的hooks就是用来增强函数式组件的，内部状态、生命周期、逻辑复用甚至副作用都可以被写成一个一个独立的hooks，在函数式组件调用hooks，就可以将所需要的东西勾到组件内部，增强对应组件相应功能。
![](/images/hook1.jpg)
### 核心Hooks
接下来介绍几个核心hooks

**useState**

函数式组件由于没有实例和上下文的概念，所以无法在每次执行时保存任何数据和状态，而useState为函数式组件引入了状态的机制

**useEffect**

副作用：异步请求、订阅事件、变更Dom元素。而useEffect是专门管理这类副作用的hooks，在class组件中，副作用在不同的生命周期中被调用，而现在统一集中在useEffect中管理

可以说useState和useEffect为函数式组件补齐了自身的短板。
![](/images/hook2.jpg)

### 自定义Hooks
如果从早期开始接触React，会发现React一直在解决逻辑抽象和代码复用问题，从Mixins到高阶组件到渲染属性Render Props，无外乎都是为了达到这个目的。我们可以通过构建hooks函数，将函数式组件内部的一些逻辑和状态提取到hooks函数中，这就形成了一个对外暴露的模块，也被称为自定义hooks。函数式组件可以在它内部的任何地方调用自定义hooks来获得需要的功能和状态。

**示例：统计每个组件挂载在dom上的时长**

这需要我们在组件挂载时启动一个定时器，在组件卸载时停止定时器，并向服务器上报时长。我们可以将这部分逻辑抽象为自定义hooks，useTimer里封装了useState和useEffect，useState会记录时长duration，useEffect里的定时器会修改duration。这就实现了一个自定义hooks。
```
const MyComponent = props => {
  const duration = useTimer();
  return <div>组件挂载时长：{duration}</div>
}
const useTimer = () => {
  const [duration, setDuration] = useState(0);
  useEffect(() => {
    const mountedTime = performance.now();
    const id = setInterval(() => {
      setDuration(performance.now() - mountedTime)
    }, 1000)
    return () => {
      clearInterval(id);
      console.log(performance.now() - mountedTime);
    }
  }, [])
}
```
自定义Hooks和高阶组件HOC和Render Props最大的不同点是，后两者仅仅是一种开发模式，而自定义hooks则是由react提供了Api层面的支持。它既能够更加自然地融入到React渲染过程中，也能够符合React函数式编程的思想。那如何将祖传代码HOC改成hooks呢？

### 重构HOC组件
![](/images/hook3.jpg)
![](/images/hook4.jpg)
![](/images/hook5.jpg)

### HOC组件和Hooks区别
```
调用时机：HOC是声明在组件外部的，而所有的hooks都需要运行在函数组件内部。
内部状态机制：this.state = {}  useState
useEffect异步问题：操作dom可能会需要用useLayoutEffect
```
![](/images/hook6.jpg)



