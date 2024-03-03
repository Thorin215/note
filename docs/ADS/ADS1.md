---
counter: True  
---

# chapter 1 AVL Tree、Splay Tree and Amortized Analysis

## AVL Tree(speed up searching)

!!! Info
    An empty binary tree is height balanced.  If $T$ is a nonempty binary tree with $T_L$ and $T_R$ as its left and right subtrees, then $T$ is height balanced iff
        (1)  $T_L$ and $T_R$ are height balanced, and
        (2)  $| h_L - h_R | \leq 1$ where  $h_L$ and $h_R$ are the heights of $T_L$ and $T_R$ , respectively.

这里定义Balance Factor(BF) = $h_L - h_R$来判断树的平衡性，在一个AVL Tree中，$BF = -1, 0, 1$

!!! Warning
    - 对于空节点，其BF默认为-1
    - 对于单节点，其BF默认为0

## Tree Rotation

!!! Warning
    通过Rotation处理非平衡的情况依照从叶子节点向上，先解决最低的

### Right(or Left)Rotation

- Tree rotation is an operation on a binary tree that changes the structure without interfering with the order of the elements.

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/bbfed516ca88dbe296476b0170abd80.png)

- After a rotation, the side of the rotation increases its height by 1 whilst the side opposite the rotation decreases its height similarly.

- Time complexity: $O(1)$

!!! Example
     The trouble maker Nov is in the right subtree’s right subtree of the trouble finder Mar.  Hence it is called an RR rotation.
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/86ede1739494d4f5745bd66256c9713.png)

### Double Rotation

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/287fb411686eeda5b2f6495c1723ae22.png)

## Minimum Number of Nodes

Let $n_h$ be the minimum number of nodes in a height balanced tree of height $h$. Then the tree must look like

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/a433dada0a102e1c933026d1e57c2f4c.png)

$F_0 = 0,  F_1 = 1,  F_i = F_{i-1} + F_{i-2}$  $for$  $i > 1$

$n_h = F_{h+3} - 1, for \quad h \leq -1$

## Splay Tree

!!! Abstract
    - Target: Any $M$ consecutive tree operations starting from an empty tree take at most $O(M log N)$ time.
    - Idea: After a node is accessed, it is pushed to the root by a series of AVL tree rotations

为了避免出现冗余的循环后回到原点，对Splay Tree的处理采用zig-zig和zig-zag两种方式。

??? Tip
    Splay 树的想法一方面来源于希望可以不像 AVL 那样保持严格的平衡约束，但也能保证某种层面（均摊）的对数时间复杂度，另一方面 Splay 树在访问（特别注意访问包括搜索、插入和删除）时都需要将元素移动到根结点，这非常符合程序局部性的要求，即刚刚访问的数据很有可能再次被访问，因此在实现缓存和垃圾收集算法中有一定的应用。

- 搜索：使用普通二叉搜索树的方法找到结点，然后通过 splay 操作经过一系列旋转将搜索的结点移动到根结点的位置
- 插入：使用普通二叉搜索树的方法找到要插入的位置进行插入，然后把刚刚插入的结点通过splay操作经过一系列旋转移动到根结点的位置
- 删除：使用普通二叉搜索树的方法找到要删除的结点，然后通过 splay 操作经过一系列旋转将要删除的结点移动到根结点的位置，然后删除根结点（现在根结点就是要删除的点），然后和普通二叉搜索树的删除一样进行合理的merge即可。

!!! Warning "Zig-zig"
    zig-zig虽然写作single rotation，但是其实际上旋转了两次，先是G与P交换位置(G变为P的右子树)，再交换P与X的关系(P变为X的右子树)
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/bed4a537e5c7944ab15fc34bf405990.png)

## Amortized Analysis(摊还分析)

??? Abstract
    摊还分析的想法来源于我们希望估计一种数据结构经过一系列操作的平均花费时间。然而，平均时间非常难计算，因为每一步都有非常多的选择，连续m个操作，可能的操作路径是指数级别的。并且有时候平均涉及概率分布等，但我们并不知道确切的分布，因此比较难以计算。一种最简单的估计方法就是用最差情况分析作为平均情况的上界，例如Splay树，每个操作最差都是$O(n)$（$n$为树中结点个数）的，因此平均不会比最差情况差，所以也是$O(n)$的。然而这样的估计显然是放得太宽了，我们对这个复杂度是非常不满意的，因此我们需要进行摊还分析。事实上，在最差情况的分析中，我们忽略了一件事情，就是有的序列是不可能出现的，例如直接在空的树上用$ O(n)$时间删除，摊还分析则是希望排除掉最差情况分析中把所有不管可能不可能的情况，最差的路径挑出来的这种无脑行为，转而分析所有可能的从空结构开始的操作路径中，最差的平均时间，那么这一时间一定比最差情况分析好，因为排除掉了一些不可能出现的所谓最差序列，但又会大于等于平均时间，因为取的是所有可能序列中最差的那一种。因此当我们算出摊还分析的时间复杂度，那么它也一定是平均时间的上界，同时这个上界会比最差情况分析好，这也是不等式worst-case bound  $ \geq $ amortized bound $ \geq $ average-case bound的内在含义。


!!! Danger ""
    接下来我们介绍三种方法：聚合分析、核算法以及势能法。聚合分析直接使用了我们上面提到的思想：“摊还分析是考虑可能出现的操作序列中的最差序列”（再次强调，这里的最差不是最差情况分析的最差，最差情况分析的最差会包括不可能出现的序列，但摊还分析要排除不可能的序列）

### Aggregate analysis(聚合分析)

??? Example
    思考这一支持 MultiPop 操作的栈，在从空栈开始的连续 n 次操作中，最差的情况是什么：实际上就是先 Push n−1个然后最后一次操作一次性 Multipop 出所有元素。为什么这样是最差的呢？事实上简单想想就能理解，无法理解或者希望严谨一些可以看下面的解释：因为 n 次操作是固定的，所以我们的目标是，固定 n 的情况使得下总的代价最大，普通的 Push 和 Pop 都是 1 次操作对应1 个单位代价，所以我们必须寄希望于序列中 MultiPop 代价最大，那代价最大的情况就是只 MultiPop一次，且就在最后一次，因为如果只 MultiPop 一次，不在最后一次，显然这次 MultiPop 代价比 n − 1小；如果 MultiPop 多次，那么 Push 操作的个数少了，所以 MultiPop 能弹出的比 n − 1 少，所以代价也少。
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240303135623.png)

### Accounting method(核算法)

核算法就是传统意义上的取长补短

$$\sum\limits_{i=1}^{n}\hat{c_i} \geq \sum\limits_{i=1}^{n}{c_i}$$

$\hat{c_i}$是预测的期望(__摊还代价__)，$c_i$是实际的开销，他们满足$\hat{c_i} = c_i + \Delta_i$，$\Delta_i$是截取的长或者是补足的短。(可正可负)

!!! Bug "一个悲伤的例子"
    当然你也可以说，你希望你的平均绩点保持在4.0以上，你知道ADS这门课你可能会挂科，所以你之前的微积分、线性代数、FDS等就需要为这次挂科存下足够高的均绩。

### Potential method(势能法)

!!! Tip "势函数"
    比如思考 Splay 树这种复杂的结构，所以我们希望有一个更统一的方法解决这个问题：我们不再把目光局限于每个操作，而是给整个结构定义一个势函数，这个势函数描述了这个结构不同状态，其实就是借用了物理里面的势能的思想，比如重力势能是高度的函数，但树的势能可能是树高的函数，也可能是树的结点总数的函数甚至更复杂等等。
    
$$
\sum\limits_{i=1}^{n}\hat{c_i} = \sum\limits_{i=1}^{n}({c_i}+\Phi(D_i)-\Phi(D_{i-1})) = \sum\limits_{i=1}^{n}{c_i} + \Phi(D_n) - \Phi(D_0) $$

!!! Attention
    注意这里的$i$不再是核算法中表示第$i$种操作，而是$n$个操作组成的序列中的第$i$个！因此这里的含义是，每一步的摊还代价等于真实操作的代价加上势函数的变化

??? seealso "如何设计势函数"
    为了使得摊还成本是平均成本的上界，我们仍然需要满足$\sum\limits_{i=1}^{n}\hat{c_i} \geq \sum\limits_{i=1}^{n}{c_i}$因此只需要满足$\Phi(D_n) \leq \Phi(D_0)$即可，这一点在设计函数的时候很容易满足，因为我们可以通过调整使得 $Φ(D_0) = 0$，即初始状态势能为0，其它任何状态的势能都不会小于0就可以了，这比考虑核算法中对任何可能的序列都成立更加简单直接，因此更多时候我们采用势能法进行分析。

### Splay树的摊还分析

!!! Attention "Rank of SubTree" 
    节点数目的对数，为什么不用Height,Height不能很好的反应数量，且不会严格增加

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240303151808.png)

$$
\sum\limits_{i=1}^{n}\hat{c_i} \leq 1 + 3(R_{k+1}(X)-R_{k}(X)) + \sum\limits_{i=1}^{k}3(R_i(X)-R_{i-1}(X)) = 1 + 3(R_{k+1}(X) - R_0(X)) = O(logN) $$