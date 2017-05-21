---
layout:     post
title:      "iOS 多线程技术"
subtitle:   "NSThread、NSOperation、GCD"
date:       2016-03-28 12:00:00
author:     "PhoenixDo"
header-img: "img/post-bg-alibaba.jpg"
catalog:    true
tags:
    - 线程
    - iOS
---

## iOS多线程技术
（1）、NSThread<br>
（2）、NSOperation<br>
（3）、GCD   Grand Central Dispatch

## NSThread

线程声明和调用方法：

```
(1) [NSThread detachNewThreadSelector:@selector(threadMethod:) toTarget:self withObject:nil];

(2) NSThread *myThread = [[NSThread alloc] initWithTarget:self selector:@selector(threadMethod) object:nil];

(3) [obj performSelectorInBackground:@selector(threadMethod) withObject:nil];

(4) //调用主线程 object为向主线程方法传递的数据对象
    [self performSelectorOnMainThread:@selector(mainMethod) withObject:object waitUnitDone:YES];
```

在ViewController中运行一个Thread，当Thread还在运行期间，ViewController被release时，Thread并不会结束，而且ViewController也不会销毁，不会执行dealloc方法。

##### 优缺点：
优点：NSThread相对轻量级；<br>
缺点：需要自己管理线程的生命周期，线程同步。线程同步对数据的加锁操作会增加系统开销。

## NSOperationQueue

NSOperation有两种执行方式：并发执行、非并发执行；<br>
NSOperation有两种使用方式：系统定义（NSInvocationOperation、NSBlockOperation）和继承NSOperation

NSOperation和NSOperationQueue实现多线程的具体步骤：
（1）先将需要执行的操作封装到一个NSOperation对象中
（2）然后将NSOperation对象添加到NSOperationQueue中
（3）系统会⾃动将NSOperationQueue中的NSOperation取出来
（4）将取出的NSOperation封装的操作放到⼀条新线程中执⾏


#### NSInvocationOperation子类创建对象、执行操作

```
 NSInvocationOperation *operation=[[NSInvocationOperation alloc]initWithTarget:self selector:@selector(threadMethod) object:nil];

 [operation start];
```
**tip: 操作对象默认在主线程中执行，只有添加到队列中才会开启新的线程。即默认情况下，如果操作没有放到队列中queue中，都是同步执行。只有将NSOperation放到一个NSOperationQueue中,才会异步执行操作**

#### NSBlockOperation子类创建对象、执行操作

```
NSBlockOperation *operation=[NSBlockOperation blockOperationWithBlock:^{//操作}];

//operation添加操作
[operation addExecutionBlock:^{//操作}];
[operation start];

//添加依赖 operation_1执行完毕之后再执行operation
[operation addDependency:operation_1];

//设置completionBlock 当BlockOperation执行完毕后运行completionBlock
operation.completionBlock = ^{//do something };
```
**tip: 当NSBlockOperation中的操作(Block)数>1, 执行operation就会异步进行执行**
<br>

#### NSOperationQueue创建、添加operation

```
NSOperationQueue *myqueue = [[NSOperationQueue alloc] init];
[myqueue addOperation:operation_1];
[myqueue addOperation:operation_2];

[myqueue addOperationWithBlock:^(//操作)];
```
NSOperation可以调⽤start⽅法来执⾏任务,但默认是同步执行的,如果将NSOperation添加到NSOperationQueue(操作队列)中,系统会自动**异步**执行NSOperation中的操作

**添加操作到NSOperationQueue中，operation会自动执行操作，自动开启线程**
 <br>

##### 优缺点
优点：不需要关系线程管理、同步的问题。

## GCD
GCD有三种队列方式，主程序队列、系统线程队列和用户自定义队列

GCD可用于解决数据锁定、资源泄露的问题。 __但GCD并不能完全避免死锁的产生__

```
//主程序队列
dispath_queue_t queue =  dispath_get_main_queue();

//系统线程队列  全局共有
dispath_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT,0);

//用户自定义队列
dispath_queue_t queue = dispath_queue_create("myqueue",NULL);

//同步运行
dispath_async(queue,^{//操作})；

```
