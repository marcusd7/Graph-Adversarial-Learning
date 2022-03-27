## 2018 ICML Adversarial Attack on Graph Structured Data

### Keywords: Attack, Q-Learning, node classification & graph classification, RL

本文使用**强化学习(Q-Learning)**的方法来学习对图结构的攻击算法(graph attacker)。

对于adversarial attack settings，本文着重于以下三种：

* white box attack(WBA):白盒攻击，即攻击者可以获得目标分类器的所有信息（包括输出预测信息，梯度信息等）
* practical black box attack(PBA):实用黑盒攻击，即攻击者可以获得目标分类器的输出预测信息。当同时可以获得目标分类器的置信度(confidence)的时候，称为PBA-C。当仅能获得到离散的预测标签值的时候，称为PBA-D。
* restrict black box attack(RBA):限制黑盒攻击，即攻击者仅能询问(query)某些样本的信息。

各种setting的信息量：WBA>PBA-C>PBA-D>RBA。

### Notions：

对于图神经网络而言有两种不同的settings:

* Inductive Graph Classification：即对于每一个图$G_i$都有一个标签值$y_i \in Y=\{1,2,3,...,Y\}$。如此一来，整个模型对每一个图进行分类，预测其标签值。这样的话，测试样本不会出现在训练数据中，这种分类器的优化目标函数可以写作 $\mathcal{L}^{ind}=\frac{1}{N}\sum_{i=1}^NL(f^{inf}(G_i),y_i)$。这种setting对应的是graph classification，即对整个图进行分类。
* Transductive Node Classification：针对node classification。由于对于node classification而言所有的操作都在一张图上完成，这样的话测试数据/结点在训练的过程中也是可见的(accessible)，优化目标函数为$\mathcal{L}^{tra}=\frac{1}{N}\sum_{i=1}^NL(f^{tra}(c_i;G_0),y_i)$

给定已经学习过的分类器和数据集$(G,c,y)\in \mathcal{D}$，对于Graph adversarial attack而言，需要训练一个attacker$g(.,.): \mathcal{G}\times\mathcal{D}\mapsto\mathcal{G} $，即将原图$G=(V,E)$修改成$\widetilde{G}=(\widetilde{V},\widetilde{E})$。其目标函数为

<img src=".\pics\)CL~0OV9$3{9B8B%BDRK})7.png" width="450"/>

### Optimization/RL-S2V

...