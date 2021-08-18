---
title: "【论文简读】【AdvMix】When Human Pose Estimation Meets Robustness: Adversarial Algorithms
  and Benchmarks"
date: 2021-08-18T11:20:00+08:00
tags:
- 论文阅读
- 数据增强
- 姿态估计
categories: ''

---

> 写在前：
> 
> 记录📝论文重点贡献、做法等
>
> （领会理解文章的做法）

## 相关信息💻

> 论文在：CVPR2021
> 

- 简称：AdvMix

- 论文地址：[https://arxiv.org/abs/2105.06152](https://arxiv.org/abs/2105.06152)





## 贡献点💡🌟

- 我们提出了三个健壮的基准测试COCO-C、MPII-C和OCHuman-C，并证明了自顶向下和自底向上的姿态估计器在损坏的图像上都遭受了严重的性能下降，引起了社区对这个问题的关注。

- 通过大量的实验，我们得到了许多有趣的结论，这些结论将有助于提高未来工作的准确性和稳健性。

- 我们提出了一种**结合知识蒸馏的对抗数据增强方法AdvMix**，该方法与模型无关，且易于实现。它显著提高了姿态估计模型的鲁棒性，同时保持或略微提高了对干净数据的性能，而没有额外的推断计算开销。


## 摘要/介绍🤓

人体姿态估计是计算机视觉中一项基础而又富有挑战性的任务，其目标是定位人体的关键点。然而，与人类视觉对各种数据损坏(如模糊和像素化)的健壮性不同，当前的姿态估计器很容易被这些损坏所混淆。

本文通过建立COCO-C、MPII-C和OCHuman-C严格的鲁棒基准来评估当前高级位姿估计器的弱点，对这一问题进行了全面的研究和解决，并提出了一种新的**AdvMix算法**来提高它们在不同腐蚀情况下的鲁棒性。

我们的工作有几个独特的好处。

(1) AdvMix是模型无关的，能够在广泛的姿态估计模型中使用。

(2) AdvMix由对抗性增强和知识蒸馏两部分组成。对抗性增强包含两个神经网络模块，它们以对抗性的方式进行联合和竞争训练，其中生成器网络混合不同的损坏图像来混淆姿态估计器，通过学习更难的样本来提高姿态估计器的鲁棒性。为了通过对抗增强对噪声模式进行补偿，采用知识分解的方法将清晰的位姿结构知识转移到目标位姿估计器中。

(3)大量实验表明，AdvMix显著提高了姿态估计在广泛的腐败范围内的稳健性，同时在各种具有挑战性的基准数据集中保持了对干净数据的准确性。



## 方法🤔

### 鲁棒的的姿态基准

#### 基准数据集

- COCO-C Dataset.

- MPII-C Dataset.

- OCHuman-C Dataset.


#### 评价标准


### 对抗增强混合(AdvMix)


#### 增强生成器

![advmix-fig-3: Overview of AdvMix. 框架包括两个模块:对抗增强模块和知识蒸馏模块。对抗增强模块包括一个增强生成器和一个姿态估计器。](https://x.arcto.xyz/NXalTC/advmix-fig-3.png)

如图3所示，给定图像$I$，我们首先使用平行列增强策略$\mathcal{O}_{k}$随机生成$K$个不同的增强图像$\mathcal{O}_{k}(I)$。在我们的实现中，我们默认使用K = 2。与原始图像I一起，我们得到一组$K + 1$的proposal图像。利用增强生成器$\mathcal{G}(\cdot, \theta)$输出归一化注意力映射$\mathcal{M}^{(K+1) \times H \times W}$，其中H和W为图像分辨率。注意力映射图$\mathcal{M}$被用作权重来混合提案图像，其中$\odot$是Hadamard乘积。


$$
I_{m i x}=\mathcal{M}_{0} \odot I+\sum_{k=1}^{K} \mathcal{M}_{k} \odot \mathcal{O}_{k}(I)
$$

在图像$I$中，我们选择Grid-Mask和AutoAugment生成随机增强图像，然后通过混合增强生成器的权重来混合这些图像。我们采用U-Net[34]架构构建增强生成器$\mathcal{G}(\cdot, \theta)$，生成用于混合随机增强图像的注意力映射。



#### 热图回归

人体姿态是用二维高斯置信度热图编码的，每个通道对应一个人体关键点。人体姿态估计网络$\mathcal{D}(\cdot, \phi)$的目标是最小化预测热图与地面真实热图Hgt$\mathcal{H}_{gt}$之间的MSE损失:


$$
\mathcal{L}_{\mathcal{D}^{*}}=\left\|\mathcal{D}\left(I_{m i x}, \phi\right)-\mathcal{H}_{g t}\right\|_{2}^{2}
$$

为加强对目标姿态估计器$\mathcal{D}$的监督，我们引入教师姿态估计器$\mathcal{T}$，以蒸馏知识，并提供**软**热图标签。注意，$\mathcal{T}$和$\mathcal{D}$共享相同的架构，而且$\mathcal{T}$在干净的训练数据上预先训练过。$\mathcal{T}$的参数将在训练时固定。为知识蒸馏损失选择MSE损失，即:

$$
\mathcal{L}_{\mathcal{D}_{k d}}=\|\mathcal{D}(I, \phi)-\mathcal{T}(I)\|_{2}^{2}
$$

将姿态估计器$\mathcal{D}$训练时的整体损失函数表示为:

$$
\mathcal{L}_{\mathcal{D}_{k d}}=\|\mathcal{D}(I, \phi)-\mathcal{T}(I)\|_{2}^{2}
$$

其中，α是MSE损失和知识蒸馏损失之间的损失重量平衡。由于模型对α不敏感，故选择α = 0.1作为默认设置。

#### 对抗训练


增强生成器$\mathcal{G}(\cdot, \theta)$和人体姿态估计器增强生成器$\mathcal{D}(\cdot, \phi)$以对抗的方式进行训练。增强生成器$\mathcal{G}(\cdot, \theta)$试图找到最容易混淆的方法来混合随机图像，而$\mathcal{D}(\cdot, \phi)$则从较硬的训练样本中学习更鲁棒的特征。优化目标的定义为:

$$
\mathcal{L}_{\mathcal{G}}=-\mathcal{L}_{\mathcal{D}^{*}}
$$



总的来说，整个学习过程可以定义为具有价值函数$\mathcal{V}(\mathcal{D}, \mathcal{G})$的二人零和博弈:

$$
\mathcal{V}(\mathcal{D}, \mathcal{G})=\min _{\phi} \max _{\theta} \underset{\mathcal{I} \sim \Omega}{\mathbb{E}} \mathcal{L}\left(\mathcal{D}(\mathcal{G}(\mathcal{O}(\mathcal{I}), \theta), \phi), \mathcal{H}_{g t}\right)
$$


## 思考🤔

- 这篇文章主要是在**数据处理**方面下的功夫，提出一种结合了知识蒸馏的对抗数据增强的方法。

- 与模型无关，可以增强姿态估计器的鲁棒性。也就是说，可以与各种姿态估计模型相结合。

- 在对抗训练的过程，还是有些疑问。。。[对抗训练——终极数据增强？](https://zhuanlan.zhihu.com/p/296809584)这个知乎的文章也没有看明白QUQ

- 关于知识蒸馏，之前简单了解思想，根据这篇文章重新回顾一下～[【经典简读】知识蒸馏(Knowledge Distillation) 经典之作](https://zhuanlan.zhihu.com/p/102038521)