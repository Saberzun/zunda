---
layout:     post
title:      "复制一个带随机指针的链表"
subtitle:   "链表的random指针"
date:       2016-03-28 12:00:00
author:     "PhoenixDo"
header-img: "img/post-bg-alibaba.jpg"
catalog:    true
tags:
    - 算法
    - 链表
---

## 题目
有一个链表L,其每个节点有2个指针，一个指针next指向链表的下个节点，另一个random随机指向链表中的任一个节点，可能是自己或者为空，写一个程序，要求复制这个链表的结构并分析其复杂性

![赋值链表random](/img/copy_random_link.jpg)

## 思路
如上图所示，复制原链表，需要先将原链表数据节点翻倍并顺序连接

```
    A->A`->B->B`->C->C`->D->D`
```
根据原节点中random指针连接对应的复制节点

```
  A->C  ::  A`->C`

  Radom_node = T->random;
  T->next->random = Radom_node;
  T = T->next->next;
```
random复制完成之后，将新链表中的原链表节点删除

```
T->next = T->next->text;
```
