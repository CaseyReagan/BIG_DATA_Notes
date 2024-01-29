#  Hadoop下开源数据挖掘库Mahout
本文借鉴：1.https://blog.csdn.net/aaronhadoop/article/details/24577221


## 总结

### （一）背景
看Hadoop生态圈的时候，看到一个有意思的数据挖掘库。   
***

### （二）Mahout简介
1. Mahout 是 Apache 官方旗下的一个开源项目，提供一些可扩展的机器学习领域经典算法的实现，旨在帮助开发人员更加方便快捷地创建智能应用程序。Mahout包含许多实现，包括聚类、分类、推荐过滤、频繁子项挖掘。此外，通过使用 Apache Hadoop 库，Mahout 可以有效地扩展到云中。

2. Mahout 是一个很强大的数据挖掘工具，是一个分布式机器学习算法的集合，Mahout最大的优点就是基于hadoop实现，把很多以前运行于单机上的算法，转化为了MapReduce模式，这样大大提升了算法可处理的数据量和处理性能。
   
+ 彩蛋，Mahout是一个骑在黄色大象上的蓝色“驭象人”，而hadoop的标志就是一只小黄象，这层比喻可以看出apache一开始组成这个开源项目的成员，对mahout的定位。
***

### （三）Mahout包含的算法
| 算法类 | 算法名 | 中文名 |
|-------|-------|-------|
| 分类算法 | Logistic Regression <br> Bayesian <br> SVM <br> Perceptron <br> Neural Network <br> Random Forests <br> Restricted Boltzmann Machines| 逻辑回归 <br> 贝叶斯 <br> 支持向量机 <br> 感知器算法 <br> 神经网络 <br> 随机森林 <br> 有限波尔兹曼机|
| 聚类算法 | Canopy Clustering <br> K-means Clustering <br> Fuzzy K-means <br> Expectation Maximization <br> Mean Shift Clustering <br>  Hierarchical Clustering <br> Dirichlet Process Clustering <br> Latent Dirichlet Allocation <br> Spectral Clustering| Canopy聚类 <br> K均值算法 <br>  模糊K均值 <br>  EM聚类（期望最大化聚类） <br> 均值漂移聚类 <br> 层次聚类 <br> 狄里克雷过程聚类 <br> LDA聚类 <br> 谱聚类|
| 关联规则挖掘 | Parallel FP Growth Algorithm | 并行FP Growth算法 |
| 回归算法 | Locally Weighted Linear Regression| 局部加权线性回归 |
| 降维/维约简 | Singular Value Decomposition <br>  Principal Components Analysis <br> Independent Component Analysis <br> Gaussian Discriminative Analysis | 奇异值分解 <br> 主成分分析 <br> 独立成分分析 <br> 高斯判别分析 |
| 进化算法 | 并行化了Watchmaker框架 |   |
| 推荐/协同过滤 | Non-distributed recommenders <br> Distributed Recommenders | Taste(UserCF, ItemCF, SlopeOne） <br> ItemCF |
| 向量相似度计算 | RowSimilarityJob <br> VectorDistanceJob | 计算列间相似度 <br> 计算向量间距离 |
| 非Map-Reduce算法 | Hidden Markov Models | 隐马尔科夫模型 |
| 集合方法扩展 | Collections | 扩展了java的Collections类 |
***
