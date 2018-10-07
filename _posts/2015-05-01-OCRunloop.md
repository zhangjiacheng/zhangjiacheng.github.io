---
layout: post
title: "Objective-C Thread & RunLoop解析"
date: 2015-05-01 
description: "Analysis of Objective-C Thread & RunLoop"
tag: iOS
---   

## <1>多线程的硬件背景

In the early days of computing, the maximum amount of work per unit of time that a computer could perform was determined by the clock speed of the CPU. But as technology advanced and processor designs became more compact, heat and other physical constraints started to limit the maximum clock speeds of processors. And so, chip manufacturers looked for other ways to increase the total performance of their chips. The solution they settled on was increasing the number of processor cores on each chip. By increasing the number of cores, a single chip could execute more instructions per second without increasing the CPU speed or changing the chip size or thermal characteristics. The only problem was how to take advantage of the extra cores.

过去的处理器处理速度遇到了瓶颈，芯片设计者的应对这一瓶颈的做法是把处理器设计成多核的。相应的软件要跑得更快，更好地利用多核的处理器，就要采取相应的解决方案，这个方案就是－－多线程编程。

## <2>什么是线程
    
Threads are a relatively lightweight way to implement multiple paths of execution inside of an application. At the system level, programs run side by side, with the system doling out execution time to each program based on its needs and the needs of other programs. Inside each program, however, exists one or more threads of execution, which can be used to perform different tasks simultaneously or in a nearly simultaneous manner. The system itself actually manages these threads of execution, scheduling them to run on the available cores and preemptively interrupting them as needed to allow other threads to run.
From a technical standpoint, a thread is a combination of the         kernel-level and application-level data structures needed to          manage the execution of code. The kernel-level structures             coordinate the dispatching of events to the thread and the            preemptive scheduling of the thread on one of the available           cores. The application-level structures include the call stack for storing function calls and the structures the application needs to manage and manipulate the thread’s attributes and state.

多线程是应用内多执行路径的一个轻量级的实现。一个应用中可能存在一个或多个线程，它们可以同时或者基本同时执行各自的任务。系统管理着这些线程的执行，安排它们运行在可用的处理器核上，也可以适时终断它们以允许别的线程运行。

从技术的角度来看，线程就是管理代码执行的的一个处理器核层的数据结构和一个应用层的数据结构的组合。处理器核层的数据结构协调着分发事件给线程，安排线程到一个可用处理器片段上去执行。应用层的数据结构包括存放调用函数的调用堆栈和应用需要用来管理线程属性和状态的数据结构。

## <3>关于Run Loops

Run loops are part of the fundamental infrastructure associated with threads. A run loop is an event processing loop that you use to schedule work and coordinate the receipt of incoming events. The purpose of a run loop is to keep your thread busy when there is work to do and put your thread to sleep when there is none.

Run Loops 是关联着线程的一个基本的基础设施。Run loop 是一个用来协调接受事件和安排工作的事件处理环。它的目的是用来在有工作时保持线程忙碌，无工作时使之休眠。

关于Run Loops 的几点:

-  Run loop 不是单独的，它是跟着线程来的，有了线程就有了它。但它不是必须要用到的。

- iOS 的主线程的Run loop是自动开启的，而除此之外的第二线程都必须手动去开启它。

[Objective-C Thread & RunLoop解析代码见此](https://github.com/zhangjiacheng/TestRunLoopDemo)


## <4>iOS多线程编程的选择

苹果的系统是基于Unix的,Objective-C又是C的超集，你可以自由选择POSIX的pthread相关API现在多线程编程，苹果的多线程技术应该也是由此而来的吧。

当然,Objective-C作为高级语言当然有它的封装实现，它提供了丰富的API让你去选择实现。

不管你去用以上哪种技术去实现多线程编程都不是那么简单。苹果提供了更好的，更简单的技术供你选择，那就是*GCD 和 NSOperation*。
