# Problem → Pattern Identification Guide

> **The #1 skill in DSA is recognizing WHICH pattern to apply.**
> This guide teaches you to read problem signals like a pro.

---

## Master Keyword Table

| When You See These Keywords/Signals | Think This Pattern |
|--------------------------------------|-------------------|
| "sorted array", "pair with target sum" | Two Pointers |
| "contiguous subarray", "substring of size k" | Sliding Window |
| "cycle in linked list", "middle of linked list" | Fast & Slow Pointers |
| "overlapping intervals", "merge intervals" | Merge Intervals |
| "missing number", "duplicate in range [1,n]" | Cyclic Sort |
| "reverse a linked list", "reverse sub-list" | In-place Linked List Reversal |
| "level order traversal", "minimum depth" | Tree BFS |
| "path sum", "all paths", "max depth" | Tree DFS |
| "median of stream", "schedule tasks" | Two Heaps |
| "all combinations", "all permutations", "subsets" | Backtracking |
| "sorted array/matrix", "find target" | Modified Binary Search |
| "k-th largest", "top k frequent" | Top K Elements (Heap) |
| "merge k sorted lists/arrays" | K-way Merge |
| "ordering with dependencies", "course schedule" | Topological Sort |
| "minimum cost", "number of ways", "can we reach..." | Dynamic Programming |
| "next greater element", "largest rectangle" | Monotonic Stack |
| "connected components", "redundant connection" | Union Find |
| "prefix matching", "word search", "autocomplete" | Trie |
| "single number", "power of 2", "XOR" | Bit Manipulation |
| "shortest path", "number of islands" | Graph BFS/DFS |
| "range sum", "subarray sum equals k" | Prefix Sum |
| "activity selection", "minimum platforms" | Greedy |
| "shortest path weighted graph", "network delay" | Shortest Path (Dijkstra/Bellman-Ford) |
| "sliding window maximum/minimum" | Monotonic Queue |
| "range sum query + update", "mutable range" | Segment Tree / Fenwick Tree |
| "find pattern in string", "repeated substring" | String Matching (KMP/Rabin-Karp) |
| "design LRU cache", "implement data structure" | Design Problems |
| "GCD", "count primes", "mod 10^9+7" | Math & Number Theory |
| "count inversions", "split into halves to solve" | Divide & Conquer |

---

## Constraint-Based Identification

### Input Size → Algorithm Complexity

| Input Size (n) | Expected Complexity | Possible Patterns |
|----------------|--------------------|--------------------|
| n ≤ 15 | O(2^n) or O(n!) | Backtracking, Bitmask DP |
| n ≤ 25 | O(2^(n/2)) | Meet in the Middle |
| n ≤ 100 | O(n³) | Floyd-Warshall, Triple loops |
| n ≤ 1,000 | O(n²) | DP (2D), Brute force pairs |
| n ≤ 10,000 | O(n² ) or O(n√n) | DP with optimization |
| n ≤ 100,000 | O(n log n) | Sorting, Binary Search, Heap |
| n ≤ 1,000,000 | O(n) or O(n log n) | Two Pointers, Sliding Window, Prefix Sum |
| n ≤ 10^8 | O(n) | Linear scan |
| n ≤ 10^18 | O(log n) or O(√n) | Binary Search, Math |

---

## Problem Type Decision Trees

### Array/String Problems

```
Array/String Problem
│
├── Need pair/triplet with sum?
│   ├── Array is sorted → Two Pointers
│   └── Array is unsorted → HashMap or Sort + Two Pointers
│
├── Need subarray/substring?
│   ├── Fixed size window → Fixed Sliding Window
│   ├── Variable size (max/min length) → Variable Sliding Window
│   ├── Sum of subarray → Prefix Sum
│   └── Count subarrays with condition → Prefix Sum + HashMap
│
├── Numbers in range [1, n]?
│   └── Cyclic Sort (missing/duplicate)
│
├── Need to merge overlapping ranges?
│   └── Merge Intervals (sort by start)
│
├── Next greater/smaller element?
│   └── Monotonic Stack
│
├── Min/Max of all subarrays of size k?
│   └── Monotonic Deque (variation of Sliding Window)
│
└── Optimization (min cost, max profit)?
    ├── Can we make greedy choice? → Greedy
    └── Need previous results? → Dynamic Programming
```

### Linked List Problems

```
Linked List Problem
│
├── Detect cycle or find meeting point?
│   └── Fast & Slow Pointers
│
├── Find middle element?
│   └── Fast & Slow Pointers
│
├── Reverse (whole or part)?
│   └── In-place Reversal
│
├── Merge k sorted lists?
│   └── K-way Merge with Heap
│
└── Rearrange nodes?
    └── Two Pointers + In-place Reversal
```

### Tree Problems

```
Tree Problem
│
├── Level-by-level processing?
│   └── BFS (Queue)
│
├── Path from root to leaf?
│   └── DFS (Recursion / Stack)
│
├── Serialize / Deserialize?
│   └── BFS or Preorder DFS
│
├── Lowest Common Ancestor?
│   └── DFS
│
├── BST operations?
│   └── Modified Binary Search + DFS
│
└── Max/Min depth, diameter?
    └── DFS (Post-order)
```

### Graph Problems

```
Graph Problem
│
├── Shortest path (unweighted)?
│   └── BFS
│
├── Shortest path (weighted, no negative)?
│   └── Dijkstra's
│
├── Shortest path (negative weights)?
│   └── Bellman-Ford
│
├── Connected components?
│   ├── Union Find (efficient for dynamic queries)
│   └── DFS/BFS (simple traversal)
│
├── Ordering with prerequisites?
│   └── Topological Sort (Kahn's BFS or DFS)
│
├── Detect cycle?
│   ├── Directed graph → Topological Sort or DFS coloring
│   └── Undirected graph → Union Find or DFS
│
├── All paths / explore all possibilities?
│   └── DFS + Backtracking
│
└── Minimum Spanning Tree?
    └── Kruskal's (Union Find) or Prim's (Heap)
```

---

## 10 Questions to Ask Yourself

Before coding, run through this checklist:

1. **What is the input type?** (Array, String, Linked List, Tree, Graph, Number)
2. **What is the output type?** (Single value, Array, Boolean, Count)
3. **Is the input sorted?** If yes → Binary Search, Two Pointers
4. **What are the constraints (n)?** → Determines acceptable complexity
5. **Does the problem involve subsequences/subsets?** → DP or Backtracking
6. **Does it ask for contiguous elements?** → Sliding Window or Prefix Sum
7. **Does it involve optimization?** → DP or Greedy
8. **Are there dependencies/orderings?** → Topological Sort
9. **Is it about connectivity?** → Union Find or Graph DFS/BFS
10. **Can I reduce it to a known pattern?** → Check the keyword table above

---

## Common Traps & How to Avoid Them

| Trap | What Happens | How to Avoid |
|------|-------------|--------------|
| Using HashMap when Two Pointers works | Wasted space O(n) | Check if input is sorted first |
| Backtracking when DP is better | TLE (Time Limit Exceeded) | Check for overlapping subproblems |
| BFS when DFS is simpler | Unnecessary queue overhead | If no "shortest" or "level" needed, try DFS |
| Forgot edge case: empty input | Runtime error | Always check `len(input) == 0` |
| Off-by-one in Binary Search | Infinite loop or wrong answer | Use `lo <= hi` with clear boundary semantics |
| Not handling duplicates | Wrong count or infinite loop | Skip duplicates in sorted arrays |

---

## Pattern Combination Cheat Codes

Many hard problems combine 2+ patterns:

| Problem Type | Pattern Combination |
|-------------|-------------------|
| K-th smallest in sorted matrix | Binary Search + K-way Merge |
| Word search in grid | Graph DFS + Backtracking + Trie |
| Meeting rooms II | Merge Intervals + Heap |
| Alien dictionary | Topological Sort + Graph BFS |
| Sliding window maximum | Sliding Window + Monotonic Deque |
| Shortest path with obstacles | Graph BFS + DP |
| Minimum window substring | Sliding Window + HashMap |
| Course schedule with order | Topological Sort + DFS |
| Longest increasing subsequence | DP + Binary Search |
| Network delay time | Graph + Dijkstra (Heap) |

---

*Use this guide as your first stop when facing a new problem. Pattern recognition is a skill — the more you practice, the faster you'll identify them.*
