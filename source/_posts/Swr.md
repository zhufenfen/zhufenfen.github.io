---
title: Swr
date: 2020-04-25 21:50:28
tags:
---
![](/images/swr.jpg)
## 什么是swr
```
SWR is a React Hooks library for remote data fetching.
```
官方的大概意思就是：swr(stale-while-revalidate)首先从缓存中返回数据，然后发送请求（重新验证），最后再次提供最新数据。充分体现了数据的即时性。
## 快速开始
yarn add swr或npm install swr
```
import useSWR from 'swr'

function fetcher(url) {
  return fetch(url).then(res => res.json())
}

export default function() {
  const { data, error } = useSWR('http://dummy.restapiexample.com/api/v1/employees', fetcher)
  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>{JSON.stringify(data)}</div>
}
```
useSWR接受一个key和任何一个异步请求函数fetch，key通常是API URL，fetch接受key作为其参数，完成具体的数据请求行为并异步返回数据。请求pending时数据为undefined，请求完成时会返回data和error然后重新渲染组件。

上述fetcher是一个简单的模块，将运行fetch并返回json数据(而不是用http响应进行回复)
## many功能
相比常见的数据请求库提供了很多很酷的特性。比如：

Focus Revalidation（焦点重新验证，重新聚焦页面和在选项卡之间切换时，SWR会重新验证数据）

Fast Navigation（快速导航，在页面导航时，如果从缓存中呈现了数据，SWR将自动从源中重新验证数据）

Refetch on Interval（间隔刷新，SWR提供自动重新获取数据的选项，当用户进行更改时，两个会话最终呈现相同的数据）

Local Mutation（局部突变，使更改感觉更快的好办法）

Scroll Position Recovery and Pagination（分页和滚动位置恢复）

Custom Data Fetching（SWR默认用原生的fetch做请求，但是也可以指定其它的任何异步请求库作为第二个参数）比如下面例子：
```
import { request } from 'graphql-request'
import useSWR from 'swr'

const API = 'https://api.graph.cool/simple/v1/movies'

export default function() {
  const { data, error } = useSWR(
    `{
      Movie(title: "Inception") {
        releaseDate
        actors {
          name
        }
      }
    }`,
    query => request(API, query)
  )
  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>Movie: {JSON.stringify(data)}!</div>
}
```
Dependent Fetching（允许您获取依赖于其它数据的数据）

Suspense（还可与React Suspense一同使用，只需在useSWR的第三个参数配置{ suspense: true }）
```
import { Suspense } from 'react'
import useSWR from 'swr'

function fetcher(url) {
  return fetch(url).then(res => res.json())
}

function Profile () {
  const { data } = useSWR(
    'http://dummy.restapiexample.com/api/v1/employees',
    fetcher,
    { suspense: true }
  )
  return <div>hello, {JSON.stringify(data)}</div>
}

export default function() {
  return (
    <Suspense fallback={<div>loading...</div>}>
      <Profile/>
    </Suspense>
  )
}
```
## 总结
目前来看SWR基于stale-while-revalidate的概念实现了一个React hooks版本的数据请求库，同时带入很多新思路，是具有较大潜力的请求类工具库。

参考链接：
[SWR官方文档](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)
