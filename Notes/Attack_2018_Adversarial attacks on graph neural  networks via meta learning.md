### Adversarial attacks on graph neural  networks via meta learning
### 2019 ICLR

**Keywords**: Gray-box attack, global attack, greedy algorithm, meta gradients

**本文思路概况**： 本文主要提出了通过使用meta gradients来将欲学习的矩阵结构（邻接矩阵）作为超参数来优化并结合贪心算法来得到攻击图（本文仅考虑改变图结构的攻击，使用meta gradient相当于是将meta gradient作为组成衡量每次改动是否有效的scoring function的一部分）

**重点**：
1. 使用meta-gradient来将欲学习的adjacency matrix作为超参数来直接优化。
2. 本文的攻击目标是global attack，即目标是降低整体模型的表现性能，而不是某几个结点的分类正确与否。

### Methods

文章的整体目标是想要降低目标模型的全局性能，即总体优化目标函数为（是training-phase attack，即可以被建模成一个bi-level优化问题）：

<img src=".\pics\pic1_2019_meta_attack.png" width="700"/>

上式中$\mathcal{L}_{atk}$即为攻击损失函数，而由于是global attack，其所选择的损失函数类型可以是：
1. $\mathcal{L}_{atk}=-\mathcal{L}_{train}$，即想要降低模型的整体表现/性能，可以降低模型训练的拟合程度，当模型在训练阶段的性能都较差的时候，该模型的整体表现/性能相对而言便相对低。
2. $\mathcal{L}_{atk}=-\mathcal{L}_{self},\ \mathcal{L}_{self}=\mathcal{L}(\mathcal{V}_U,\hat{\mathcal{C}}_U)$，该式意为attacker可以使用图中已知结点特征及其标签值来进行self-learning，并计算无标签的结点的loss（这是因为针对图结构的学习一般都是transducive learning，即图结构中某些结点的标签值是已知的，而其余结点的标签值的未知的）

使用meta-gradients将adjacency matrix作为一个超参数并计算$\mathcal{L}_{atk}$对其的loss function可得：

<img src=".\pics\pic2_2019_meta_attack.png" width="700"/>

其中$opt(\cdot)$为某个优化过程，如stochastic gradient descent或者是其variants，met-gradients意味着attacker loss会在微笑的扰动后变化多少，故有：

<img src=".\pics\pic3_2019_meta_attack.png" width="700"/>

至此，使用meta-gradients计算出了不同的扰动对于最终的attacker loss的影响，故meta-gradients即可以当作衡量不同扰动正确性的scoring function（就像是2018 KDD Adversarial Attacks on Neural Networks for Graph Data(Nettack)中贪心算法的评价函数）

由于是gray-box attack，文章仍使用GCN网络模型作为surrogate model，由于图结构的离散性，文章仍使用贪心算法，通过定义$S:\mathcal{V}\times\mathcal{V}\rarr \mathcal{R},\ S(u,v)=\nabla_{a_{uv}}^{meta}\cdot (-2\cdot a_{uv}+1)$，并在所有符合**unnoticeable perturbation**的边增减中使用贪心算法在符合budget的情况下寻找最得分最高的扰动：

<img src=".\pics\pic4_2019_meta_attack.png" width="700"/>

由于meta-gradient的计算问题，文中还提出了近似计算的方法，总体的算法流程图如下所示：

<img src=".\pics\pic5_2019_meta_attack.png" width="700"/>
<img src=".\pics\pic6_2019_meta_attack.png" width="700"/>