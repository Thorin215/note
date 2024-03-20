---
counter: True  
---

# chapter 4 Leftist Heap & Skew Heap

## Leftist Heaps

!!! tip "definition"
    - Target : Speed up merging in $O(N)$.
    - Heap:Structure Property + Order Property
    - The null path length, $Npl(X)$(到外结点的路径长度), of any node X is the length of the **shortest path** from $X$ to a node without two children.  Define $Npl(NULL) = –1$.
    - The leftist heap property is that for every node $X$ in the heap, the null path length of the left child is **at least as large as that of the right child**.
    
    !!! Note "NPL"
        $\textbf{Npl}(X)$ = $\min$ { $\textbf{Npl}(C) + 1  \quad \text{for all C as children of X}$ }
        

- Order Property – the same
- Structure Property – binary tree, but ***unbalanced***

性质: 
- A leftist tree with $r$ nodes on the right path must have at least $2^r – 1$ nodes(左倾堆的NPL等于右路径)
- 右路径长度的渐进上界是$O(\log N)$

### Declaration

```cpp
struct TreeNode 
{ 
    ElementType	    Element;
    PriorityQueue	    Left;
    PriorityQueue	    Right;
    int		    Npl;
};
```

### merge

## Skew Heaps

!!! tip "definition"
    - Target : Any M consecutive operations take at most $O(M log N)$ time
    - 

