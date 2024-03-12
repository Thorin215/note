---
counter: True  
---

# chapter 2 Red-Black Trees and B+ Trees

## Red-Black Trees

!!! Abstract "Definition of red-black tree"
    A red-black tree is a binary search tree that satisfies the following red-black properties:
    1. Every node is either red or black.
    2. The root is black.
    3. Every leaf (NIL) is black.
    4. If a node is red, then both its children are black.
    5. For each node, all simple paths from the node to descendant leaves contain the same number of black nodes.(黑高相同)

!!! warning 
    - 除了常规的父子以及键值信息之外，红黑树还有一个颜色的属性，并且当左右子结点为空时，必须是黑色的NIL结点（它没有键值），这与一般的NULL不一样。
    - 黑高是绝对严格的平衡要求，而红色结点则是少量不平衡的因素，并且定义控制了红色结点的个数，也就控制了不平衡因素的影响
  
### Insertion

??? question "为什么默认插入红色节点"
    假设我们每次都插入黑色结点，那么定义第五条一定会被破坏，因此每次插入都必须调整（除了从空树插入的那次）；但是如果插入红色结点，定义第二条（仅在插入空树时）和定义第四条仅仅是有可能被破坏，因此直观来看插入红色结点会比插入黑色结点需要的调整更少。除此之外，在学完红黑树删除后，红黑树删除黑色结点时的复杂情况也暗示了插入红色应当是更好的选择。

- 情况 1：$X$ 的叔叔（即父亲的兄弟）是红色的，$X$ 无论左右孩子都是该情况
 
  ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240309151416.png)

- 情况 2：$X$ 的叔叔（即父亲的兄弟）是黑色的，且 $X$ 是右孩子

- 情况 3：$X$ 的叔叔（即父亲的兄弟）是黑色的，且 $X$ 是左孩子
  
  ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240309151537.png)

!!! summary
    对于插入产生的冲突问题，核心的处理思想是将锅不断向上甩
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/f0d08129e015af3ada1c7be7ae3f38a.png)
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240309152747.png)

### Deletion

!!! Abstract "基础删除操作"
    - 如果$X$没有孩子，直接删除就好，没有任何后顾之忧；
    - 如果$X$只有一个孩子，那就让孩子接替$X$的位置；
    - 如果$X$有两个孩子，那就让$X$与其左子树的最大结点（或右子树最小结点）交换，然后删除 $X$（这时 $X$ 所在的位置一定只有一个子节点，因为左子树最大结点不可能有右孩子，右子树最小结点不可能有左孩子）

!!! Warning "Red-Black Tree删除特点"
    事实上红黑树的删除是基于这些操作的，需要注意的是第三种情况，**$X$ 和与其交换的结点只交换键值，不交换颜色**，否则如果两者颜色不同，在交换的时候就可能破坏第五条性质，这是很难令人满意的。总而言之，第三种情况可以通过一步交换直接转化为第一或第二种情况，因此我们只需要关心第一和第二种情况。

!!! Tip "双黑节点"
    如果接替被删除的是NIL或是黑色结点应该怎么办？我们直接给黑色结点或者 NIL（其实也是黑色结点）再加一重黑色，于是它的颜色变成了**双黑**。此时第五条性质没有被破坏，但是，第一条性质被破坏了！这里出现了非红也非黑的颜色！于是我们的想法就是：把这个黑色的 debuff 扔给一个红色结点，或者一步一步往上扔给根结点，**事实上根结点把双黑直接变成黑色完全不影响其它性质**。我们可以将删除的情况分成以下四类（与插入相同，这里用子树表示更一般的情况，$X$ 在此处则表示双黑结点，图中用黑色圆圈和圆旁边的加号表示双黑，蓝色表示颜色无所谓，可红可黑。注意这里 $X$ 都是父亲的左孩子，右孩子情况对称）

- 情况 1：$X$ 的兄弟是红色的

  ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240309213427.png)

由于原先的树满足红黑树定义第四条，因此此时父结点一定是黑色。这种情况我们将兄弟节点拎起来

- 情况 2：$X$ 的兄弟是黑色的，且兄弟的两个孩子（根据距离划分为近、远侄子，用远近而不用左右是为
了对称情况不混淆左右）都是黑色的

  ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240309213455.png)

这时候没人是红色可以救了，那就想起根结点再怎么样都能救，所以把双黑往上推给父亲，为了保证红黑树性质，兄弟也要从黑变红，总而言之就是 $X$ 这一层的黑色全部往上推。如果父亲原本是红色，那就染黑，问题解决；如果父亲原本是黑色，那父亲就变成双黑，让问题向根结点靠近。**需要特别注意的是，如果是从情况1变成情况2的，父亲一定是红色，所以如果是1变为2，则问题会马上解决**。


- 情况 3：$X$ 的兄弟是黑色的，且近侄子是红色的，远侄子是黑色的

  ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240309213513.png)

这时我们借用 AVL 树的想法，红色在父亲 P 的 RL 位置，因此做 double rotation：single rotation 后会变成情况4的RR的情况（也就意味着红色要给到RR的位置，这里有一个颜色的变化，用RR记忆很方便）。

- 情况 4：$X$ 的兄弟是黑色的，且远侄子是红色的，近侄子颜色任意
  
  ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240309213532.png)

此时对应AVL树的RR，于是再一次single rotation即可把双黑的一重黑丢给红色远侄子（即$X$和 $N_2$ 都变成黑色），但要注意为了保证红黑树性质的颜色变化，即P和S还要交换颜色，此时问题解决。

??? Note "时间复杂度"
    此时我们可以总结并计算出删除操作的时间复杂度。首先我们最多用$O(\log n)$的时间找到删除结点，最多 1 次交换和 1 个删除的操作。接下来如果删除后没有问题则到此结束；否则根据分析，情况 1、3 和 4 在问题解决前最多进去一次，因为 4 可以直接解决，3 直接进入 4 然后解决，1 如果进入 3 和 4 也可以马上解决，进入 2 后也因为父结点是红色可以马上解决。因此关键在于情况 2 可能出现很多次，但最多也只是树高$O(\log n)$次，因为每次都会上推 1 格。总而言之，因为情况 1、3 和 4 在问题解决前最多进去一次，所以最多 3 次旋转加上$O(\log n)$次颜色调整可以解决问题，**因此我们有如下结论：一棵有 $n$个内部结点的红黑树删除一个结点的时间复杂度为$O(\log n)$**


## B+ Tree

!!! Abstract "Definition"
    A B+ tree of order M is a tree with the following structural properties:
    - The root is either a leaf or has between 2 and M children.
    - All nonleaf nodes (except the root) have between $ \lceil M/2 \rceil$ and $M$ children.
    - All leaves are at the same depth.
    Assume each nonroot leaf also has between $ \lceil M/2 \rceil$ and $M$ children.
    
    ??? Example
        ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/cf3e34d29d8eb9b62b057f5ce0d46d5.png)

### B+ Tree的操作

#### search

$B+$树中的节点包含有多个键。假设需要查找的是$k$，那么从根节点开始，从上到下递归的遍历树。在每一层上，搜索的范围被减小到包含了搜索值的子树中。子树值的范围被它的父节点的键确定。因为是从根节点开始的二分法查找，所以查找一个键的代码如下：

!!! Bug "code"
    ```cpp
    BTreeNode *BTreeNode::search(int k) {
      // 找到第一个大于等于待查找键 k 的键
      int i = 0;
      while (i < n && k > keys[i]) i++;

      // 如果找到的第一个键等于 k , 返回节点指针
      if (keys[i] == k) return this;

      // 如果没有找到键 k 且当前节点为叶子节点则返回NULL
      if (leaf == true) return NULL;

      // 递归
      return C[i]->search(k);
    }
    ```

根据 B+ 树定义，需要在非叶结点层逐层和存储的键值比较从而确定去哪一个孩子结点。因此时间复杂度有两个重要因素：一个是树的高度，另一个是每一层搜索需要的时间。树的高度非常好计算，最差的情况也是每个结点都存 $⌈M/2⌉$ 个结点，因此最大高度是$O(\log_{\lceil M/2 \rceil} N)$的。然后每一层因为键值是排好序的，因此用二分查找找到要去哪个孩子结点，复杂度为 O(\log_2 M)，综合可得搜索的时间复杂度为$O(\log_2 M · log_{⌈M/2⌉} N) = (\log_2 M/2 + 1) ·\frac{\log_2 N}{\log_2 M/2} = O(\log N)$，注意推导中使用了换底公式.

#### Insert

PPT上的伪代码已经十分清楚，就是找到插入的位置，然后插入看结点是否放得下，放不下就分裂，如果分裂后子结点个数也过多则继续向上一层分裂，直到根结点孩子爆满则将根结点分裂并生成新的根结点，当然还要注意即使不分裂也可能需要按$B+$树定义**更新上层结点**。我们知道树有$O(log_{⌈M/2⌉} N)$层，每层操作最多是$O(M)$的（如更新结点或者分裂，无非就是更改$O(M)$个键值以及修改$O(M)$个父子指针），因此整体时间复杂度为$O(M · \log_{\lceil M/2 \rceil} N) = O(\frac{M}{\log M} \log N)$。

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/337ca66414f25cffd161811a799ada7.png)

??? Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/351e1ed2c808f6b4264800a67d2c3cf.png)
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/241d327316c2b4131d1437cfdfdd7b0.png)

#### Delete

PPT 没有要求，但想法很简单，因为只需把插入时分裂结点改为合并键值或孩子数量少的结点，当然需要注意的是，为了确保合并后键值数量不会超过 M 且减少合并次数，可以先看看兄弟结点是不是键值还很多，多的话拿一个过来即可，事实上整体时间复杂度和插入分析类似，也为 $O(\frac{M}{\log M} \log N)$。

### B+ Tree的意义

通过降低树的高度，极大减少了数据库查询等需要的磁盘操作次数。

!!! Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240312145738.png)