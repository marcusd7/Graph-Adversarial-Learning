### Adversarial Examples on Graph Data: Deep Insights into Attack and Defense
### 2019 IJCAI

**Keywords**： White-Box Attack, Integrated Gradients based, FGSM & JSMA

**主要思路概况**：文章首先通过说明传统的攻击方法（例如FGSM与JSMA等通常用于攻击图像模型的方法）在攻击图结构模型的时候所遇到的问题（即local gradient problem），引出了归因(attribution)方法Integrated Gradients来分析对于网络的图结构来说，哪些结点特征/邻接边更加重要，随后直接攻击这些重要的特征，得到attacked graph。对于防御方法来说，文章通过观察发现攻击方法通常通过**将相似度较低的两结点相连接或者破坏两结点之间的连接关系**来从attacked graph中删除这些不合理的邻接关系，从而完成防御

**重点**：
1. Integrated Gradients是为了更深一步的理解deep neural model而提出的方法，是归因方法(attribution)，相较于普通的gradients-based方法来解释模型中的特征的重要性程度，integrated gradients更加合理。

**另外的reference**：
1. JSMA attack on graph: 2015 The Limitations of Deep Learning in Adversarial Settings
2. Integrated gradients: 2017 Axiomatic Attribution for Deep Networks

### Preliminary Setting

对于图结构这种离散数据结构来说，**用在诸如图像模型的攻击方法通常并不如图像模型一样奏效**，主要的原因就是由于**图结构的离散型**，为了解决图结构的离散性问题，很多文章提出了不同的方法：
1. 贪心策略 
2. 将离散结构连续化(如$\{0,1\}^N\mapsto [0,1]^N$)，随后便转化成了一个连续优化问题，在解决该连续优化问题后便可以通过特定的方法转换回离散结构

### Attack Methods

FGSM方法通过计算模型的损失函数对每个输入特征的导数(gradients)来衡量哪些特征对于模型性能的提升更为重要，为了攻击该模型便可以按着最大导数的反方向更新参数，即$\eta=\epsilon \ sign(\nabla J_\theta(x,l)),\ x^{'}=x+\eta$

JSMA方法则是通过计算模型的前向导数(forward derivations，Jacobian Matrix，$\nabla F=\partial F/\partial X$)来计算模型的输出对于输入特征的重要性，JSMA与FGSM的区别有：
1. FGSM是通过输出的损失函数对输入特征的梯度决定，相当于是反向传播，而JSMA是通过输出函数对输入的梯度来决定，相当于是前向传播，从输入传播到输出。
   
其主要通过计算并使用saliency map来得到

<img src=".\pics\pic1_2019_IGattack.png" width="450"/>

对于targetted attack，若想使某一结点被分类为类$t$，则我们希望$F_t(X)$增大而其余缩小，反应在梯度上即希望$\partial F_t(X)/\partial X_i<0$，并且其余$j\neq t$的梯度总和大于0。上图中第二行的公式中后面的绝对值求和项保证了对于所有特征而言都有相同的比较基准。

但是2017 Axiomatic Attribution for Deep Networks文中提出Gradient并不是一种有效的衡量attribution(即相当于哪些特征对于最终的结果更为重要)的标准。以RelU函数举例，当输入$x:0\rarr 1$时，$ReLU(x):0\rarr 1$，但当$x=0$时，$\partial ReLU(x)/\partial x=0$，这明显是不合理的。故原文中使用Integrated Gradient来代替普通的Gradient来衡量Attribution。Integrated Gradients是从baseline到原特征的路径积分，baseline通常是使得原模型为0的数据（对于图像而言可能是全黑图像all-zeros/all-ones）

<img src=".\pics\pic2_2019_IGattack.png" width="450"/>

随后根据以上公式分别计算更加重要的结点特征或是图中的邻接边，挑选最重要的结点特征/边来干扰，最终得到attack graph。

### Defense Methods

对于使用以上方法以及其他方法得到的攻击图而言，攻击图中倾向于将**将相似度较低的两结点相连接或者破坏两结点之间的连接**，通过在attack graph中利用衡量similarity的指标删去/相似度低的结点对或是增加相似度高得结点对，便可以在一定程度上从attack graph中恢复出原图。