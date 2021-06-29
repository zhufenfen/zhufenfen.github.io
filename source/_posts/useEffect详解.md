---
title: useEffect详解
date: 2021-05-14 11:12:28
tags:
header-img: https://images.pexels.com/photos/7818815/pexels-photo-7818815.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260
---
从前觉得useEffect是一个好用且好理解的Hook，可在项目引入了react-hooks/exhaustive-deps lint规则的时候，我发现自己的很多useEffect都报lint错误，当时的想法就是突破规则不能让它压制了我用useEffect的多样性。后来某一天代码被公之于众，这就让我不得不反思我对useEffect的理解问题了。
<img src="/images/useEffect1.png" width=256 height=256 />
我发觉自己在问类似的问题：
- 为什么有时候effect拿到的是旧的state或props
- 为什么有时候我想要实现的业务和eslint相冲突，真正的用法是怎样的？

花费一些时间过了遍useEffect[官方文档](https://zh-hans.reactjs.org/docs/hooks-effect.html)和Dan的[useEffect完整指南](https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/#swimming-against-the-tide)，算是揭开了useEffect的真面目～当我不再透过熟悉的class生命周期方法去窥探useEffect这个Hook的时候，我才得以融会贯通
<img src="/images/useEffect2.gif" width=256 height=256 />
*“忘记你已经学到的”*

## 每一次渲染都有它自己的state、props、事件处理函数、Effects
```
function Counter() {
  const [count, setCount] = useState(0);
  const handleAlertClick = () => {
    setTimeout(() => {
      alert(count);
    }, 3000);
  };
  useEffect(() => {
    document.title = `you clicked ${count} times`;
    setTimeout(() => {
      console.log(count);
    }, 3000);
  });
  return (
    <>
      <p>you clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>click me</button>
      <button onClick={handleAlertClick}>show alert</button>
    </>
  );
}
```
React会记住你提供的effect函数，并且会在每次更改作用于DOM并让浏览器绘制屏幕后去调用它。你可以想象，effect是渲染结果的一部分。渲染过程：
- React：给我状态为0时的UI
- 组件：给你需要渲染的jsx，渲染完成之后记得调用effect
- React：没问题，开始更新UI。喂，浏览器，我要改下DOM
- 浏览器：酷，我已经绘制屏幕上了
- React：好，开始运行effect
点击之后
- 组件：把我的状态设置为1
- ...
得出结论：**组件内的每一个函数（事件处理函数、effects、定时器、API调用）会捕获定义它们那次渲染中的props和state。**

### 清理时机
有些effects可能需要一个清理步骤，它的目的是消除副作用，比如取消订阅。思考下面的代码：
```
// 假设第一次渲染props是{id: 10}，第二次渲染props是{id: 20}
useEffect(() => {
  ChatAPI.subscribeToFriendStatus(props.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.id, handleStatusChange);
  };
});
```
你可能会认为，清除过程看到的是旧的props因为它是在重新渲染之前运行，不过这并不精确，让我们来一探究竟。React只会在浏览器绘制后运行effects，这使你的应用更流畅因为大多数effects并不会阻塞屏幕更新，effect的清除同样被延迟了，上一次的effect会在重新渲染后被清除！
- React渲染{id: 20}的UI
- 浏览器绘制，我们看到{id: 20}的UI
- React清除{id: 10}的effect（引用上述结论）
- React运行{id: 20}的effect

## 如何读取未来的state和props
有时候我想在effect中拿到最新的值而不是那次渲染中定义的值，最简单的方法的就是使用refs。
```
function Counter() {
  const [count, setCount] = useState(0);
  const latestCount = useRef();
  useEffect(() => {
    latestCount.current = count;
    setTimeout(() => {
      console.log(latestCount.current);
    }, 3000);
  });
  return (
    <>
      <p>you clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>click me</button>
    </>
  );
}
```
它不是默认行为，而是你主动选择去这样做。

## 关于依赖项不要对React撒谎
为了避免effects不必要的重复调用，可以提供给useEffect一个依赖数组参数。这好比你告诉React：嘿，我知道你看不到这个函数里的东西，但我可以保证只使用了渲染中的[依赖数组参数]。如果设置了依赖项，effects中用到的所有组件内的值都要包含在依赖项中，包括props、state、函数...

但我曾经试图违反这个规则，因为我只想在挂载的时候运行它。
```
function SearchResults() {
  async function fetchData() {
    // ...
  }
  useEffect(() => {
    fetchData();
  }, []);
}
```
如果盲目把函数放在依赖项里，可能会遇到无限请求的问题，在我们深入解决方案之前，我们先更好的理解问题。

### 如果设置了错误的依赖会怎么样呢
如果你的心智模型是：只有当我想重新触发effect的时候才需要设置依赖。这个例子会让你产生危机，你想触发一次并且这是个定时器，但为什么会有问题？
```
const [count, setCount] = useState(0);
useEffect(() => {
  const timer = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(timer);
}, []);
```
在第一次渲染中count是0，setCount(0 + 1)，如果不设置依赖的话，effect不会重新运行，后面每一次都是在调用setCount(0 + 1)。类似这样的问题是很难想到的，所以关于依赖不要对React撒谎。

### 两种诚实告知依赖的方法
- 在依赖中包含所有effect中用到的组件内的值

让我们在依赖中包含count，这能解决问题，但是定时器会在每次count改变后清除和重新创建。
```
const [count, setCount] = useState(0);
useEffect(() => {
  const timer = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(timer);
}, [count]);
```
- 修改effect内部代码确保它包含的值只会在需要的时候发生变更

下面来看一些移除依赖的常用技巧

## 移除依赖

### 让effects自给自足
我们想去掉effect的count依赖，为了实现这个目的，需要问自己一个问题：我们为什么要用count？可以看到我们在setCount中用到了count，在这个场景中，我们其实并不需要在effect中使用count。当想要根据前一个状态更新状态的时候，我们可以使用setState的函数形式。因为我们需要告知React的仅仅是递增状态，不管它现在具体是什么值。
```
const [count, setCount] = useState(0);
useEffect(() => {
  const timer = setInterval(() => {
    setCount(c => c + 1);
  }, 1000);
  return () => clearInterval(timer);
}, []);
```
尽管effect只调用了一次，第一次渲染的定时器回调函数会在每次触发时给React发送c => c + 1更新指令，它不再需要知道当前的count值，因为React已经知道了。当我们想基于一个prop来计算下一次的state，它并不能做到，幸运的是，setCount(c => c + 1)有一个更强大的姐妹模式，它的名字叫useReducer。

### 解耦来自Actions的更新
```
const [count, setCount] = useState(0);
const [step, setStep] = useState(1);
useEffect(() => {
  const timer = setInterval(() => {
    setCount(c => c + step);
  }, 1000);
  return () => clearInterval(timer);
}, [step]);
return (
  <>
    <p>{count}</p>
    <input value={step} onChange={(e) => setStep(Number(e.target.value))} />
  </>
);
```
如果我们不想在step更新后重启定时器，我们该如何从effect中移除对step的依赖呢？当你想更新一个状态，并且这个状态更新依赖另一个状态的值时，你可能需要用useReducer去替换它们。
```
function reducer(state, action) {
  const { count, step } = state;
  if (action.type === 'tick') {
    return { count: count + step, step };
  } else if (action.type === 'step') {
    return { count, step: action.step };
  } else {
    throw new Error();
  }
}
const initialState = {
  count: 0,
  step: 1,
};
export default function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  const { count, step } = state;
  useEffect(() => {
    const timer = setInterval(() => {
      dispatch({ type: 'tick' });
    }, 1000);
    return () => clearInterval(timer);
  }, [dispatch]);
  return (
    <>
      <p>{count}</p>
      <input
        value={step}
        onChange={(e) => {
          dispatch({ type: 'step', step: Number(e.target.value) });
        }}
      />
    </>
  );
}
```
如果step是prop的话，我们可以把reducer函数放到组件内去读取prop。我们解决了问题！effect负责告诉我们发生了什么，更新逻辑交由reducer去统一处理，这可以帮助我们移除不必要的依赖。你可能会疑惑，在之前渲染中调用的reducer怎么知道新的prop？答案是当你dispatch的时候，React只是记住了action，它会在下次渲染中调用reducer，而且调用reducer也不是在effect里。

## 函数
```
function SearchResults() {
  async function fetchData() {
    // ...
  }
  useEffect(() => {
    fetchData();
  }, []);
}
```
上面的代码可以正常工作，在组件日渐复杂的迭代流程中我们很难确保它在各种情况下还能正常运行。如果某些函数仅在effect中调用，我们可以把它移到effect中，就不用考虑间接依赖了。
```
useEffect(() => {
  function getFetchUrl() {
    return 'https://hn.algolia.com/api/v1/search?query=' + query;
  }
  async function fetchData() {
    const result = await axios(getFetchUrl());
    setData(result.data);
  }
  fetchData();
}, [query]);
```
有时候函数会在不同地方被重复调用，我们不想把它移到effect中了。由于在组件内定义的函数每次渲染都会变，我们有两个解决方案。
- 函数没有使用组件内的任何值，可以提到组件外面去定义
```
async function fetchData() {
  // ...
}
function SearchResults() {
  useEffect(() => {
    fetchData();
  }, []);
}
```
- 函数被useCallback包装，使函数在需要的时候才会变。通过属性从父组件传入的函数也同样适用这个方法。
```
const fetchData = useCallback(() => {
  // ...
}, []);
useEffect(() => {
  fetchData();
}, [fetchData]);
```

当然，到处使用useCallback是件很笨拙的事情，当我们需要把函数传递下去且函数会在子组件的effect中被调用时，useCallback是很好的技巧且非常有用。

### 函数是数据流的一部分么
有趣的是，这种模式在class组件中行不通，并且这种行不通恰当好处揭示了effect和生命周期之间的区别。考虑下面的转换：
```
class Parent extends Component {
  state = {
    query: 'react'
  };
  fetchData = () => {
    const url = 'https://hn.algolia.com/api/v1/search?query=' + this.state.query;
    // ... Fetch data and do something ...
  };
  render() {
    return <Child fetchData={this.fetchData} query={this.state.query} />;
  }
}
class Child extends Component {
  componentDidMount() {
    this.props.fetchData();
  }
  componentDidUpdate(prevProps) {
    // props.fetchData始终等于fetchData
    <!-- if (this.props.fetchData !== prevProps.fetchData) {
      this.props.fetchData();
    } -->
    if (this.props.query !== prevProps.query) {
      this.props.fetchData();
    }
  }
}
```
在class组件中，函数属性本身并不是数据流的一部分。而在hooks中，使用useCallback，函数完全可以参与到数据流中。

通过对useEffect重新理解和认识，规范使用useEffect志在必得！