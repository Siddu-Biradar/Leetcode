# DSA Patterns CheatSheet — Go Language

> **Your ultimate guide to cracking coding interviews and competitive programming.**
> Every pattern includes: Theory | When to Use | How to Identify | Go Code Templates | Practice Problems | Hints

---

## How to Use This Guide

1. **Read the Pattern Theory** — Understand *why* the pattern works
2. **Study the Identification Signals** — Learn to spot patterns in new problems
3. **Master the Go Templates** — Internalize the code skeleton
4. **Solve Practice Problems** — Apply patterns to real problems
5. **Review Hints** — When stuck, use hints before looking at solutions

---

## Pattern Index

| # | Pattern | Difficulty | File |
|---|---------|-----------|------|
| 01 | [Two Pointers](patterns/01-two-pointers.md) | Easy-Medium | `patterns/01-two-pointers.md` |
| 02 | [Sliding Window](patterns/02-sliding-window.md) | Medium | `patterns/02-sliding-window.md` |
| 03 | [Fast & Slow Pointers](patterns/03-fast-slow-pointers.md) | Medium | `patterns/03-fast-slow-pointers.md` |
| 04 | [Merge Intervals](patterns/04-merge-intervals.md) | Medium | `patterns/04-merge-intervals.md` |
| 05 | [Cyclic Sort](patterns/05-cyclic-sort.md) | Easy-Medium | `patterns/05-cyclic-sort.md` |
| 06 | [In-place Linked List Reversal](patterns/06-linked-list-reversal.md) | Medium | `patterns/06-linked-list-reversal.md` |
| 07 | [Tree BFS (Level Order)](patterns/07-tree-bfs.md) | Medium | `patterns/07-tree-bfs.md` |
| 08 | [Tree DFS](patterns/08-tree-dfs.md) | Medium | `patterns/08-tree-dfs.md` |
| 09 | [Two Heaps](patterns/09-two-heaps.md) | Hard | `patterns/09-two-heaps.md` |
| 10 | [Subsets / Backtracking](patterns/10-subsets-backtracking.md) | Medium-Hard | `patterns/10-subsets-backtracking.md` |
| 11 | [Modified Binary Search](patterns/11-binary-search.md) | Medium | `patterns/11-binary-search.md` |
| 12 | [Top K Elements](patterns/12-top-k-elements.md) | Medium | `patterns/12-top-k-elements.md` |
| 13 | [K-way Merge](patterns/13-k-way-merge.md) | Hard | `patterns/13-k-way-merge.md` |
| 14 | [Topological Sort](patterns/14-topological-sort.md) | Medium-Hard | `patterns/14-topological-sort.md` |
| 15 | [Dynamic Programming](patterns/15-dynamic-programming.md) | Medium-Hard | `patterns/15-dynamic-programming.md` |
| 16 | [Monotonic Stack](patterns/16-monotonic-stack.md) | Medium | `patterns/16-monotonic-stack.md` |
| 17 | [Union Find (Disjoint Set)](patterns/17-union-find.md) | Medium-Hard | `patterns/17-union-find.md` |
| 18 | [Trie (Prefix Tree)](patterns/18-trie.md) | Medium | `patterns/18-trie.md` |
| 19 | [Bit Manipulation](patterns/19-bit-manipulation.md) | Easy-Medium | `patterns/19-bit-manipulation.md` |
| 20 | [Graph BFS / DFS](patterns/20-graph-bfs-dfs.md) | Medium-Hard | `patterns/20-graph-bfs-dfs.md` |
| 21 | [Prefix Sum](patterns/21-prefix-sum.md) | Easy-Medium | `patterns/21-prefix-sum.md` |
| 22 | [Greedy Algorithms](patterns/22-greedy.md) | Medium-Hard | `patterns/22-greedy.md` |

---

## Quick Reference Guides

| Guide | File |
|-------|------|
| [Problem → Pattern Identification Guide](problem-identification-guide.md) | `problem-identification-guide.md` |
| [Big-O Complexity CheatSheet](complexity-cheatsheet.md) | `complexity-cheatsheet.md` |
| [Go-Specific DSA Tips & Tricks](go-dsa-tips.md) | `go-dsa-tips.md` |

---

## Pattern Decision Flowchart

```
START: Read the problem
│
├── Is input SORTED or can be sorted?
│   ├── Search for a target? → Binary Search (#11)
│   ├── Pair/triplet with target sum? → Two Pointers (#01)
│   └── Merge sorted lists? → K-way Merge (#13)
│
├── Deals with SUBARRAYS or SUBSTRINGS?
│   ├── Fixed or variable window? → Sliding Window (#02)
│   ├── Cumulative sum queries? → Prefix Sum (#21)
│   └── Next greater/smaller element? → Monotonic Stack (#16)
│
├── Involves a LINKED LIST?
│   ├── Detect cycle? → Fast & Slow Pointers (#03)
│   └── Reverse part of list? → In-place Reversal (#06)
│
├── Involves a TREE?
│   ├── Level-by-level? → Tree BFS (#07)
│   └── Path/depth/leaf? → Tree DFS (#08)
│
├── Involves a GRAPH?
│   ├── Shortest path (unweighted)? → Graph BFS (#20)
│   ├── Connected components? → Union Find (#17) or DFS (#20)
│   ├── Ordering with dependencies? → Topological Sort (#14)
│   └── Explore all paths? → Graph DFS (#20)
│
├── Need ALL combinations/permutations?
│   └── → Subsets / Backtracking (#10)
│
├── OPTIMIZATION problem (min/max)?
│   ├── Overlapping subproblems? → Dynamic Programming (#15)
│   ├── Local choice leads to global? → Greedy (#22)
│   └── Find median in stream? → Two Heaps (#09)
│
├── Find TOP K / K-th largest?
│   └── → Top K Elements with Heap (#12)
│
├── Numbers in range [1, n]?
│   └── → Cyclic Sort (#05)
│
├── String prefix matching?
│   └── → Trie (#18)
│
└── Constraints involve powers of 2 or XOR?
    └── → Bit Manipulation (#19)
```

---

## Study Plan (4 Weeks)

### Week 1: Foundation Patterns
- Day 1-2: Two Pointers + Prefix Sum
- Day 3-4: Sliding Window
- Day 5-6: Binary Search
- Day 7: Review + Practice

### Week 2: Linked List & Tree Patterns
- Day 1: Fast & Slow Pointers
- Day 2: Linked List Reversal
- Day 3-4: Tree BFS + Tree DFS
- Day 5: Merge Intervals + Cyclic Sort
- Day 6-7: Review + Practice

### Week 3: Advanced Patterns
- Day 1-2: Backtracking / Subsets
- Day 3: Top K Elements + Two Heaps
- Day 4: K-way Merge + Monotonic Stack
- Day 5-6: Graph BFS/DFS + Union Find
- Day 7: Topological Sort + Trie

### Week 4: DP, Greedy & Mastery
- Day 1-3: Dynamic Programming (all sub-patterns)
- Day 4: Greedy Algorithms
- Day 5: Bit Manipulation
- Day 6-7: Mixed practice & mock interviews

---

## Golden Rules for Interviews

1. **Clarify** — Always ask about constraints, edge cases, input size
2. **Brute Force First** — State the naive solution and its complexity
3. **Optimize** — Identify the pattern, explain the approach
4. **Code** — Write clean, readable code
5. **Test** — Walk through examples, check edge cases
6. **Analyze** — State time and space complexity

---

*Happy Coding! 🚀*
