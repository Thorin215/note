# Fractal Generative Models

## 多叉树 Transformer

我们先以一维数据为例，学习这篇论文是怎么加速 Transformer 的。在正式学习方法之前，我们再次复习一遍为什么 Tranformer 的运算是平方复杂度的。

忽略 Transformer 中注意力运算的实现细节，我们仅关心每个运算的输入输出是什么。在 Transformer 中，由于自注意力运算是一种全局运算，每个元素的输出都取决于其他所有元素。从信息的交换次数来看，如果序列有 $N$ 个元素，每个元素都要至少访问 $N$ 个输入元素的信息，总计算的复杂度是 $O(N×N)=O(N^2)$。

在前文对树的知识回顾中，我们知道树的某个根节点可以维护整个子树内所有数据的统计信息。那么，我们可不可以用树来减少 Transformer 的信息交换次数呢？比如，我们要让 8 号元素查询 1-4 号元素的信息，我们不去逐个查询叶子节点，而是去查询它们构成的子树的根

![img](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/v2-b5d4bdfaf86d6b71b6078e79bd9c71c5_1440w.jpg)

![image-20250306164111361](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/image-20250306164111361.png)