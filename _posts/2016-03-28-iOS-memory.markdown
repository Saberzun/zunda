---
layout:     post
title:      "iOS 引用计数"
subtitle:   "assign、retain、copy"
date:       2016-03-28 12:00:00
author:     "PhoenixDo"
header-img: "img/post-bg-alibaba.jpg"
catalog:    true
tags:
    - 内存管理
    - iOS
---

## assign
简单赋值，不改变引用计数
<br>适用与基本类型（NSInteger）和C基本类型

```
-(void)setCount:(int)count{
    _count = count;
}
```

## retain
指针拷贝，retain创建一个指针，原对象引用计数+1

```
-(void)setDog:(Dog *)dog{
    if(_dog != dog){
        [_dog release];
        _dog = [dog retain];
    }
}
```
## copy
内容拷贝，copy创建一个新对象，与旧对象内容相同，新的对象retain为1，与旧对象引用计数无关。

```
-(void)setString:(NSString *)str{
    if(_str != str){
        [_str release];
        _str = [str copy];
    }
}
```
