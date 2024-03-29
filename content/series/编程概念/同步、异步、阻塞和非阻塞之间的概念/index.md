---
title: "同步、异步、阻塞和非阻塞之间的概念"
date: 2023-08-19T23:20:19+08:00
lastmod: 2022-11-28T16:53:08+08:00
draft: false
comments: true
math: false
weight: 1
tags:
---

## 说明
- 同步：必须等到资源返回之后才能继续执行
- 异步：先请求资源，之后在需要的地方使用资源
- 阻塞：资源请求后，必须成功获取返回后才能继续执行
- 非阻塞：先请求资源，之后在使用资源时再查询资源是否成功获取
- 同步阻塞：线程开始请求资源时，暂停后续所有操作，直到资源成功返回
- 同步非阻塞：线程请求资源后立刻返回，不管资源是否真正获取。核心是欺骗线程资源已获取，它会在使用资源处阻塞
- 异步：线程请求资源，但不需要返回。当需要使用资源时，若资源未到位，则在此处阻塞

## 总结
阻塞和非阻塞的区别是资源的 `获取` 和 `返回` 是否需要一同完成。换句话说，资
源请求之后是否需要 `立刻使用` 。阻塞是必然存在的，因为没有资源就没法执行。重点是充分利用 `资源请求` 和 `资源使用` 之间的时间。也因此，异步没有阻塞和非阻塞的说法。
