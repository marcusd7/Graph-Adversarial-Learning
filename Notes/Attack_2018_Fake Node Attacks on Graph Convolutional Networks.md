### Fake Node Attacks on Graph Convolutional Networks
### 2018 

**Keywords**：Fake Node Attack, White-box attack, Gradients-based, GAN-based

**文章主要思路概况**：该文提出了一种有别于其他修改图结构adjacency matrix/feature matrix以实现攻击图模型的方法，**其通过在图中增加fake nodes，使fake nodes与原图中的结点相连，并设计他的features来实现攻击源模型**，这种攻击更加现实。从实现方法上而言，该文是使用gradient-based方法，通过依次搜索magnitude最大梯度来实现对修改fake nodes与原图之间的邻接关系以及特征情况来攻击原图。除此之外，本文还需要考虑如何将fake nodes设计得更加realistic，以至于其不会被data cleaning系统清除。

**重点**：
1. Fake nodes方法，有别于其他攻击方法的思路。
2. 本文还需要考虑如何将fake nodes设计得更加realistic，以至于其不会被data cleaning系统清除（使用GAN方法）
3. **文章并没有交代如何设计/优化增加结点的个数，仅是在实验部分探讨了多少数量的fake nodes效果更好，若将fake nodes个数作为一个优化参数可行？**

### Methods

文章分为target attack/untarget attack，即是否将attack nodes在模型中的分类攻击成指定的某个结点。

文章使用gradients-based method，且由于图结构本身的离散性，使用greedy attack来攻击原模型，欲优化目标函数为$J(A^{'},X^{'})=\sum_{i\in S}(\max([f(X^{'},A^{'})]_{i,:}-[f(X^{'},A^{'})]_{i,y_i}))$，其中$y_i$为结点$v_i$原本的标签值。故因为是untarget attack，故优化目标函数是降低整体网络的预测精度，优化表达式为：

<img src=".\pics\pic1_2018_fake node attack.png" width="700"/>

后面的约束条件为perturbation budget。由于图结构的离散性以上优化问题使用贪心算法来解决，即在一个更新轮次中依次计算所有可能的连接/特征的gradient，挑选gradient最大的进行更新。

<img src=".\pics\pic2_2018_fake node attack.png" width="700"/>

但是除此之外，本文还需要考虑如何将fake nodes设计得更加realistic，以至于其不会被data cleaning系统清除。为了实现该目的，本文使用类似GAN结构增加一个discriminator来在训练过程中尝试辨别出fake nodes，因此Greedy-GAN Attack是一个min-max优化问题，尝试设计出可以欺骗discriminator的fake nodes。优化表达式如下，具体算法流程图如下。

<img src=".\pics\pic4_2018_fake node attack.png" width="700"/>

文中对于target与untarget的定义与其他文章稍有不同，即该文章中的target是干扰图结构，使某些结点分类成为需要的/设定的特定结点，而其余文章中的target与untarget的区别大多是针对干扰某些特定结点还是降低整个图结构的性能。

**文章增加fake nodes的思路较为有趣，但是实现方法仍是gradient-based，从attribution的角度来说，gradient并不是一个好的attribution指标。**