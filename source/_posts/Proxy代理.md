---
title: Proxy代理
date: 2021-01-26 10:42:55
tags:
header-img: https://images.pexels.com/photos/3881104/pexels-photo-3881104.jpeg?auto=compress&cs=tinysrgb&dpr=2&w=500
---
Proxy原意是代理，表示由它来“代理”某些操作，可以译为“代理器”。 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截。
简单用法：
```
var obj = new Proxy({}, {
  get: function (target, propKey, receiver) {
    console.log(`getting ${propKey}!`);
    return Reflect.get(target, propKey, receiver);
  },
  set: function (target, propKey, value, receiver) {
    console.log(`setting ${propKey}!`);
    return Reflect.set(target, propKey, value, receiver);
  }
});
obj.count = 1
//  setting count!
++obj.count
//  getting count!
//  setting count!
//  2
```
Proxy 对象的所有用法，都是var proxy = new Proxy(target, handler)这种形式，不同的只是handler参数的写法。其中，new Proxy()表示生成一个Proxy实例，target参数表示所要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为。下面来介绍Proxy实例的方法。

## Proxy实例的方法
- **get(target, propKey, receiver)**：拦截对象属性的读取，比如proxy.foo和proxy['foo']。
- **set(target, propKey, value, receiver)**：拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
- **has(target, propKey)**：拦截propKey in proxy的操作，返回一个布尔值。
```
let stu1 = {name: '张三', score: 59};
let stu2 = {name: '李四', score: 99};

let handler = {
  has(target, prop) {
    if (prop === 'score' && target[prop] < 60) {
      console.log(`${target.name} 不及格`);
      return false;
    }
    return prop in target;
  }
}
let oproxy1 = new Proxy(stu1, handler);
let oproxy2 = new Proxy(stu2, handler);
'score' in oproxy1
// 张三 不及格
// false
'score' in oproxy2
// true
for (let a in oproxy1) {
  console.log(oproxy1[a]);
}
// 张三
// 59
for (let b in oproxy2) {
  console.log(oproxy2[b]);
}
// 李四
// 99
虽然for...in循环也用到了in运算符，但是has()拦截对for...in循环不生效。
```
- **deleteProperty(target, propKey)**：拦截delete proxy[propKey]的操作，返回一个布尔值。
- **ownKeys(target)**：拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
- **getOwnPropertyDescriptor(target, propKey)**：拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
- **defineProperty(target, propKey, propDesc)**：拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
- **preventExtensions(target)**：拦截Object.preventExtensions(proxy)，返回一个布尔值。
- **getPrototypeOf(target)**：拦截Object.getPrototypeOf(proxy)，返回一个对象。
- **isExtensible(target)**：拦截Object.isExtensible(proxy)，返回一个布尔值。
- **setPrototypeOf(target, proto)**：拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- **apply(target, object, args)**：拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
```
var twice = {
  apply (target, ctx, args) {
    return Reflect.apply(...arguments) * 2;
  }
};
function sum (left, right) {
  return left + right;
};
var proxy = new Proxy(sum, twice);
proxy(1, 2) // 6
proxy.call(null, 5, 6) // 22
proxy.apply(null, [7, 8]) // 30
```
- **construct(target, args)**：拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。
```
const p = new Proxy(function () {}, {
  construct: function(target, args) {
    console.log('called: ' + args.join(', '));
    return { value: args[0] * 10 };
  }
});
(new p(1)).value
// "called: 1"
// 10
```

## Proxy.revocable()
返回一个可取消的proxy实例，它的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。
```
let target = {};
let handler = {};
let {proxy, revoke} = Proxy.revocable(target, handler);
proxy.foo = 123;
proxy.foo // 123
revoke();
proxy.foo // TypeError: Revoked
```

## this问题
虽然 Proxy 可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。主要原因就是在 Proxy 代理的情况下，目标对象内部的this关键字会指向 Proxy 代理。
```
const target = {
  m: function () {
    console.log(this === proxy);
  }
};
const handler = {};
const proxy = new Proxy(target, handler);
target.m() // false
proxy.m()  // true
```
此外，有些原生对象的内部属性，只有通过正确的this才能拿到，所以 Proxy 也无法代理这些原生对象的属性。这时，this绑定原始对象，就可以解决这个问题。
```
const target = new Date('2015-01-01');
const handler = {
  get(target, prop) {
    if (prop === 'getDate') {
      return target.getDate.bind(target);
    }
    return Reflect.get(target, prop);
  }
};
const proxy = new Proxy(target, handler);
proxy.getDate() // 1
```
另外，Proxy 拦截函数内部的this，指向的是handler对象。
```
const handler = {
  get: function (target, key, receiver) {
    console.log(this === handler);
    return 'Hello, ' + key;
  },
  set: function (target, key, value) {
    console.log(this === handler);
    target[key] = value;
    return true;
  }
};
const proxy = new Proxy({}, handler);
proxy.foo
// true
// Hello, foo
proxy.foo = 1
// true
```
