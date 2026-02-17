# DSA Patterns Cheatsheet (Go)

> **Your one-stop reference for every DSA pattern with theory, templates, and solved problems — all in Go.**

---

## Pattern Index

| # | Pattern | Key Problems | File |
|---|---------|-------------|------|
| 01 | Two Pointers | Two Sum II, Three Sum, Container With Most Water | [patterns/01-two-pointers.md](patterns/01-two-pointers.md) |
| 02 | Sliding Window | Minimum Window Substring, Longest Substring Without Repeating | [patterns/02-sliding-window.md](patterns/02-sliding-window.md) |
| 03 | Fast & Slow Pointers | Linked List Cycle, Find Duplicate Number | [patterns/03-fast-slow-pointers.md](patterns/03-fast-slow-pointers.md) |
| 04 | Merge Intervals | Merge Intervals, Meeting Rooms II | [patterns/04-merge-intervals.md](patterns/04-merge-intervals.md) |
| 05 | Cyclic Sort | Missing Number, First Missing Positive | [patterns/05-cyclic-sort.md](patterns/05-cyclic-sort.md) |
| 06 | Linked List Reversal | Reverse Linked List, Reverse K-Group | [patterns/06-linked-list-reversal.md](patterns/06-linked-list-reversal.md) |
| 07 | Tree BFS | Level Order, Zigzag, Right Side View | [patterns/07-tree-bfs.md](patterns/07-tree-bfs.md) |
| 08 | Tree DFS | Path Sum, LCA, Validate BST | [patterns/08-tree-dfs.md](patterns/08-tree-dfs.md) |
| 09 | Two Heaps | Find Median from Data Stream | [patterns/09-two-heaps.md](patterns/09-two-heaps.md) |
| 10 | Subsets / Backtracking | Subsets, Permutations, N-Queens | [patterns/10-subsets-backtracking.md](patterns/10-subsets-backtracking.md) |
| 11 | Binary Search | Rotated Array, Koko Eating Bananas | [patterns/11-binary-search.md](patterns/11-binary-search.md) |
| 12 | Top K Elements | Kth Largest, Top K Frequent | [patterns/12-top-k-elements.md](patterns/12-top-k-elements.md) |
| 13 | K-Way Merge | Merge K Sorted Lists | [patterns/13-k-way-merge.md](patterns/13-k-way-merge.md) |
| 14 | Topological Sort | Course Schedule, Alien Dictionary | [patterns/14-topological-sort.md](patterns/14-topological-sort.md) |
| 15 | Dynamic Programming | House Robber, Coin Change, LCS, Edit Distance | [patterns/15-dynamic-programming.md](patterns/15-dynamic-programming.md) |
| 16 | Monotonic Stack | Daily Temperatures, Largest Rectangle | [patterns/16-monotonic-stack.md](patterns/16-monotonic-stack.md) |
| 17 | Union Find | Number of Provinces, Accounts Merge | [patterns/17-union-find.md](patterns/17-union-find.md) |
| 18 | Trie | Implement Trie, Word Search II | [patterns/18-trie.md](patterns/18-trie.md) |
| 19 | Bit Manipulation | Single Number, Counting Bits | [patterns/19-bit-manipulation.md](patterns/19-bit-manipulation.md) |
| 20 | Graph BFS / DFS | Number of Islands, Clone Graph, Word Ladder | [patterns/20-graph-bfs-dfs.md](patterns/20-graph-bfs-dfs.md) |
| 21 | Prefix Sum | Subarray Sum Equals K, Range Sum Query | [patterns/21-prefix-sum.md](patterns/21-prefix-sum.md) |
| 22 | Greedy | Jump Game, Task Scheduler, Partition Labels | [patterns/22-greedy.md](patterns/22-greedy.md) |
| 23 | Shortest Path Algorithms | Dijkstra, Bellman-Ford, Network Delay Time | [patterns/23-shortest-path.md](patterns/23-shortest-path.md) |
| 24 | Monotonic Queue | Sliding Window Maximum | [patterns/24-monotonic-queue.md](patterns/24-monotonic-queue.md) |
| 25 | Segment Tree / Fenwick Tree | Range Sum Query Mutable, Count of Smaller Numbers | [patterns/25-segment-tree.md](patterns/25-segment-tree.md) |
| 26 | String Matching | KMP, Rabin-Karp, Repeated Substring | [patterns/26-string-matching.md](patterns/26-string-matching.md) |
| 27 | Design Problems | LRU Cache, Min Stack, Iterator | [patterns/27-design-problems.md](patterns/27-design-problems.md) |
| 28 | Math & Number Theory | GCD, Sieve, Modular Arithmetic | [patterns/28-math-number-theory.md](patterns/28-math-number-theory.md) |
| 29 | Divide & Conquer | Merge Sort, Count Inversions, Closest Pair | [patterns/29-divide-and-conquer.md](patterns/29-divide-and-conquer.md) |

---

## Supporting Guides

| Guide | Description |
|-------|-------------|
| [Problem Identification Guide](problem-identification-guide.md) | How to figure out which pattern to use |
| [Complexity Cheatsheet](complexity-cheatsheet.md) | Big-O reference for all algorithms & data structures |
| [Go DSA Tips](go-dsa-tips.md) | Go-specific tricks, gotchas, and templates |

---

## Decision Flowchart

```
Start: What is the input?
│
├─ Array/String
│  ├─ Sorted? ──────────────────────→ Two Pointers / Binary Search
│  ├─ Subarray/Substring? ─────────→ Sliding Window / Prefix Sum
│  ├─ Pairs/Triplets? ─────────────→ Two Pointers / HashMap
│  ├─ Range [1, n] with missing? ──→ Cyclic Sort
│  ├─ Next greater/smaller? ───────→ Monotonic Stack
│  ├─ Sliding window max/min? ─────→ Monotonic Queue
│  ├─ Range query + updates? ──────→ Segment Tree / Fenwick Tree
│  ├─ Pattern matching? ──────────→ String Matching (KMP/Rabin-Karp)
│  └─ All subsets/permutations? ───→ Backtracking
│
├─ Linked List
│  ├─ Cycle? ───────────────────────→ Fast & Slow Pointers
│  └─ Reverse? ────────────────────→ In-place Reversal
│
├─ Tree
│  ├─ Level by level? ─────────────→ BFS
│  └─ Path / depth / validation? ──→ DFS
│
├─ Graph
│  ├─ Shortest path (unweighted)? ─→ BFS
│  ├─ Shortest path (weighted)? ───→ Dijkstra / Bellman-Ford
│  ├─ Connected components? ───────→ Union Find / DFS
│  ├─ Ordering with deps? ────────→ Topological Sort
│  └─ Explore all states? ────────→ DFS + Backtracking
│
├─ Intervals
│  └─ Overlapping / merge / insert → Merge Intervals
│
├─ Stream / Running data
│  ├─ Median? ──────────────────────→ Two Heaps
│  └─ Kth largest/smallest? ───────→ Heap / Top K
│
├─ Design / System
│  └─ Cache, Stack variant, etc. ──→ Design Problems
│
├─ Math / Number
│  └─ Primes, GCD, combinatorics ──→ Math Patterns
│
└─ Optimization / Counting
   ├─ Can split into subproblems? ──→ Divide & Conquer
   ├─ Overlapping subproblems? ─────→ Dynamic Programming
   └─ Local choice = global best? ──→ Greedy
```

---

## 5-Week Study Plan

| Week | Patterns | Focus |
|------|----------|-------|
| **Week 1** | Two Pointers, Sliding Window, Fast-Slow, Prefix Sum | Array/String fundamentals |
| **Week 2** | Binary Search, Merge Intervals, Cyclic Sort, Monotonic Stack/Queue | Search & ordering |
| **Week 3** | Tree BFS/DFS, Graph BFS/DFS, Topological Sort, Shortest Path, Union Find | Trees & Graphs |
| **Week 4** | DP, Backtracking, Greedy, Divide & Conquer | Recursion & optimization |
| **Week 5** | Heaps, Trie, Segment Tree, String Matching, Design, Math, Bit Manipulation | Advanced topics |

---

## Golden Rules for Interviews

1. **Clarify first** — Ask about constraints, edge cases, input size
2. **Brute force first** — State the obvious O(n²) or O(2ⁿ) solution
3. **Optimize with patterns** — Identify the pattern from signals
4. **Talk through your approach** — Explain before coding
5. **Code cleanly** — Use meaningful variable names
6. **Test with examples** — Walk through your code with the given example
7. **Analyze complexity** — State time and space complexity
8. **Handle edge cases** — Empty input, single element, duplicates, negative numbers