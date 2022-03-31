### Adversarial Attacks on Neural Networks for Graph Data(Nettack)
### 2018 KDD

**Keywords**：Gray-box attack, greedy algorithm, unnoticeable perturbation check, training-time attack(poisoning attack)

**主要思路概况**：本文主要通过使用gray-box attack来攻击图结构中具体某些结点，使其在node classification等任务中被误分类。由于是灰盒攻击，攻击者不具备model structure/parameters等信息，仅获知training data，即可以获知给定图的邻接矩阵或者结点特征，故需要**攻击surrogate model（假设或在后续证明该攻击具有转移性/transferability，即在surrogate模型上有效即代表在其余模型上也有类似作用）**。随后，使用贪心算法生成攻击图。本文还显式的定义了何为unnoticeable perturbation，并提出了检验的方法。

**重点**：
1. 解决图结构离散问题的另一方法即为设计**针对不同修改(edge insertion/deletion)的评价函数**，并使用greedy algorithm来选取不同的修改方法。
2. unnoticeable perturbation的定义以及检查方法。

### Methods

对于targetted attack而言，其目的是通过修改attack nodes $\mathcal{V}_A\subset\mathcal{V}$ 结点的node features/connection edge从而使得target node在node classification等任务中被误分类，而由于本文侧重于training-time attack/poisoning attack，故是一个bi-level/min-max优化问题，其优化表达式为:

<img src=".\pics\pic1_2018_nettack.png" width="450"/>

由于是灰盒模型，文中使用两层GCN为surrogate模型$Z^{'}=softmax(\hat{A}\hat{A}XW^{(1)}W^{(2)})=softmax(\hat{A}^2XW)$

故原优化函数即为：

<img src=".\pics\pic2_2018_nettack.png" width="450"/>

但是由于图结构的离散性，针对以上方程式的优化仍较为困难，因此文中通过greedy algorithm来选择最符合的修改，通过定义向一个特定图$G=(A,X)$增加/减少一个特征$f=(u,i)$或是增加/减少一个边$e=(u,v)$这一个动作的评价函数为：

<img src=".\pics\pic3_2018_nettack.png" width="450"/>

随后既可以通过贪心算法，在每次选择中选择评价函数得分最高的修改方法，修改原图结构，具体算法流程图：

<img src=".\pics\pic4_2018_nettack.png" width="450"/>

### Unnoticeable Perturbation

文中定义图中的度分布(degree distribution)情况为判断扰动图是否noticeable的方法，即为每次的可能的扰动后的生产图的度分布与原图的度分布进行比较（similarity比较等方法）。

文中认为图结构中的结点度分布一般是power-law分布，且认为$p(x)\propto x^{-\alpha}$，且：

<img src=".\pics\pic5_2018_nettack.png" width="450"/>

后续仅需对每次扰动后的生成图计算其$\alpha_G$并与原图的度分布做similarity检测即可判断该扰动是否是Unnoticeable Perturbation