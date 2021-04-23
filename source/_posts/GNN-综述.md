---
title: GNN 综述
date: 2020-08-11 20:14:45
tags:
---

说明：这是总结、整合GNN相关综述文献的笔记。

## 1 GNN 介绍
图是一种由结点（nodes）和边（edges）构成的数据结构。由于图可以很好地表示社交网络、蛋白质结构、知识图谱等许多领域的研究。图分析关注的问题包括结点分类、边预测和回归。

### 1.1 Motivation
图神经网络（GNNs）首先发展于**卷积神经网络（CNN）**。CNNs 可以提取多尺度的局部空间特征，并将其组合以构造具有高度表达力的表现形式。CNNs 采用的关键技术包括`局部链接`、`权值共享`、`多层网络的使用`。这也是解决图领域问题的关键技术：

1. 图结构是最典型的局部连接的结构。
2. 权值共享相比传统的基于谱的图理论减少了计算开销。
3. 多层结构是解决分层结构的关键技术。

然而 CNNs 只能操作常规的欧几里得数据结构，例如，2D网格或1D序列。具体来说，CNNs 在图中难以定义局部卷积过滤器和池化操作，如`Fig.1`所示。因此，我们需要将 CNNs 推广到图上，实现对非欧式数据的操作。

![Fig.1](fig1-grid-vs-graph.png)

图神经网络的另一个发展动机来自于**图嵌入**。图嵌入是指将图的结点、边或子使用低维向量表示。因此在表示学习思想的指导下，一些方法诸如`node2vec`, `LINE`, `TADW` 有了较大的突破。然而这些图嵌入的方法也存在两个严重的问题：

1. 在编码过程中，没有参数共享。这导致了参数随结点数线性增长，计算性能效率低下。
2. 直接嵌入的方法缺少泛化的能力。这意味着该方法不能处理动态图以及泛化到新的图。

GNNs 的目的是从图结构中聚集信息。因此，GNNs 需要可以输入并（或）输出由元素及其依赖。更进一步，可以同时使用RNN核对图上的扩散过程进行建模。

相较于传统的神经网络，GNNs 有以下三个优点。首先，一个标准的 CNNs 或 RNNs 只能以特定的顺序处理结点的特征。然而， GNNs可以分别传播每个结点并且忽视输入结点的顺序。其次，GNNs 可以在图的结构的指导下进行传播训练而不将结构当作特征的一部分。最后，GNNs 可以从非结构数据（现场图片和故事文本）中生成图。

### 1.2 分类
在文献[2]中，将 GNNs 分为五种。
1. Graph Convolutional Networks；
2. Graph Attention Networks；
3. Graph Auto-encoders；
4. Graph Generative Networks；
5. Graph Spatial-temporal Networks。 

在文献[1]中，存在不同的划分方法。
1. Graph Convolutional Networks & Graph Attention Networks 为一类；
2. Graph Spatial-temporal Networks；
3. Graph Auto-encoders；
4. Graph Generative Networks


## 2 MODELS

*待续*

## REFERENCES
[1] Zhou J, Cui G, Zhang Z, et al. Graph neural networks: A review of methods and applications[J]. arXiv preprint arXiv:1812.08434, 2018.
[2] Z. Wu, S. Pan, F. Chen, G. Long, C. Zhang, and P. S. Yu, “A comprehensive survey on graph neural networks,” arXiv preprint arXiv:1901.00596, 2019.