### Topology Attack and Defense for Graph Neural Networks:An Optimization Perspective
### 2019 IJCAI

**ATTENTION!**: white-box attack, projected gradient descent(PGD), Untargetted Attack, pre-trained & re-trainable model, first-order method(传统优化方法)

**主要思路概况**：该文章主要通过将Adversarial attack on graphs建模成**传统的优化模型**来攻击GNN，一般来说对于拓扑攻击(topology attack，即通过改变图结构中的邻接关系)来达到攻击原图结构的目的会由于**图结构自身的离散性**而遇到困难，而本文通过将图中的离散图结构松弛到$[0,1]$的实数范围区间来解决该问题。

**重点**：
1.  其中，若约束受限空间是凸(convex)的，则该PGD有闭式解
2.  对于所有的Poisoning问题，即后续模型可以retrain的问题都可以被总结成一个Min-Max问题

### Preliminary Setting

文中提出的Topology攻击是白盒攻击，即攻击者在攻击的过程中拥有model structrue/parameters, training data这些信息，并主要是通过改变图结构的邻接关系，也就是图的邻接矩阵$A$来尝试攻击。

对于原图的邻接矩阵$A$，文中定义$S\in\{0,1\}^N$表示矩阵中对应边的邻接关系有无被改变，其中若$S_{i,j}=1$即意味着原图中对应边$A_{i,j}$发生了改变（加上或是减少）。定义原邻接矩阵的补矩阵为$\bar{A}=11^T-I-A$，之后定义矩阵$A^{'}=A+C\circ S$，其中$\circ$为element-wise乘积，$C=\bar{A}-A$

矩阵$C$意味着原图结构中$\{i,j\}$的位置上有无边，若有边，对其边结构修改后则需要删去边，这里就意味着$C_{i,j}=-1$，同样若无边，则修改边则意味着增加边$C_{i,j}=1$，配合待学习的扰动矩阵$S$，便可以得到经过攻击后的图结构矩阵$A^{'}$。

### Generating Attacks

为了生成攻击图，需要定义一个攻击损失函数(Attack Loss)，定义$\boldsymbol{Z}(\boldsymbol{S},\boldsymbol{W};\boldsymbol{A},\{x_i\})$为在结点特征值为$\{x_i\}$的情况下的预测概率，则$Z_{i,c}$意为将结点$i$预测为标签值$c$的概率。则定义attack loss function为$f_i(\boldsymbol{S},\boldsymbol{W};\boldsymbol{A},\{x_i\},y_i)=\max\{Z_{i,y_i}-\mathop{\max}_{c\neq y_i}Z_{i,c},\ -\kappa\}$，其中$\kappa$为置信度。（该损失函数为**CW-type loss**，文中还提到了**CE-type loss**）。

故在给定一定量的干扰成本(perturbation budget)的情况下，文中对于两种情况设计了攻击算法：
1. 攻击预训练过的GNN模型，即相当于是Evasion，攻击已训练完成的模型
2. 攻击可以重训练的GNN模型（相当于是min-max优化问题）

#### 对于pre-trained GNN model

对于pre-trained GNN模型来说，生成攻击图的问题可以总结为如下的优化问题

<img src=".\pics\pic1_2019_topologyattack.png" width="450"/>

其中$s$向量为矩阵$S$向量化的结果，$1^Ts\leq \epsilon,\ s\in\{0,1\}^n$即为优化的约束条件，对原邻接矩阵的总改动数量不能超过$\epsilon$

原优化问题是 **combinatorial optimization(组合优化)** 问题（即想要优化的目标向量有离散性）。为解决该问题，将原约束范围松弛到他的convex hull中，即$s\in[0,1]^n$，之后用projected gradient descent方法解决该contrained optimization problem。

**projected gradient descent(PGD)**：为了解决contrained optimization problem而提出，即在一般梯度下降方法的过程中将每个梯度向量投影到约束空间中，即$s^{(t)}=\prod_\mathcal{S}[s^{(t-1)}-\eta_t\hat{\boldsymbol{g}}_t]$，其中$\prod_\mathcal{S}(\boldsymbol{a})=\argmin_{s\in\mathcal{S}}||s-a||^2_2$，即为将每阶段梯度后得到的点往受限空间$\mathcal{S}$上做投影。

**重要： 其中，若约束受限空间是凸(convex)的，则该PGD有闭式解**

在做PGD最终得到$s$的向量后，由于$s$中的值是连续的，故需要离散化，文章中使用Random Sampling方法从最终的概率连续向量$s$生成离散的向量（代表图中的边增减与否）。

<img src=".\pics\pic2_2019_topologyattack.png" width="450"/>

最终的PGD-topology Attack算法流程图为：

<img src=".\pics\pic3_2019_topologyattack.png" width="450"/>

#### 对于re-trainable GNN model

原优化问题可以总结成一个Min-Max问题（**其实对于所有的Poisoning问题，即后续模型可以retrain的问题都可以被总结成一个Min-Max问题**），本文最终使用的方法是first order alternating optimization，即交替固定，迭代求解

<img src=".\pics\pic4_2019_topologyattack.png" width="450"/>

<img src=".\pics\pic6_2019_topologyattack.png" width="450"/>

### Adversarial Training for GNN

按照上文的思路可以使用Adversarial Training的方法来设计更加鲁棒的GNN:
$\min_W\max_{s\in S}-f(s,\boldsymbol{W})$

<img src=".\pics\pic5_2019_topologyattack.png" width="450"/>