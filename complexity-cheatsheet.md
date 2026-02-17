# Big-O Complexity CheatSheet

## Time Complexity Ranking (Best → Worst)

```
O(1) < O(log n) < O(√n) < O(n) < O(n log n) < O(n²) < O(n³) < O(2^n) < O(n!) < O(n^n)
```

| Complexity | Name | Example | n=1000 ops |
|-----------|------|---------|------------|
| O(1) | Constant | HashMap lookup, array index | 1 |
| O(log n) | Logarithmic | Binary search | ~10 |
| O(√n) | Square root | Prime check | ~31 |
| O(n) | Linear | Single loop, linear scan | 1,000 |
| O(n log n) | Linearithmic | Merge sort, heap sort | ~10,000 |
| O(n²) | Quadratic | Nested loops, bubble sort | 1,000,000 |
| O(n³) | Cubic | Triple nested loops | 10^9 (TLE!) |
| O(2^n) | Exponential | Subsets, backtracking | 10^301 (impossible) |
| O(n!) | Factorial | Permutations | ∞ (impossible) |

---

## Data Structure Operations

### Arrays / Slices

| Operation | Time | Notes |
|-----------|------|-------|
| Access by index | O(1) | |
| Append | O(1) amortized | May trigger reallocation |
| Insert at beginning | O(n) | Shift all elements |
| Insert at position i | O(n) | Shift n-i elements |
| Delete at position i | O(n) | Shift n-i elements |
| Search (unsorted) | O(n) | Linear scan |
| Search (sorted) | O(log n) | Binary search |
| Sort | O(n log n) | Go's sort package |

### HashMap (Go map)

| Operation | Average | Worst |
|-----------|---------|-------|
| Insert | O(1) | O(n) |
| Lookup | O(1) | O(n) |
| Delete | O(1) | O(n) |
| Iteration | O(n) | O(n) |

### Linked List

| Operation | Time |
|-----------|------|
| Access by index | O(n) |
| Insert at head | O(1) |
| Insert at tail (with tail pointer) | O(1) |
| Insert at position | O(n) |
| Delete (with reference) | O(1) |
| Search | O(n) |

### Stack / Queue (using slice)

| Operation | Time |
|-----------|------|
| Push / Enqueue | O(1) amortized |
| Pop / Dequeue | O(1) |
| Peek | O(1) |

### Binary Heap (Priority Queue)

| Operation | Time |
|-----------|------|
| Insert (Push) | O(log n) |
| Extract Min/Max (Pop) | O(log n) |
| Peek | O(1) |
| Build Heap | O(n) |
| Heapify | O(log n) |

### Binary Search Tree (Balanced)

| Operation | Average | Worst (unbalanced) |
|-----------|---------|-------------------|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |
| Min/Max | O(log n) | O(n) |

### Trie

| Operation | Time | Space |
|-----------|------|-------|
| Insert | O(m) | O(m) per word |
| Search | O(m) | - |
| Prefix Search | O(p) | - |
(m = word length, p = prefix length)

### Union Find (with path compression + union by rank)

| Operation | Time |
|-----------|------|
| Find | O(α(n)) ≈ O(1) |
| Union | O(α(n)) ≈ O(1) |

---

## Sorting Algorithm Comparison

| Algorithm | Best | Average | Worst | Space | Stable |
|-----------|------|---------|-------|-------|--------|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Counting Sort | O(n+k) | O(n+k) | O(n+k) | O(k) | Yes |
| Radix Sort | O(d·(n+k)) | O(d·(n+k)) | O(d·(n+k)) | O(n+k) | Yes |
| Bucket Sort | O(n+k) | O(n+k) | O(n²) | O(n) | Yes |

**Go's `sort.Slice`:** Uses pattern-defeating quicksort — O(n log n) average, O(n log n) worst.

---

## Graph Algorithm Complexities

| Algorithm | Time | Space | Use Case |
|-----------|------|-------|----------|
| BFS | O(V+E) | O(V) | Shortest path (unweighted) |
| DFS | O(V+E) | O(V) | Traversal, cycle detection |
| Dijkstra | O((V+E) log V) | O(V) | Shortest path (weighted, no neg) |
| Bellman-Ford | O(V·E) | O(V) | Shortest path (negative weights) |
| Floyd-Warshall | O(V³) | O(V²) | All-pairs shortest paths |
| Kruskal's MST | O(E log E) | O(V) | Minimum spanning tree |
| Prim's MST | O((V+E) log V) | O(V) | Minimum spanning tree |
| Topological Sort | O(V+E) | O(V) | DAG ordering |

---

## Pattern Complexity Summary

| Pattern | Typical Time | Typical Space |
|---------|-------------|---------------|
| Two Pointers | O(n) | O(1) |
| Sliding Window | O(n) | O(1) or O(k) |
| Fast & Slow Pointers | O(n) | O(1) |
| Merge Intervals | O(n log n) | O(n) |
| Cyclic Sort | O(n) | O(1) |
| Linked List Reversal | O(n) | O(1) |
| Tree BFS | O(n) | O(n) |
| Tree DFS | O(n) | O(h) |
| Two Heaps | O(n log n) | O(n) |
| Backtracking | O(2^n) or O(n!) | O(n) |
| Binary Search | O(log n) | O(1) |
| Top K Elements | O(n log k) | O(k) |
| K-way Merge | O(N log K) | O(K) |
| Topological Sort | O(V+E) | O(V+E) |
| Dynamic Programming | Varies | Varies |
| Monotonic Stack | O(n) | O(n) |
| Union Find | O(n·α(n)) | O(n) |
| Trie | O(m) per op | O(alphabet·m·n) |
| Bit Manipulation | O(n) or O(1) | O(1) |
| Graph BFS/DFS | O(V+E) | O(V) |
| Prefix Sum | O(n) build, O(1) query | O(n) |
| Greedy | O(n) or O(n log n) | O(1) |

---

## Quick Rules of Thumb

- **1 second** ≈ 10^8 simple operations
- **n ≤ 10** → O(n!), O(2^n) okay
- **n ≤ 20** → O(2^n) okay
- **n ≤ 500** → O(n³) okay
- **n ≤ 5000** → O(n²) okay
- **n ≤ 10^6** → O(n log n) needed
- **n ≤ 10^8** → O(n) needed
- **n > 10^8** → O(log n) or O(1) needed

---

[Back to Index](README.md)
