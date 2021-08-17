---
title: "【论文阅读】ViPNAS: Efficient Video Pose Estimation via Neural Architecture Search"
date: 2021-08-18T02:35:00+08:00
tags:
- NAS
- 论文阅读
- 姿态估计
categories: ''

---

> 写在前：
> 
> 记录📝
> 
> 在仔细阅读之前，搜索发现了已经有人翻译or解释过此篇文章，也当作借鉴，链接🔗放在文章靠后的位置。
> 

## 亮点💡

- 提出在视频中探索时间特征的融合以及自动计算分配（文中说是第一个提出的）

## 相关信息💻

> 论文在：CVPR2021
>
> 作者单位：香港中文大学、商汤科技、香港大学、SenseTime Research and Tetras.AI、悉尼大学
> 

- 简称

- 论文地址：[https://arxiv.org/abs/2105.10154](https://arxiv.org/abs/2105.10154)

- 代码地址：[https://github.com/luminxu/ViPNAS](https://github.com/luminxu/ViPNAS)




## 贡献点💡
1. 提出了一种新的用于视频姿态估计的时空神经结构搜索(NAS)框架，称为ViPNAS
2. ViPNAS学会在跨帧的总计算复杂度约束下为不同的帧分配计算资源(例如Flops)
3. ViPNAS自动搜索时间连接，即融合模块和位置。在视频姿态估计的任务中，我们在CPU**实时**性能(>25FPS)下达到了SOTA。

## 摘要🤓

## 介绍

- 轻量模型：在本文中，我们的目标是构建一个轻量级的姿态估计器，达到SOTA并显著降低模型复杂性。

- 针对现状提出问题：低维特征高维特征，如何融合？什么时候融合？


- 解决：NAS（网络搜索结构），提出ViPNAS。

我们提出了一种新的用于视频姿态估计的时空NAS框架，称为ViPNAS。对于空间层面的搜索，我们通过五个维度(深度、宽度、核大小、组数和注意力)来优化神经结构。对于时间层面的搜索，我们共同搜索三个方面的设计:1)特征融合阶段，2)特征融合操作，3)计算在视频帧间的分配。空间层次和时间层次的搜索通过一个单一的框架共同优化。给定多个帧上的总Flops作为约束，我们可以有效地跨不同视频帧分配计算，以优化性能。



![vipnas-fig-1](https://x.arcto.xyz/8Xk_Vw/vipnas-fig-1.png)

图1所示。PoseTrack2018验证集的速度-精度权衡。该文章提出的ViPNAS的精度可与最先进的网络相媲美，它可以在**显著降低计算量**的情况下实现**CPU实时**。




## 相关工作🤔

### 人体姿态估计


### 神经结构搜索（NAS）

#### 图片级任务的NAS

#### 视频级任务的NAS

#### 单张图姿态估计的NAS

**小结**：
我们的工作与现有的NAS工作有三个不同之处。
1. 首次将NAS应用于**视频**姿态估计

2. 现有的图像级和视频级任务并不会同时搜索不同的架构，但我们的工作搜索框架专用模型，以便进一步利用NAS在视频姿态估计中的能力。

3. 提出了新的时空搜索空间。

为了充分利用时间信息，我们在将前一帧的特征融合到当前帧时，寻找最优的组合，这是之前工作没有探索的。该方法还继承了once-for-all的优点，即只训练一次，获得多个子网，有效地降低了搜索成本。


## 方法

### Overview

![vipnas-fig-2](https://x.arcto.xyz/KpsoY8/vipnas-fig-2.png)


视频姿态估计的目的是在每一帧中定位人的人体部位(也称为关键点或关节)。

本文在在线姿态传播范式的基础上，提出了一种高效视频姿态估计框架(ViPNAS)。如图2所示。每$T + 1$帧选取第一帧作为关键帧。**对于关键帧**，采用高精度空间视频姿态估计网络(S-ViPNet)对人体姿态进行定位。我们遵循常用的设置，使用热图将关节位置编码为高斯峰。**对于非关键帧**，采用轻量级时间视频姿态估计网络(T-ViPNet)进行姿态传播。在T-ViPNet中，利用CNN的某些层提取当前帧的特征，然后利用时间特征融合模块将当前帧的特征与最后一帧的热图进行融合。融合的特征然后由T-ViPNet的剩余CNN层处理，以获得热图。预测的热图对每个关节的像素似然进行编码，作为指导后续帧关键点定位的信息线索。传播一直持续到下一个关键帧。
ViPNAS包含两层搜索空间，即空间层和时间层。在空间级搜索空间中搜索关键帧S-ViPNet的结构。对非关键帧(T-ViPNets)的架构，包括时间特征融合模块和CNN层，在空间级和时间级的搜索空间中进行搜索。不同的非关键帧在特征融合模块(融合操作和特征融合阶段)和CNN层中都有不同的架构，如图2中的t + 1和t + 2帧所示。

### 空间级搜索空间

设计了权值共享super-network，用于模型结构搜索、块数搜索和块结构搜索。我们的超网络被串联成几个阶段，每个阶段由几个具有相同输出特征空间分辨率的块组成。我们在以下五个维度进行搜索:
- 弹性深度:每个阶段的块数。当深度D被选为这个阶段时，我们激活阶段的第一个D块。

![vipnas-fig-a1](https://x.arcto.xyz/3WYNEQ/vipnas-fig-a1.png)


- 弹性宽度:每个块的输出通道数量。当宽度W被选择时，我们保留第一个W过滤器。

![vipnas-fig-a2](https://x.arcto.xyz/DLESLr/vipnas-fig-a2.png)


- 弹性核大小:每个块中卷积层的克尔大小。在选择kernel大小K时，保留中心K卷积核。对于正卷积层，核大小K的可能选择为3,5,7，对于反卷积层，核大小K的可能选择为2,4。

![vipnas-fig-a3](https://x.arcto.xyz/Bmoxmz/vipnas-fig-a3.png)

- 弹性组数:每个块中卷积层[22]的组数。对于N个输入通道，它的范围从1(标准卷积)到N(深度卷积)。

![vipnas-fig-a4](https://x.arcto.xyz/s7qWEK/vipnas-fig-a4.png)

- 弹性注意模块:在每个块的末尾是否使用注意模块。由于注意模块在[8,48]中被证明是有效的姿态估计，我们在搜索空间中包含注意模块。我们研究是否在每个块的末尾使用注意模块(例如GC块[4]或SE块[16])。如果没有选择注意模块，则跳过注意模块，应用身份映射。

![vipnas-fig-a5](https://x.arcto.xyz/PwEc9E/vipnas-fig-a5.png)




### 时间级搜索空间


### ViPNAS的训练和搜索

#### S-ViPNet的训练和搜索

基于3.2节定义的空间级搜索空间，我们使用[61]中的方法来训练超网络。采用三明治规则[60,61]和就地蒸馏[60,61]。然后对给定约束条件下的子网络进行抽样，在验证集上对每个子网络进行评估，从而搜索关键框架网络S-ViPNet的体系结构。


#### T-ViPNet的训练

在本节中，我们将介绍我们的ViPNAS的多帧传播训练方案。目标是在跨多个视频帧的姿态传播过程中，同时在空间和时间水平上优化整体模型的精度。总体目标函数可以表示为

$$
\min _{\theta_{\mathcal{T}}} \sum_{t=1}^{T} \sum_{\text {arch }^{t}} \mathcal{L}\left(\mathcal{T}\left(I^{t}, H^{t-1} ;\left\{\theta_{\mathcal{T}}, \operatorname{arch}^{t}\right\}\right)\right)
$$

其中，

$$
H^{t}= \begin{cases}\mathcal{T}\left(I^{t}, H^{t-1} ;\left\{\theta_{\lambda}, \operatorname{arch}^{t}\right\}\right), & t \geq 1 \\ \mathcal{S}\left(I^{t} ;\left\{\theta_{\mathcal{S}}\right\}\right), & t=0\end{cases}
$$

$\mathcal{S}$是关键帧模型S-ViPNet，其权值为$\theta_{\mathcal{S}}$。在训练和搜索T-ViPNets时，它是预先训练和固定的。$\mathcal{T}$是T-ViPNets的超网络，由$\theta_{\mathcal{T}}$参数化。在训练过程中，我们从$\mathcal{T}$中抽取架构$\text{arch}^{t}$组成的子网络，并从超网络权值$\theta_{\mathcal{T}}$中复制该架构的权值。对于每一帧$t$，$I^{t}$是输入图像，$H^{t}$是预测的热图。我们使用MSE损耗函数$\mathcal{L
}$来测量每个非关键帧的目标热图和预测热图之间的差异.

![vipnas-fig-3](https://x.arcto.xyz/m9uDUP/vipnas-fig-3.png)


#### 自动计算分配


## 实验🧪

### 数据集

- COCO2017 Dataset

- PoseTrack2018 Dataset


### 细节


### ViPNAS用于高效的视频姿态估计

![vipnas-tab-1](https://x.arcto.xyz/tZ4DQV/vipnas-tab-1.png)

表1:在PoseTrack2018验证集上与其他视频姿态估计方法进行了比较。ViPNAS实现了最先进的性能，显著降低了计算复杂度。

### ViPNAS用于基于图像的姿态估计

![vipnas-tab-2](https://x.arcto.xyz/AJec0p/vipnas-tab-2.png)

表2：对比COCO2017数据集。在COCO val2017 set和test-dev2017 set上，我们的方法在速度和准确性方面显著优于其他手工和NAS模型。“十字架”表示使用更强的人包围盒探测器(HTC[5])。


### 消融实验

## 结论


## CITE🔗
- [ViPNAS: Efficient Video Pose Estimation via Neural Architecture Search论文解读](https://blog.csdn.net/weilaicxy22/article/details/117406595)


## 那...什么是NAS？

- [NAS（神经结构搜索）综述](https://zhuanlan.zhihu.com/p/60414004)

- [神经网络架构搜索（Neural Architecture Search）杂谈](https://blog.csdn.net/jinzhuojun/article/details/84698471)

- [神经架构搜索(NAS)2020最新综述：挑战与解决方案](https://zhuanlan.zhihu.com/p/146138419)

- [一篇 NAS 的介绍](https://zhuanlan.zhihu.com/p/45133026)


- B站视频：神经网络结构搜索
<iframe src="//player.bilibili.com/player.html?aid=460871780&bvid=BV195411T7gX&cid=343774864&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

- [FBNet: Hardware-Aware Efficient ConvNet Design via Differentiable Neural Architecture Search](https://arxiv.org/abs/1812.03443)



