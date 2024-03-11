---
counter: True  
---
# chapter 2 Arithmetic for Computer

!!! Quote
    本周计组是对着[HobbitQia的笔记本](https://note.hobbitqia.cc)和马德老师的智云自己补的XD

!!! Abstract
    - Introduction
    - Signed and Unsigned Numbers-Possible Representations
    - Arithmetic--Addition & subtraction and ALU
    - Multiplication
    - Division
    - Floating point numbers

!!! Warning
    本周计组涉及到了计逻中的ALU，CLA等概念

## Introduction

Instructions can be divided into 3 categories

* memory-reference instructions  
***e.g.*** `lw, sw`
需要 ALU 计算内存地址
* arithmetic-logical instructions  
***e.g.*** `add, sub, and, or, xor, slt`
需要 ALU 进行计算
* control flow instructions  
***e.g.*** `beq, bne, jal`
需要 ALU 进行条件判断

## Signed Number Formats

* Sign and magnitude
* 2's Complement
* 1's Complement
* Biased notation  

    !!! Example "Why we need biased notation"
        <div align=center> <img src="http://cdn.hobbitqia.cc/202303061541842.png" width = 65%/> </div>  
        
        上图是 32 位的二进制补码表示，我们可以看到左侧二进制表示，如果看作无符号数，那他们是从小到大排列的；但右侧对应的十进制整数确实分段单增的。  
        我们希望有一种这样的表示，能够让右侧的对应的值也单调递增。  
        一个想法是对右侧数加上 $2^{31}$, 相当于其二进制表示下最高位翻转。  

    $[X]_b = 2^n + X$  从二进制补码到移码，只需要翻转符号位即可。

## Overflow

**出现Overflow时候，记录当前指令地址，然后跳转专门处理异常指令的地方**

- The sum of two numbers can exceed any representation
- The difference of two numbers can exceed any representation
- 2's complement:Numbers change sign and size

!!! Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240310162235.png)    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240310162302.png)

  ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/d2169068888d3277c2c684e8f7c10e0.png)

### Reaction of Overflow

- An exception (interrupt) occurs
    - Control jumps to predefined address for exception
    - Interrupted address is saved for possible resumption
- Signaling to application (Ada, Fortran) 
- Ignore, don't always want to detect overflow
- note: `sltu, sltiu` for unsigned comparisons

### Overflow process

- Hardware detection in the ALU
    - Generation of an exception (interrupt)
- Save the instruction address (not PC) in special register SEPC
- Jump to specific routine in OS
    - Correct & return to program
    - Return to program with error code
    - Abort program

## Logical Operation

### Logical operations

- Logical shift operation
  - right (srl)
  - left (sll)  

### The machine instruction for the instruction 

- slli x11, x9, 3 

## Constructing an ALU

### ALU symbol & Control

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/53424bd429e8cb8ec09b4902b64108b.png)

### Carry skip adder(跳跃加法器)

- Accelerating the carry by skipping the interior blocks
- Optimal speed with no-equal distribution of block length

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/6baa770629da3b6e44927e8a9d2a05f.png)

## Arithmetic 

* Substraction  
* Overflow detection: $C_n \oplus C_{n-1}$

注: RISC-V 不支持 nor 指令。

### Multiplication

#### Unsigned multiplication

Multiplicand (被乘数) $\times$ Multiplier (乘数)  

* 如果乘数末位是 1, 加被乘数，否则加 0. 随后将被乘数左移 1 位。
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/eb04ab4473aad86aa45aed5ba895b85.png)  

    需要 128+128+64 bit 的寄存器，和一个 128 bit ALU.  

* 不移被乘数，而是移积 (product). 这样 ALU 只需要 64 位。
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/09e53e100f55a32bcd610e0acacea80.png) 

    ??? Example
        ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/6fd2d06758af371146d7f8dcb28f4af.png)

* 这里积最开始只保存在左半部分，右半部分为空。而乘数也要右移，这样我们可以把两个数拼到一起，同时右移。  
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/c7dbf6e52cb970936f2c571c9cc4fab.png)   

#### Signed multiplication

有符号相乘不能直接乘，可以先用符号位决定结果符号，再对绝对值进行乘法。 

!!! Tip "Booth's Algorithm"  
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/bdb3252de9820891a7ae70d1f0701b9.png)   
    思想：如果有一串$1$
    - 减掉乘数的第一个1
    - 后面的1的序列进行移位
    - 当上一步是最后一个1时加  
    最开始把积放在高位，被乘数放在低位。（数据保存方法同 2.1.1）默认 $bit_{-1}=0$

* Action
    * 10 - subtract multiplicand from left
    * 11 - nop
    * 01 - add multiplicand to left half
    * 00 - nop

    每个操作结束后都要移位，和 2.1.1 中类似

注意移位时不要改变符号位。

!!! Example 
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/ac7a50ccac3a85e79f533ebeba167d9.png)  

    被乘数 Multiplicand 是 0010,  乘数 Multiplier 是 1101.  
    最开始将积 0000 放在高四位, 1101 作为乘数放在低四位。
    最开始 10, 即执行减操作, $0000-0010=1110$. 答案依然放在高四位，随后右移，以此类推。  
    注意右移的时候是**算术右移**, $bit_{-1}$ 也可能会改变。

#### Faster Multiplication

32 位数乘 32 位数，相当于 32 个 32 位数相加。（并行加速）
![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/237c61b97bfceafe33b32fee5325aca.png) 

### Division

Dividend (被除数) $\div$ Divisor (除数)

* 将除数放到高位。从高位开始减，减完将除数右移。商也随之不断左移。如果减完之后是负数，需要还回去。
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/070d8430c4aa505094dad844690168c.png) 

??? Example "7÷2"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240310200821.png)

* 除数不动，被除数不停地往左移。减到最后一次，如果是小于 0 的，说明不用减了，剩下的就是余数，需要右移移回来。（即将左半部分右移一位)因为每次都是将除数和被除数最高位减，减了之后高位就没用了，可以移出去。
  
![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/531b52db7d4d124ce506b6b047deb27.png)

**实际上这里结果是 129 位，防止 carry 丢失**

??? Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/353e347dbdffa055959f288ec5425c5.png)
    这里最开始余数就是整个被除数。   
    类似乘法，这里的除数只和被除数的高位相减。如果减出来是负数，需要加回去。每次减完之后先左移，然后最右边的一位放商。**4.1 时其实我们已经结束了除法操作，此时的高位就是我们的余数，但是这最后一次的结果还没有放回到 Reminder 中，因此我们需要再往左移一位为商留出空间，放入后，再把高位余数往右移动以抵消影响。**

#### signed division

- 带符号的除法：要求余数和被除数符号相同。
  ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/9812e5172bbf2d36ac7e61b3af24d9f.png)
- 除零会产生溢出，由软件检测。


#### RISC-V Division

- Four instructions:
    - div, rem: signed divide, remainder
    - divu, remu: unsigned divide, remainder
- Overflow and division-by-zero don’t produce errors
    - Just return defined results
    - Faster for the common case of no error
