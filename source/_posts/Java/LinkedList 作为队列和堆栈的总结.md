---
title: Java-LinkedList 作为队列和堆栈的总结
date: 2020-08-04 17:39:37
categories:
- Java
comments: true
---

刷 LeetCode 的时候，发现 LinkedList 可以同时作为 堆栈 和 队列 使用，记录一下它们作为不同数据结构使用时的用法。

<!-- more -->

|                            | 堆栈                                                         | 队列                                                         |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 声明                       | `Deque<T> stack = new LinkedList<T>();`                      | `Queue<T> q = new LinkedList<T>();`使用java.util.Queue接口，其底层关联到一个LinkedList实例，由于只暴露部分基于队列实现的接口，所以可以提供安全的队列实现 |
| 入                         | `void addFirst(T v)、void push(T v)`                         | `void offer(T v)`                                            |
| 出                         | `T poll()、T pop()`                                          | `T poll()`, 如果队列为空，则返回null； `T remove()`, 如果队列为空，则抛出异常 |
| 偷看(看看队首元素不移除它) | `T peek()`， 如果队列为空，则返回 null ；`T element()`，如果队列为空，则抛出异常 | `T peek()`， 如果队列为空，则返回 null ；`T element()`，如果队列为空，则抛出异常 |
| 是否为空                   | `boolean isEmpty()` 空返回 true，否则返回 false              | `boolean isEmpty()` 空返回 true，否则返回 false              |

注意：都可以直接声明LinkedList  `LinkedList<T> stack = new LinkedList<T>(); || LinkedList<T> q = new LinkedList<T>();`

