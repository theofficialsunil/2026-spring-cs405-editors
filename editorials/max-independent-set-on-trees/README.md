# Maximum Independent Set on Trees

## Introduction

The **Maximum Independent Set (MIS)** problem asks:

> What is the largest set of vertices we can choose so that no two chosen vertices share an edge?

In simpler words, we want to pick as many nodes as possible while ensuring that no selected nodes are neighbors.

---

## Applications

MIS appears in many real-world scenarios:

- Scheduling tasks where conflicting tasks cannot run together  
- Placing Wi-Fi routers or base stations to avoid interference  
- Network design and resource allocation  
- Map labeling and facility placement  
- Bioinformatics (protein interaction networks)

---

## Why is MIS Important?

MIS was one of the original 21 NP-complete problems identified by Karp.

The Maximum Independent Set problem is **NP-hard** on general graphs.

This means:

- No polynomial-time algorithm is known for all graphs  
- A polynomial-time solution would imply $P = NP$  
- It is closely related to Vertex Cover, Clique, and 3-SAT  

However, many NP-hard problems become efficient on restricted graph families.

**Trees are one of the most important such families.**

Because trees have no cycles, they allow elegant and efficient dynamic programming solutions.

---

## Problem Statement

**Input:**  
An undirected tree $T = (V, E)$ with $n$ vertices.

**Output:**  
The size of the largest independent set in $T$.

We root the tree at any arbitrary node (say node 1).  
The result does not depend on the chosen root.

---

# Key Insight

At every node, we have exactly **two choices**:

1. Include this node  
2. Exclude this node  

If we include a node → we cannot include its children.  
If we exclude a node → children are free to choose independently.

This naturally leads to a dynamic programming solution on trees.

---

# DP Formulation

For each node $u$, define:

- $dp[u][0]$ = maximum independent set size in subtree of $u$ **excluding $u$**
- $dp[u][1]$ = maximum independent set size in subtree of $u$ **including $u$**

---

## Recurrence Relations

### Include $u$

$$
dp[u][1] = 1 + \sum_{v \in children(u)} dp[v][0]
$$

### Exclude $u$

$$
dp[u][0] = \sum_{v \in children(u)} \max(dp[v][0], dp[v][1])
$$

---

## Base Case

For a leaf node $u$:

$$
dp[u][0] = 0
$$

$$
dp[u][1] = 1
$$

---

## Final Answer

$$
\max(dp[root][0], dp[root][1])
$$

---

# Pseudocode

```text
Global:
    adj[1..n]          // adjacency list
    dp[1..n][0..1]     // dp[u][0] = exclude u
                       // dp[u][1] = include u

procedure DFS(u, parent):

    dp[u][0] ← 0
    dp[u][1] ← 1        // include u itself

    for each v in adj[u]:
        if v ≠ parent then
            DFS(v, u)

            // If u is included, children must be excluded
            dp[u][1] ← dp[u][1] + dp[v][0]

            // If u is excluded, choose best for each child
            dp[u][0] ← dp[u][0] + max(dp[v][0], dp[v][1])


Main:

    read n
    build adjacency list adj

    DFS(1, -1)

    answer ← max(dp[1][0], dp[1][1])
    print answer

```

We use a post-order DFS so that children are computed before the parent.

---

# Example

```
      1
     / \
    2   3
   /     \
  4       5
```

Edges:
- (1,2)
- (1,3)
- (2,4)
- (3,5)

### Leaves

- $dp[4] = (0,1)$
- $dp[5] = (0,1)$

### Node 2

- $dp[2][1] = 1$
- $dp[2][0] = 1$

### Node 3

- $dp[3][1] = 1$
- $dp[3][0] = 1$

### Node 1

If include 1:

$$
dp[1][1] = 1 + 1 + 1 = 3
$$

If exclude 1:

$$
dp[1][0] = 1 + 1 = 2
$$

### Final Answer

$$
\max(2,3) = 3
$$

One maximum independent set is:

$$
\{1,4,5\}
$$

---

# Why This Works (Proof Sketch)

Every independent set in a subtree either:

- includes node $u$, or  
- excludes node $u$

We compute the optimal value for both cases.

Because trees contain no cycles:

- Child subtrees are independent  
- Optimal substructure holds  

Thus taking

$$
\max(dp[u][0], dp[u][1])
$$

gives the correct result.

---

# Complexity Analysis

### Time Complexity

Each node is visited once.

$$
O(n)
$$

### Space Complexity

DP storage:

$$
O(n)
$$

Recursion stack:

$$
O(h)
$$

Worst case: $O(n)$

---

# Advanced Section: Treewidth


Treewidth measures how “tree-like” a graph is.

- Trees have treewidth = 1  
- Cycles have treewidth = 2  
- A clique on $n$ vertices has treewidth $n-1$

If the largest bag in a tree decomposition has size $w+1$, the graph has treewidth $w$.

---

## DP on Tree Decomposition

For each bag $X_t$ and subset $S \subseteq X_t$ that is independent, define:

$$
dp_t(S)
$$

as the maximum size of an independent set consistent with selecting exactly $S$ inside $X_t$.

Since $|X_t| \le w+1$:

$$
\text{States per bag} = O(2^{w+1})
$$

---

## Recurrences

### Introduce Node

If $v \notin S$:

$$
dp_t(S) = dp_{child}(S)
$$

If $v \in S$:

$$
dp_t(S) = dp_{child}(S \setminus \{v\}) + 1
$$

---

### Forget Node

$$
dp_t(S) = \max(dp_{child}(S), dp_{child}(S \cup \{v\}))
$$

---

### Join Node

$$
dp_t(S) = dp_{left}(S) + dp_{right}(S) - |S|
$$

---

## Total Complexity

$$
O(2^w \cdot n)
$$

Thus MIS is solvable in:

$$
O\big(2^{\text{treewidth}(G)} \cdot \text{poly}(n)\big)
$$

For trees ($w=1$):

$$
O(n)
$$

---

# Implementation

A C++ implementation is provided in:

```
solution.cpp
```

---

# Related Practice Problems

- LeetCode 337 — House Robber III  
- Minimum Vertex Cover on Trees  
- LeetCode 968 — Binary Tree Cameras  

---

# Takeaways

- MIS is NP-hard in general graphs  
- Trees allow linear-time DP  
- Treewidth generalizes this idea  
- MIS is FPT with respect to treewidth  

---

# References

- Kleinberg & Tardos — *Algorithm Design*  
- CLRS — *Introduction to Algorithms*
