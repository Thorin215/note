# Lecture 4 Bayesian Data Analysis

## Difference Between Classical Data Analysis and Bayesian

<img src="https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/ee7703a0020160e7d3d3aa14afe0b21.png" />

- Classical: Say we have a model with mass of electron as parameter. We might not know the value, but it is nonetheless a constant.（假设我们有一个以电子质量为参数的模型。我们可能不知道它的值，**但它仍然是一个常数**。）
- Bayesian: If we do not know its value completely, use a prior distribution reflecting what we do know.（如果我们不完全知道它的值，使用反映我们所知道的先验分布。）
- Classical: Prior Distribution seems arbitrary.（先验分布似乎是任意的）
- Bayesian: Every statistical method makes some choice might as well use a prior to codify these choices.(每一种统计方法都有一些选择，在整理这些选择之前不妨使用先验。)
- Classical: Bayesian methods too difficult to compute (practical considerations)
经典:贝叶斯方法太难计算(实际考虑)

!!! Example "先验事件与后验事件"
    - 先验事件就是由因求果，先验概率也就是根据以往经验和分析得到的概率，最典型的代表就是抛硬币，抛一个硬币求其正面的概率，就是已经知道**硬币正反面概率都是0.5**的条件，求出“硬币是正面”的“结果”的概率。
    - 后验事件则是由果求因，也就是依据得到"结果"信息所计算出的最有可能是哪种事件引起的，例子就是：中午我拉了肚子，那么我早上喝了一杯凉水的概率是多大？换言之，“拉肚子”是结果，我在已经知道结果的前提下，求“喝凉水”的原因的概率，也就是：$P({喝凉水}|{拉肚子})$, 后验概率而先验与后验的基础都是条件概率。

!!! summary 
    - 信息利用不同
        - 经典统计根据样本信息对总体分布或者总体特征分布进行统计推断，只需要用两种信息：总体分布信息以及样本信息
        - 贝叶斯统计除了利用这两种信息外还需要用到先验分布信息，特别重视先验信息的收集、挖掘、加工，使他数量化，提高统计推断的质量，利用三种信息可以得到：

        $$\pi(\theta|x) = \frac{h(x,\theta)}{m(x)} = \frac{p(x|\theta)\pi(\theta)}{\sum p(x|\theta) \pi(\theta)} $$
    
    - 对主观概率的认同不同
        - 经典统计的概率是大量重复实验中获得的
        - 贝叶斯概率是根据自己的生活活动积累，对某件事情发生的可能性给出的信息，贝叶斯允许利用主观概率。
    - 对参数$θ$的认识不同
        - 经典统计把$θ$看成一个常数，对于某种现象进行统计推断贝叶斯统计把参数θ看成一个随机变量来进行统计推断，用一个概率分布来描述θ的未知状况，这个概率分布在抽样前就有关于θ的先验信息的概率陈述。
    - 对样本的认识不同
        - 经典统计学把样本看做是总体分布的信息，研究的是总体，不局限于数据本身。贝叶斯是重视样本观测值，通过样本观测值设出先验分布，得到后验分布。
    - 对于可信区间和置信区间的认识不同
          - 经典统计把真值看作是常量，置信水平为n次使用这个区间时，大概有多少可以盖住θ，置信区间比较难求，需要引入含被估参数的随机变量，使其不含未知参数。
          - 贝叶斯统计可信区间将真值看成变量，可信水平$θ$落入可信区间内的概率例如
    
        !!! example
            ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/372b3bd6c5101f950aaa88bc41ea50f.png)
            ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/b1d4909f602d3b9c3d31365bae7a3c8.png)
    
    - 假设检验认同不同
        - 经典统计假设检验需要建立原假设$H_0$，备择假设$H_1$，选择检验统计量，显著性水平，并确定拒绝域。
        - 贝叶斯统计根据先验分布，得到后验分布之后进行假设检验$H_0$，$H_1$，不需要统计量来假设抽样分布，也不需要显著性水平来假设拒绝域，但是需要考虑损失函数。
    - 三种信息的图形不同
        - 经典统计只有一个样本图型
        - 贝叶斯统计的后验分布是利用样本信息对先验信息修正的结果，后验概率密度图形在样本密度图形与先验密度图形之间。

## Bayesian Learning

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/63e93ae7b823b632e1112c41c52ca6a.png)

$$P({\theta}|{D}) = \frac{P({D}|{\theta})P(\theta)}{P(D)}$$

- $P(\theta)$ : 先验概率（Prior Probability） 就是还没有训练数据之前，某个假设$h(h∈H)$的初始概率，记为$P(\theta)$。
- $P({\theta}|{D})$ : 后验概率（Posterior Probability） 就是在数据d上经过学习之后，获得的假设h成立的概率，记为$P(\theta|D)$。 $P(\theta|D)$表示给定数据$D$时假设$\theta$成立的概率，称为h的后验概率。(后验概率是学习的结果，反映了在看到训练数据d之后，假设h成立的置信度。后验概率用作解决问题时的依据。 对于给定数据根据该概率做出相应决策，例如判断数据的类别，或得出某种结论，或执行某种行动等等。)
- $P({D}|{\theta})$ : 似然度，表示已知假设$\theta$成立时$D$的概率，称之为类条件概率，或者给定假设$\theta$时数据$D$的似然度

!!! summary 
    - $P(\theta|D)$随着$P(\theta)$和$P(D|\theta)$的增长而增长，随着$P(D)$的增长而减少。 即如果$D$独立于$\theta$时被观察到的可能性越大，那么$D$对$\theta$的支持度越小。 后验概率是对先验概率的修正。后验概率$P(\theta|D)$是在数据$D$上得到的学习结果，反映了数据$D$的影响，这个学习结果是与训练数据相关的。 与此相反，先验概率是与训练数据$D$无关的，是独立于$D$的
    - 贝叶斯法则解决的机器学习任务一般是： 在给定训练数据$D$时，确定假设空间$H$中的最优假设

### Bayesian Rule

<div align  = center>

<img src="https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/83a0635e958cd341c18926d2a3e23e1.png" /> </div>

<div align = center><img src="https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/1711764093923.jpg" /></div>

### Output of Bayesian Inference

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/38a2139ecf14e40bba10e921672d68c.png)

AP统计可以分为描述统计(Descriptive statistics)和统计推断(Inferential statistics)两大部分。而参数估计(Parameter estimation)和假设检验(Hypothesis test)是统计推断的两个重要内容。其中参数估计又包括点估计(Point estimation)和区间估计(Interval estimation)两块内容。

### Point Estimation(点估计)

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/79387327b8ace4999b8aad72ac43957.png)

- We get it by applying some function $g$ to the observation $x$,$\hat{\Theta} = g(x)$

$$\hat{\Theta}(X) = g(X)$$

### Point Estimators

- MAP Estimator : $\hat{\Theta}(X) = arg \max\limits_{\Theta}p_{\Theta}(\Theta|X)$
- Conditional Expectation : $\hat{\Theta}(X) = \mathbb{E}(\Theta|X)$

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/2bdfe6e9267e71f0bc17a99af2ed57c.png)

!!! tip "MAP Rule"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/6828650a794d3f7ddb714e6eb8e8d5f.png)

    - since the denominator depends only on $x$, and is the same for all $\theta$.

## MAP(最大后验估计) and MLE(最大似然估计)

~~一般来说,频率学派更喜欢MLE,贝叶斯学派更喜欢MAP~~

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/9367de652dbf49adeb027d621e9d09f.png)

!!! note "compare"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/ea0f2d6f49760a1bdc776713ad10105.png)
    - MLE当数据较少的时候，参数估计的偏差很大。
    - MAP的结果会随着先验概率而变化

??? warning "$P(x;\theta)$ 和 $P(x|\theta)$的区别"
    - 在贝叶斯统计的角度下应该一样的。这时$x$和$\theta$可以看做服从某个联合分布，而两个符号都表示$\theta$取某个值时，$x$的条件分布。
    - 在频率学派角度下不同。前者$\theta$表示待估计参数，后者表示随机变量，不一样。

## LMS Estimation

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/f9d07ee9cebcd9b323962b1582f14eb.png)

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/b46d1980c1a03cbc5235f614c629576.png)

!!! tip "莫名联想到了最大类间方差的大津算法"

- $\hat{y}(n) = w^T(n) x(n)$ 预期
- $e(n) = y(n) - \hat{y}(n)$ 求平方误差
- $w(n+1) = w(n) + \mu \cdot e(n) \cdot x(n)$ 修正
