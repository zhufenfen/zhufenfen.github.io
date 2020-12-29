---
title: Set和Map数据结构
date: 2020-12-29 10:59:02
tags:
---
相信大家都知道Set类似于数组，Map类似于对象，但这两种数据结构的意义何在，可能就有些说不上来了，但它们看起来又很不平凡的样子，那么下面就跟随我一起来细细了解下这几种数据结构吧。
## Set
类似于数组，成员唯一。接受一个数组（或者具有iterable接口的其他数据结构）作为参数，可以去重，去重方法类似于精确相等运算符===，主要区别在于Set加入值时认为NaN等于自身。
### 属性
- Set.prototype.constructor：构造函数，默认就是Set函数
- Set.prototype.size：Set实例的成员总数

### 方法
**操作方法**
- Set.prototype.add(value)
- Set.prototype.delete(value)
- Set.prototype.has(value)
- Set.prototype.clear()

**遍历方法**
- Set.prototype.keys()
- Set.prototype.values()：Set结构的实例默认可遍历，它的默认遍历器生成函数就是它的values方法，Set.prototype[Symbol.iterator] = Set.prototype.values
- Set.prototype.entries()
- Set.prototype.forEach((value, key) => {键值和键名一样})

### 应用
扩展运算符(...)内部使用for...of循环，所以也可以用于Set接口

**数组去重：**
```
[...new Set([1, 2, 3, 3])]
Array.from(new Set([1, 2, 3, 3]))
```
**改变原来数据结构：**
```
let set = new Set([1, 2, 3])
方法一：set = new Set([...set].map(val => val * 2))
方法二：set = new Set(Array.from(set, val => val * 2))
```
## WeakSet
和Set区别在于：
1. 成员只能是对象
2. WeakSet中的对象都是弱引用，垃圾回收机制不考虑WeakSet对该对象的引用
```
const a = [[1, 2], [3, 4]];
const ws = new WeakSet(a); // {[1, 2], [3, 4]}
```

### 应用
因为WeakSet中的成员都是弱引用，随时可能丢失，就不用担心内存泄漏。
- 储存dom节点
```
// ws对实例的引用，不会被计入内存回收机制
const ws = new WeakSet();
class Foo {
  constructor() {
    ws.add(this);
  }
  method() {
    if (!ws.has(this)) {
      throw new Error('Foo.prototype.method 只能在Foo的实例上调用')
    }
  }
}
```

## Map
类似于对象，也是键值对的集合，但是键的范围不限于字符串。不仅仅是数组，任何具有Iterator接口，且每个成员都是一个双元素的数组的数据结构，都可以当作Map构造函数的参数
### 属性
- Map.prototype.size

### 操作方法
- Map.prototype.set(key, value)
- Map.prototype.get(key)
- Map.prototype.has(key)
- Map.prototype.delete(key)
- Map.prototype.clear()

### 遍历方法
- Map.prototype.keys()
- Map.prototype.values()
- Map.prototype.entries()：Map结构的默认遍历器生成函数就是它的entries方法，Map.prototype[Symbol.iterator] = Map.prototype.entries
- Map.prototype.forEach((value, key, map) => {})
```
const map = new Map([['F', 'no'], ['T', 'yes']]);
for(let [key, value] of map) {
  console.log(key, value);
}
```

## WeakMap
和Map区别在于：
1. 只接受对象作为键名
2. 键名所引用的对象都是弱引用（键值正常引用）
3. 只有set、get、has、delete四个方法
```
const wm = new WeakMap()
const key = {}
let obj = {p: 1}
wm.set(key, obj)
obj = null
wm.get(key) // {p: 1}
```

WeakMap可用于防止内存泄漏，但比较难演示，因为无法观察里面的引用会自动消失。如果引用占用特别多的内存，就可以通过Node的process.memoryUsage方法看出来。首先打开Node命令行：
![](/images/weakMap1.jpg)
--expose-gc表示允许手动执行垃圾回收机制
![](/images/weakMap2.jpg)
查看内存占用初始状态
![](/images/weakMap3.jpg)
数组被引用了两次
![](/images/weakMap4.jpg)
此时，清除变量key对数组的引用，但没有清除WeakMap的键名对数组的引用，再次执行垃圾回收，会发现WeakMap键名没有阻止gc对内存的回收。看来外部的引用消失，WeakMap内部的引用，就会自动被垃圾回收清除。Chrome浏览器的Memory面板的垃圾桶按钮，可以强制进行垃圾回收，这个按钮也可用来观察WeakMap内部引用是否消失。

以上只是了解了Set和Map的数据结构，知道了相关用法用途，但还未深入，感兴趣的同学可以手动实现下Set和Map数据结构。