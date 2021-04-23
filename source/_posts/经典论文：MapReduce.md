---
title: 经典论文：MapReduce
date: 2021-03-16 22:35:14
tags: MapReduce, Cloud Computing
---

前言：这是《云计算》课程指定的论文阅读笔记。

> MapReduce

[TOC]

### 0. 摘要

**概念定义：**MapReduce是一个 programming model，可以用来处理和生成大量数据集。

**方法简介：** specify 一个 map 函数处理键值生成一套中间键值对，再用 reduce 函数根据相同的中间键合并中间值。

**方法特点：**可以并行地在大规模集群上使用。run-time system会帮助处理执行中的细节。这会帮助没有经验的用户使用并行和大型分布式系统。

**落地现状和贡献：**有大量的集群正在使用MapReduce；帮助程序员简单地使用。

### 1. Introduction

谷歌每天要处理大量概念直白，但计算上困难（数据量大，计算资源分配量多）的任务。需要解决任务中的并行化计算，分布存储数据，处理错误。

Jeffrey Dean 等人的想法来自于Lisp的*map*和*reduce*以及其他的函数式语言。方法的简介。

论文主要贡献。

章节分配。

### 2. Programming Model

计算过程就是输入一组键值对，在产生一组键值对。由两个方程，*Map*和*Reduce*实现，两个方程均由用户书写完成。

![](.\经典论文：MapReduce\MapReduce函数相关类型.png)

### 3. Implementation

![](.\经典论文：MapReduce\执行概览.png)

> 如上图所示，用户的MR程序，在运行时会fork出一个唯一的Master，以及众多用于执行map及reduce的worker进程，并分散到多台物理机上执行。其中，Master负责记录每个切分后的任务的状态(idle、in-process、completed)，Worker负责从master领取分配给自己的任务执行具体计算。
>
> 每次运行的时候，会启动M个用于执行的Worker和R个用于执行Reduce的worker。map和reduce所需要的数据所在地的信息，都经由master中转。比如上游map产出的抓取地址、上游map任务的执行进度等，这些基本信息都在master有记录。**所有的map、reduce的协调工作，由master负责**。这种结构显著简化了系统的设计，避免了worker之间的各种通讯和协同问题。
>
> 引用来源：https://lvsizhe.github.io/course/2020/03/mapreduce.html

1. split input files into M pieces & copy programs
2. one of copy is the master, others are workers
3. worker do map function & buffer intermediate key/value in memory
4. period write buffered pairs into local disk &  buffered pairs 被分配给 R partition function，注意在每个map tasks生成的intermediate key/value中都要根据key分配给几乎所有的reduce tasks。
5. master 通知 reduce workers 目标位置，后者 remote calls buffered data 然后 sorts intermediate keys
6. reduce worker 迭代地将每个key及其list(value)给reduce function，最后将结果append 在一起到 output file
7. 所有 map和reduce tasks 结束后，分会调用用户主程序，往往返回结果R个output files ，且不做合并。

### 4. Refinements

**划分函数：**使用hash函数均分任务。e.g. `hash(key) mod R`

**一个partition中的排序key：**以方便输出文件的查询货后续的用户处理

**可自定义的Combiner function：**该方程在each machine上的map任务后执行，和 reduce function 功能类似。

### 8. Conclusions

**方法成功的原因：**

1. 模型方便使用。因为其隐藏了并行化、容错机制、局部优化和负载平衡的细节。
2. 模型可以很简单地表达很多问题。有很多应用举例。
3. 在集群上的可扩展性强。可以方便地利用大量地计算资源。

**一般性的规律总结：**

1. 限制程序模型以使其更简单地并行，分布计算且能保证容错率。
2. 网络带宽是稀缺的资源，要尽量减少上下行数据量。
3. 冗余执行（ redundant execution ）可以用来减少低性能机器带来的影戏，并能处理机器宕机和数据损失。

