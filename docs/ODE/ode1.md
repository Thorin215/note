# 基本概念

!!! Abstract
    一般的，在一个方程或者方程组中，如果未知量是一个函数(组)，而且该方程中含有此未知函数的导数(组)，则称这种方程为**微分方程(组)**。
    - 如果在微分方程里，出现的未知函数是单个自变量的函数，我们称这一类微分方程为**常微分方程**
    - 如果未知函数有两个或以上的自变量，则称为**偏微分方程**

## 阶

在微分方程中出现的未知函数的导数的最高阶数，称为微分方程的**阶**

!!! Example
    - 一阶微分方程的一般形式为$F(x,y,\frac{dy}{dx}) = 0$
    - $F(x,y,\frac{\mathrm{d}y}{\mathrm{d}x},\cdots,\frac{\mathrm{d}^ny}{\mathrm{d}x^n})=0$为$n$阶方程
    - eg. $\frac{d^2y}{dx^2} = \frac{1}{a}\sqrt{1+(\frac{dy}{dx})^2}$为二阶微分方程

## 解

- 如果$y = \varphi(x)$满足$F(x,y,\frac{\mathrm{d}y}{\mathrm{d}x},\cdots,\frac{\mathrm{d}^ny}{\mathrm{d}x^n})=0$，则该函数称为ODE的**解**
- 隐式解：若$\Phi(x,y) = 0$决定的$y = \varphi(x)$,则称$\Phi(x,y) = 0$为**隐式解**
- 通解：如$y = \varphi(x,c_1,c_2,\cdots,c_n)$
- 方程的解或积分在$(x, y)$平面上的图形称为该方程的**积分曲线**
  
### 解的存在性

例：$\frac{dy}{dx} = f(x, y)$

- **初值条件**为：$y|_{x = x_0} = y_0$

设$f(x, y)$在区域$D: |x - x_0| \leq a, |y - y_0| \leq b$内连续，且有对于$y$的偏导数,则在区间$|x - x_0| < h \quad (h = min(a,\frac{b}{M}), M = \max\limits_{(x, y) \in D}|f(x, y)|)$内存在唯一的$y = \varphi(x)$

- 满足初始条件的解称为**特解**

### 通解

一般的，如果含有一个任意常数$c$的函数$y = \varphi(x, c)$满足$\frac{d \varphi(x, c)}{dx} \equiv f(x, \varphi(x, c))$,则称$\varphi(x, c)$为**通解**

