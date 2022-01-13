---
title: K个一组翻转链表
date: 2021-12-27 21:15:48
categories:
- 数据结构
comments: true
---

[**题目描述：**](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

k 是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

<!-- more -->

进阶：

- 你可以设计一个只使用常数额外空间的算法来解决此问题吗？
- 你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

> 示例 1：
>
> <img src="https://assets.leetcode.com/uploads/2020/10/03/reverse_ex1.jpg" alt="img" style="zoom:50%;" />
>
> 输入：head = [1,2,3,4,5], k = 2
> 输出：[2,1,4,3,5]
>
> 示例 2：
>
> <img src="https://assets.leetcode.com/uploads/2020/10/03/reverse_ex2.jpg" alt="img" style="zoom:50%;" />
>
> 输入：head = [1,2,3,4,5], k = 3
> 输出：[3,2,1,4,5]
>
> 示例 3：
> 输入：head = [1,2,3,4,5], k = 1
> 输出：[1,2,3,4,5]
>
> 示例 4：
> 输入：head = [1], k = 1
> 输出：[1]



## [模拟](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/solution/k-ge-yi-zu-fan-zhuan-lian-biao-by-leetcode-solutio/)

- 把链表节点按照 k 个一组分组，使用一个指针 head 依次指向每组的头节点

- 这个指针每次向前移动 k 步，直至链表结尾

- 对于每个分组，我们先判断它的长度是否大于等于 k。若是，我们就翻转这部分链表，否则不需要翻转

- 反转子链表的思路是【反转链表】，除了翻转其本身之外，还需要将子链表的头部与上一个子链表连接，以及子链表的尾部与下一个子链表连接

![fig1](https://assets.leetcode-cn.com/solution-static/25/25_fig1.png)

一个步骤后是如下

<img src="https://assets.leetcode-cn.com/solution-static/25/9.PNG" alt="img" style="zoom:50%;" />

```js
const reverseList = (head, tail) => {
  let prev = tail.next, cur = head;
  while (prev !== tail) {
    const next = cur.next;
    cur.next = prev;
    prev = cur;
    cur = next;
  }
  return [tail, head];
}
var reverseKGroup = function(head, k) {
  const dummyHead = new ListNode(0);
  dummyHead.next = head;
  let prev = dummyHead;

  while (head) {
    let tail = prev;
    // 查看剩余部分长度是否大于等于 k
    for (let i = 0; i < k; i++) {
      tail = tail.next;
      if (!tail) {
        return hair.next;
      }
      // 如果是不足 k个也要反转
      // if (!tail.next) {
      //  break
      // }
    }
    const next = tail.next;
    [head, tail] = reverseList(head, tail);
    // 把子链表重新接回原链表
    prev.next = head;
    tail.next = next;
    prev = tail;
    head = tail.next;
  }
  return dummyHead.next;
};
```

**复杂度分析**

- 时间复杂度：O(n)，其中 n 为链表的长度。head 指针会在 O([n/k]) 个节点上停留，每次停留需要进行一次 O(k) 的翻转操作。

- 空间复杂度：O(1)，我们只需要建立常数个变量。

