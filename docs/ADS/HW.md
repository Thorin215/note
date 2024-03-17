# Problem

## Week1

!!! question 
    If the depth of an AVL tree is 6 (the depth of an empty tree is defined to be -1), then the minimum possible number of nodes in this tree is:

    !!! success "Answer"
        - 33
        - $n_h = F_{h+3} - 1, for \quad h \leq -1$
        - 斐波那契公式不一定要记忆，~~数值不大可以现推~~

!!! Question
    Insert 2, 1, 4, 5, 9, 3, 6, 7 into an initially empty AVL tree. Which one of the following statements is FALSE?
    A.4 is the root
    B.3 and 7 are siblings
    C.2 and 6 are siblings
    D.9 is the parent of 7

    !!! success "Answer"
        - B

!!! Question
    When doing amortized analysis, which one of the following statements is FALSE?
    A.Aggregate analysis shows that for all n, a sequence of n operations takes worst-case time $T(n)$ in total. Then the amortized cost per operation is therefore $T(n)/n$
    B.For potential method, a good potential function should always assume its maximum at the start of the sequence
    C.For accounting method, when an operation's amortized cost exceeds its actual cost, we save the difference as credit to pay for later operations whose amortized cost is less than their actual cost
    D.The difference between aggregate analysis and accounting method is that the later one assumes that the amortized costs of the operations may differ from each other

    !!! success "Answer"
        - B : 应该是minimum，初始势能最低

!!! Question
    Consider the following buffer management problem. Initially the buffer size (the number of blocks) is one. Each block can accommodate exactly one item. As soon as a new item arrives, check if there is an available block. If yes, put the item into the block, induced a cost of one. Otherwise, the buffer size is doubled, and then the item is able to put into. Moreover, the old items have to be moved into the new buffer so it costs $k+1$ to make this insertion, where $k$ is the number of old items. Clearly, if there are $N$ items, the worst-case cost for one insertion can be $Ω(N)$. To show that the average cost is $O(1)$, let us turn to the amortized analysis. To simplify the problem, assume that the buffer is full after all the $N$ items are placed. Which of the following potential functions works?

    A.The number of items currently in the buffer
    B.The opposite number of items currently in the buffer
    C.The number of available blocks currently in the buffer
    D.The opposite number of available blocks in the buffer

    !!! success "Answer"
        D 


!!! Question 
    For the result of accessing the keys 3, 9, 1, 5 in order in the splay tree in the following figure, which one of the following statements is FALSE?
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240317203802.png)
    A.5 is the root
    B.1 and 9 are siblings
    C.6 and 10 are siblings
    D.3 is the parent of 4

    !!! success "Answer"
        - D
