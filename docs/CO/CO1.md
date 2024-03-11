---
counter: True  
---
# chapter 1 Computer Abstractions and Technology

!!! Abstract
    - 8 Ideas in Computer Architecture
    - Performance
    - Incredible performance improvement

## 8 Ideas in Computer Architecture

* Moore's Law  
The integrate circuit resource double every 18-24 months.
* User abstraction to simplify design(使用抽象简化设计)
    * Lower-level details are hidden to higher levels
    * Instruction set architecture -- the interface between HW and SW.
* Make the common cases fast(加速经常性事件)
* Performance via Parallelism(通过并行提高性能)
* Performance via Pipelining (通过流水线提高性能)
* Performance via Prediction(通过预测提高性能)
* Hierarchy of memory(存储层次)
* Dependability via redundancy(通过冗余提高可靠性)

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/079c20007630b5c85bb09e16816b663.png)


## Performance(性能)

* **Response time（响应时间）**: How long it takes to do a task. (个体用户在意) 
* **Throughput (吞吐量)**: Total work done per unit time.  （服务器重视)

Define $Performance = \dfrac{1}{Execution\ Time}$  

### Execution time(执行时间)

* Elapsed Time (和响应时间一回事)
Total response time, including all aspects ***e.g.*** Processing, I/O, OS overhead, idle time.  

* CPU Time (CPU 执行时间)

    * 用户CPU时间(user CPU time)
    * 系统CPU时间(system CPU time)
    * 为了保持一致，我们保持区分基于时间的性能和基于CPU执行时间的性能，前者叫做系统性能(system performance),后者叫做CPU性能(CPU performance).
    * Discounts I/O time, other jobs’ shares  
* 这里我们只考虑CPU时间

### CPU Clocking(CPU 时钟)

* Clock period: duration of a clock cycle.(周期长度)  
用时钟周期代替具体的秒数。 
* Clock frequency(rate): cycles per second. (周期频率)

!!! tip "CPI"
    - 指令平均时钟周期数(clock cycle per instruction)表示执行每条指令所需的时钟周期平均数，缩写为CPI。
    - CPI is determined by CPU hardware.  
    - 如果不同指令有不同的 CPI, 我们可以用 Average CPI. 

$$
\begin{align*} 
CPU\ Time &= CPU\ Clock\ Cycles \times Clock\ Cycle\ Time \\
&=\dfrac{ CPU\ Clock\ Cycles}{Clock\ Rates}
\end{align*}
$$ 

Performance improved by
* Reducing number of clock cycles
* Increasing clock rate
* Hardware designer must often trade off clock rate against cycle count

$$
\begin{align*}
Clock\ Cycles &= Instruction\ Count \times Cycles\ per\ Instruction(CPI)\\
CPU\ Time & = Instruction\ Count \times CPI\times CPI\ Cycle\ Time\\
& = \dfrac{Instruction\ Count \times CPI}{Clock\ Rate}
\end{align*}
$$

综上, $CPU\ Time = \dfrac{Instructions}{Program}\times \dfrac{Clock\ Cycles}{Instruction}\times \dfrac{Seconds}{Clock Cycle}$ 

Performance depends on  

* Algorithm: affects IC, possibly CPI
* Programming language: affects IC, CPI
* Compiler: affects IC, CPI
* Instruction set architecture ：IC，CPI, 时钟频率

## Incredible performance improvement

### Uniprocessor

**Three Walls**

* **Power Wall**
$Power = Capactive\ load \times Voltage^2 \times Frequency$
![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240308212433.png)

??? Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/8fd5a32025ecebacd43a54f9599bfe5.png)

主频提高了很多，但功耗并没有得到这么多的提升，因为我们降低了工作电压 (5V-1V)
现在工作电压不能再降低了（否则泄漏电流占比太大），因此我们不能再提高功率了。

* **Memory Wall**  
Memory 的性能增长不如 CPU 的性能增长，大部分时间花在读写内存了，影响整体性能。目前使用多级Cache
* **ITP Wall**  
difficulty to find enough parallelism in the instructions stream of a single process to keep higher performance processor cores busy.
指令集并行程度

### Multiprocessors  

requires explicitly **parallel programming**.  

!!! Tip "Amdahl's Law"
    Improve an aspect of a computer and expecting improvement in overall performance. 
    $T_{\textbf{improved}}=\dfrac{T_{\textbf{affected}}}{\textbf{improvement factor}}+T_{\textbf{unaffected}}$. 

    ??? Example
        ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/cceeb8fcb56332444ff3c9692bf4c43.png)

Corollary: make the common case fast.  

- Low Power Not at Idle.机器在没有工作时也有功耗损失。
- MIPS as a Performance Metric

    - MIPS: Millions of Instructions Per Second  
    - 这个参数需要在其他参数一致时，才有比较意义。不同的 ISA 之间不能仅凭 MIPS 比较。

!!! Note "SPEC Power Benchmark"
    - Power consumption of server at different workload levels
        - Performance: ssj_ops/sec
        - Power: Watts (Joules/sec)
    - Overall ssj_ops per Watt = $\frac{\sum\limits_{i=0}^{10}{ssj\_ops}_i}{\sum\limits_{i=0}^{10}{power}_i}$