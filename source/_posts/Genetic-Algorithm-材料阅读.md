---
title: Genetic Algorithm 材料阅读
date: 2021-03-16 10:11:23
tags: Genetic Algorithm, Process Algebra
---

前言：这是《计算智能》课程方法的两篇阅读材料。

> S. Karaman, T. Shima和E. Frazzoli, 《A Process Algebra Genetic Algorithm》, *IEEE Trans. Evol. Computat.*, 卷 16, 期 4, 页 489–503, 8月 2012, doi: [10.1109/TEVC.2011.2160400](https://doi.org/10.1109/TEVC.2011.2160400).
>
> C. Pizzuti, 《A Multiobjective Genetic Algorithm to Find Communities in Complex Networks》, *IEEE Trans. Evol. Computat.*, 卷 16, 期 3, 页 418–430, 6月 2012, doi: [10.1109/TEVC.2011.2161090](https://doi.org/10.1109/TEVC.2011.2161090).



[TOC]



## Ⅰ.  A Process Algebra Genetic Algorithm

### 1. 什么是进程代数（ Process Algebra ）

> 进程代数英文为：process algebra，在英文中，这个词组中的process代表一个system(系统的)behavior（行为）；一个系统就是一个能表现出各种行为的事物，在计算机世界，process主要指一个软件系统的行为；这句话很抽象，说白了就是，一个软件系统可以表现为一个动作（action），比如转换一个文件的格式，也可以发生一个个事件（event），比如格式转换完毕，另外，一个软件系统也可以**在一定的序列下**完成一系列动作（action）；我们可以从各个角度（aspect）去观察一个系统的行为。研究者往往会关注一个角度的系统行为，这是他们会把系统进行抽象，称这种抽象为对系统行为的一种观察（observation）
>
> 来自互联网资源：https://www.cnblogs.com/dengfanxin/archive/2009/06/08/pa.html

### 2. 摘要

**提出的算法：**使用process algebra来编码遗传算法中的遗传基因（解决方案）和遗传操作。

**算法的应用：**应用于 mission planning 和 optimization problems。

**应用的示例：**高等级的 mission planning of UAV 集群合作问题。

**算法的详解：**对于mission planning problem，solutions to the assignment problem，The evolutionary operators  在文章中使用的定义，方法和工作。

**验证的方法：**介绍本文算法的验证方法。使用模拟的方法验证，并在小中大的问题上得到了验证。

## Ⅱ. A Multiobjective Genetic Algorithm to Find Communities in Complex Networks

### 1. 摘要

**提出的算法：**使用多目标遗传算法来解释复杂网络中的社区结构。

**算法的应用：**优化两个目标方程可以用于识别多个密集连接的节点组，而节点组内部是稀疏连接的。

**算法的详解：**生成一组由不同层次结构等级的网络。其中，解决方案（基因）在更深的层次。介绍moduel个数的决定方法。

**验证的方法：**在合成的和现实的网络上实验，都达到了SOTA的效果。

