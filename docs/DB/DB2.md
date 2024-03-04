---
counter: True  
---

# Chapter 2 Relational Model

!!! Abstract
    - Structure of Relational Databases
    - Database Schema
    - Keys
    - Schema Diagrams
    - Relational Query Languages
    - The Relational Algebra
    

## Basic Structure

Formally, given sets $D_1, D_2, … D_n$ a relation $r$ is a subset of  $D_1 \times  D_2  \times … D_n$.Thus, a relation is a set of n-tuples $(a_1, a_2, …, a_n)$ where each $a_i \in D_i$

!!! Abstract 
    - relation(关系) : 表
    - tuple(元组) ：行
    - Attribute(属性) : 列 

## Relation Schema and Instance

A1, A2, …, An are **attributes**
R = (A1, A2, …, An ) is a **relation schema**
    
!!! Example
    instructor  = (ID,  name, dept_name, salary)

A relation instance r defined over schema $R$ is denoted by $r(R)$.
The current values a relation are specified by a table
An element $t$ of relation $r$ is called a tuple and is represented by a row in a table

### Attributes

- The set of allowed values for each **attribute** is called the **domain（域）** of the attribute
- Attribute values are (normally) required to be **atomic（原子的）**; that is, indivisible
- The special value **null（空值）** is a member of every domain
- The null value causes complications in the definition of many operations

## Key(码\键)

- Let $K \subseteq R$
- K is a **superkey(超健)** of R if values for K are sufficient to identify a unique tuple of each possible relation $r(R)$
   
!!! Example  
    `{ID}` and `{ID,name}` are both superkeys of instructor.

S- uperkey K is a **candidate key(候选健)** if K is minimal 

!!! Example
    `{ID}` is a candidate key for Instructor

- One of the candidate keys is selected to be the **primary key（主键）**.

**Foreign key（外键）** constraint  from attribute(s) A of relation $r_1$ to the primary  key  B  of  relation r2 states that on any database instance, the value of A for each tuple in $r_1$ must also be the value of B for some tuple in $r_2$. 

!!! Example "Foreign key Example"
    
    - foreign key $(A_{j1},A_{j2}, \ldots A_{jm})$ references $s$
    ```sql
    foreign key(dept_name)references department(dept_name)
    ```

??? Tip "Summary"
    - superkey(超健) ：可以确定一个元组的一个或多个属性的集合(可能包含无关紧要的)
    - candidate key(候选健) ：最小的超码，任意真子集都不是超码
    - primary key(主码)：区分元组的主要方式的候选码
    - Foreign key（外码）：表明引用与被引用关系


## Schema Diagram for University Database

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/bbe9065ec3b5d31278fda8e0084f589.png)

- 实体完整性（Entity Integrity）
- 参照完整性（Referential Integrity）
- 用户定义的完整性（User-defined Integrity）

## Relational Query Languages(关系查询语言)

- Procedural vs.non-procedural, or declarative
- “Pure” languages:
  - Relational algebra(关系代数)(对关系进行操作，结果仍是关系)
  - Tuple relational calculus（元组关系演算）
  - Domain relational calculus（域关系演算）
- The above 3 pure languages are equivalent in computing power
- We will concentrate on relational algebra
  - Not Turing-machine equivalent
  - Consists of 6 basic operations

!!! Abstract "Relational algebra(关系代数)"
    - select(选择): $\sigma$
    - project(投影): $\Pi$
    - union(并集): $\cup$
    - set difference(集差): $-$
    - Cartesian product(笛卡尔积): $x$
    - rename(更名): $\rho$

!!! Question
    孙老师上课提到了思考为什么没有**交集**

    ??? Tip "Answer"
        交集可以通过并集和集差来实现

### Select

- $\sigma_p(r)=\{t|t\in r\ and\ p(t)\}$  , where $p$ is called **selection predicate**.  

??? Example "Select Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/974cf289cbfd868acd9c049e5bd0161.png)

### Project

- The **project** operation is a unary operation that returns its argument relation, with certain attributes left out.(可以过滤特定的属性)    
- $\prod_{A_1,A_2,\ldots, A_k}(r)$ where $A_i$ are attribute names and $r$ is a relation name.   
- The result is defined as the relation of k columns obtained by erasing the columns that are not listed. 会对结果进行去重。  

??? Example "Projection Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240304204951.png)

### Union

* The **union** operation allows us to combine two relations.  $r\cup s = \{t| t\in r \ or \ t\in s\}$  
* $r$ and $s$ must have the same **arity (元数)** (same number f attributes)  
* The attribute domains must be **compatible（相容）**
* 当属性有关联类型时，对于每个输入 $i$, 两个输入关系的第 $i$ 个属性的类型必须相同。  

??? Example "Projection Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/3c0d1d695271df0aa254e9691514ba8.png) 

### Set Difference

- The **set-difference** operation allows us to find tuples that are in one relation but are not in another. (r里面有而s里面没有)$r-s=\{t|t\in r\ and\ t\notin s\}$  

- Set differences must be taken between **compatible** relations.

??? Example "Projection Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/18a62cbf481f40da92560d1add64c79.png)

### Cartesian-Product

- The **Cartesian-product** operation (denoted by $\times$)  allows us to combine information from any two relations. (两个表里的元组各自相乘) $r\times s  =\{t\ q|t\in r\ and\ q\in s\}$  

??? Example "Projection Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/1edc7cb036b9774fe11d9f70eb7dfa5.png)

!!! Warning "与自身做笛卡尔积的时候"
    当作两个不同的表处理

### Rename

Allows us to refer to a relation by more than one name.  $\rho_X(E)$

??? Example "Composition of Operations 1"
    Find the names of all instructors in the Physics department, along with the course_id of all courses they have taught.  
    
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/5e8103062a948c9b24e21d7abea4960.png)

    这两条语句含义一样，但第二条我们先进行了一次 select, 条目少了更高效.

!!! Tip 
    一般DBMS都能够自己进行优化

??? Example "Composition of Operations 2"
    Find the largest salary in the university.  

    * find instructor salaries that are less than some other instructor salary (**i.e.** not maximum)  
    using a copy of instructor under a new name $d$.   
    $\prod_{instructor.salary}(\sigma_{instructor.salary<d.salary}(instructor \times \rho_d(instructor)))$  
    * find the largest salary  
    $\prod_{instructor}-\prod_{instructor.salary}(\sigma_{instructor.salary}<d.salary(instructor\times \rho_d(instructor)))$  

    我们第一步将两个关系拼起来之后，限定 instructor 的工资小于 d, 随后投影我们就可以获得所有不是最大值的薪水。（因为任何不是最大值的薪水都会在笛卡尔积 select 后至少存在一个元组，这样投影之后仍会存在。但最大值就不会有元组存在），最后用全集减掉即可。

## Additional Operations

!!! Warning
    额外操作符并不能增强查询能力,只能简化表达

!!! Abstract
    - Set intersection(交集): $r \cap s$ 
    - Natural join(自然连接):  $r \Join s$
    - Semijoin(半连接): $\ltimes_\theta $
    - Assignment(赋值): $ \leftarrow $
    - Outer join(外连接): $r \ltimes s,r \rtimes s, r ⟗ s$
    - Division Operator(分部编号): $r \div s $

### Natural-Join Operation

Let r and s be relations on schemas R and S respectively.  Then,  $r\bowtie s$ is a relation on schema $R \cup S$ obtained as follows:

* Consider each pair of tuples $t_r$ from $r$ and $t_s$ from $s$.  
* If $t_r$ and $t_s$ have the same value on each of the attributes in $R \cap S$, add a tuple $t $ to the result, where
    * $t$ has the same value as $t_r$ on $r$
    * $t$ has the same value as $t_s$ on $s$

- 即共同属性要有相同的值，才能在拼接后的结果中保留。  
- 对乘法的扩展，相当于先 $\times$ 再 select, 最后 project.  
- 先笛卡尔积，完了进行选择，最后投影

??? Example "Natural Join Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/dc3a0a9619b4287814748a1739feb7e.png)

### Theta Join(连接)

- $r\bowtie_\theta s=\sigma_\theta (r\times s)$可以理解为条件连接，但是也可以理解为自然连接是用隐式谓词取代了$\theta$的连接

### Outer Join

**特点**：保留未匹配元组

Computes the join and then adds tuples form one relation that does not match tuples in the other relation to the result of the join.   

Uses *null values*:  

* **null** signifies that the value is unknown or does not exist 
* All comparisons involving null are (roughly speaking) false by definition

Outer join can be expressed using basic operations.  

* $r\rtimes s=(r\bowtie s)\cup (r-\cap_R(r\bowtie s)\times \{null,\ldots,null\})$
* $r\ltimes s=(r\bowtie s)\cup \{null,\ldots,null\}\times (s-\cap_R(r\bowtie s))$
* $r$⟗$s$ $=(r\bowtie s)\cup (r-\cap_R(r\bowtie  s))\times \{(null, \ldots)\}\cup\{(null,\ldots,null)\}\times (s-\cap_s(r\bowtie s))$

- Left outer join(左外连接)：只保留出现在**左外连接符**之前的关系中的元组
- Right outer join(右外连接)：只保留出现在**右外连接符**之后的关系中的元组
- Full outer join(全外连接)：保留两个关系中的元组

??? Example "Outer Join Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/4ffb74acdc37a7633e5bf400332eb2f.png)
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/a3ff26750e4d1e9d9b84e1c6cf3c50a.png)

### Semijoin(半连接)

$r\ltimes_\theta s$ 保留 $r$ 中能与 $s$ 相连的元组(在给定条件下)的集合(最大子集),相当于筛选。

$r\ltimes_\theta s = \sigma()$ 

??? Example "Semijoin Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/da067cc6aed327d13958933e5815c05.png)

### Assignment(赋值)

将一个关系代数表达式用一个临时变量来替代

### Division

- 查询包含另外一个表中所有信息的元组集合的共同元素
- Given relations $r(R)$ and $s(S)$, such that $S \subset R$, $r\div s$ is the largest relation $t(R-S)$ such that $t\times s \subsetneqq  r$  

- We can write $r\div s$ as  

$$
\begin{align*}
    temp1 & \leftarrow \Pi_{R-S}(r)\\
    temp2 & \leftarrow \Pi_{R-S}((temp1 \times s)- \Pi_{R-S,S}(r))\\
    result & = temp1 - temp2
\end{align*}
$$

??? Example "Division Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/5dd58d501e5510992bdd7f5a2598ad3.png)
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/a36a3bd308df96f94c1983d59fdceac.png)


## Extended Relational-Algebra-Operations

!!! Abstract
    - Generalized Projection
    - Aggregate Functions
    

### Generalized Projection

- 一般化投影generalized projection是投影projection的扩展，是带上了算术运算的
- $E$ is any relational-algebra expression
- Each of $F_1, F_2, …, F_n $are arithmetic expressions involving constants and attributes in the schema of $E$.
- Given relation instructor(ID, name, dept_name, salary) where salary is annual salary, get the same information but with monthly salary 

$$\Pi_{F_1,F_2 \ldots F_n}(E)$$

??? Example "Generalized Projection Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/98bda52da2e909b76479f007450b794.png)


### Aggregate Functions and Operations

* **Aggregation function**（聚合函数）takes a collection of values and returns a single value as a result.  
    * *avg*: average value
    * *min*: minimum value
    * *max*: maximum value
    * *sum*: sum of values
    * *count*: number of values

* **Aggregate operation** in relational algebra $G_1,G_2,\ldots,G_n \mathcal{G}_{F_1(A_1),\ldots F_n(A_n)}(E)$  

!!! Example "Aggregate Operation Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/f413714ed3fbf78e068fc3a5076b9f4.png)
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/81c946c7cfddf796058f6bffca34174.png)

分组结果没有名字，可以用 rename 或者 as 进行改名。  
***e.g.*** `dept_name G avg(salary) as avg_sal (instructor)`

# Multiset Relational Algebra

- 关系代数中，我们要求关系要是一个严格的集合。  
- 但实际数据库中并不是，而是一个多重集，允许有重复元素存在。  
- 因为一些操作的中间结果会带来重复元素，要保持集合特性开销很大，因此实际操作中不会去重 。 

## SQL and Relational Algebra

* `select A1, A2, ... An from r1, r2, ... rm where P` is equivalent to $\Pi_{A_1,\ldots, A_n}(\sigma_P(r_1\times r_2\ldots r_m))$ 
* `select A1, A2, sum(A3) from r1, r2, ... rm where P group by A1, A2` is equivalent to $A_1, A_2 \mathcal{G} sum(A_3)(\sigma_P(r_1\times r_2\times\ldots r_m))$  
这里按 $A_1,A_2$ 分组，那么结果的表中会有 $A_1,A_2,sum(A_3)$ 三列（分组依据+分组后的聚合结果），这里我们需要的就是这三列，所以分组即可。但是假设我们只需要 $A_1, sumA3$ 那么最后还需要投影。