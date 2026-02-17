# Pattern 25: Segment Tree / Fenwick Tree (BIT)

## Theory

Both data structures handle **range queries with point updates** efficiently.

| Operation | Array | Prefix Sum | Segment Tree | Fenwick Tree |
|-----------|-------|-----------|--------------|--------------|
| Build | O(n) | O(n) | O(n) | O(n) |
| Point Update | O(1) | O(n) rebuild | **O(log n)** | **O(log n)** |
| Range Query | O(n) | O(1) | **O(log n)** | **O(log n)** |

**When to use which:**
- Static array, only queries → **Prefix Sum**
- Queries + updates → **Segment Tree / Fenwick Tree**
- Need range update + range query → **Segment Tree with Lazy Propagation**
- Simple sum/min/max queries + point updates → **Fenwick Tree** (simpler code)

---

## How to Identify

- "Range sum query" + "update" in the same problem
- "Count of elements smaller/larger" in a range
- "Mutable array" + "range queries"
- Need to query aggregate (sum, min, max, GCD) over a range AND update individual elements
- Problems involving Merge Sort Tree or counting inversions

---

## Part 1: Segment Tree

### Theory

A Segment Tree is a **binary tree** where:
- Each **leaf** represents an array element
- Each **internal node** represents a range and stores an aggregate (sum, min, max)
- We use a flat array of size `4n` to represent the tree

```
Array:     [1, 3, 5, 7, 9, 11]
Tree:             36
              /        \
           9              27
          / \            /   \
        4     5       16      11
       / \           / \
      1   3         7   9
```

### Template: Range Sum Segment Tree

```go
type SegTree struct {
    tree []int
    n    int
}

func NewSegTree(nums []int) *SegTree {
    n := len(nums)
    st := &SegTree{
        tree: make([]int, 4*n),
        n:    n,
    }
    st.build(nums, 1, 0, n-1)
    return st
}

func (st *SegTree) build(nums []int, node, start, end int) {
    if start == end {
        st.tree[node] = nums[start]
        return
    }
    mid := (start + end) / 2
    st.build(nums, 2*node, start, mid)
    st.build(nums, 2*node+1, mid+1, end)
    st.tree[node] = st.tree[2*node] + st.tree[2*node+1]
}

// Update index i to val
func (st *SegTree) Update(i, val int) {
    st.update(1, 0, st.n-1, i, val)
}

func (st *SegTree) update(node, start, end, idx, val int) {
    if start == end {
        st.tree[node] = val
        return
    }
    mid := (start + end) / 2
    if idx <= mid {
        st.update(2*node, start, mid, idx, val)
    } else {
        st.update(2*node+1, mid+1, end, idx, val)
    }
    st.tree[node] = st.tree[2*node] + st.tree[2*node+1]
}

// Query sum of range [l, r]
func (st *SegTree) Query(l, r int) int {
    return st.query(1, 0, st.n-1, l, r)
}

func (st *SegTree) query(node, start, end, l, r int) int {
    if r < start || end < l {
        return 0 // Out of range
    }
    if l <= start && end <= r {
        return st.tree[node] // Fully within range
    }
    mid := (start + end) / 2
    left := st.query(2*node, start, mid, l, r)
    right := st.query(2*node+1, mid+1, end, l, r)
    return left + right
}
```

### Range Min Segment Tree (change merge function)

```go
// In build and update, change merge to:
st.tree[node] = min(st.tree[2*node], st.tree[2*node+1])

// In query, change identity to:
if r < start || end < l {
    return math.MaxInt64 // Identity for min
}
```

---

## Part 2: Fenwick Tree (Binary Indexed Tree)

### Theory

A Fenwick Tree uses **bit manipulation** to achieve O(log n) updates and prefix queries. It's simpler to implement than a Segment Tree but only supports operations that have an **inverse** (sum, XOR — not min/max natively).

**Key Trick:** `i & (-i)` gives the lowest set bit, which determines the range each node covers.

### Template

```go
type FenwickTree struct {
    tree []int
    n    int
}

func NewFenwick(n int) *FenwickTree {
    return &FenwickTree{
        tree: make([]int, n+1), // 1-indexed
        n:    n,
    }
}

// Add delta to index i (1-indexed)
func (ft *FenwickTree) Update(i, delta int) {
    for ; i <= ft.n; i += i & (-i) {
        ft.tree[i] += delta
    }
}

// Prefix sum [1, i]
func (ft *FenwickTree) Query(i int) int {
    sum := 0
    for ; i > 0; i -= i & (-i) {
        sum += ft.tree[i]
    }
    return sum
}

// Range sum [l, r] (1-indexed)
func (ft *FenwickTree) RangeQuery(l, r int) int {
    return ft.Query(r) - ft.Query(l-1)
}
```

### Build from array

```go
func NewFenwickFromArray(nums []int) *FenwickTree {
    ft := NewFenwick(len(nums))
    for i, v := range nums {
        ft.Update(i+1, v) // 1-indexed
    }
    return ft
}
```

---

## Solved Problems

### Problem 1: Range Sum Query - Mutable (LC 307)

> Given an array, handle `update(index, val)` and `sumRange(left, right)`.

**Pattern:** Classic Segment Tree / Fenwick Tree use case.

```go
// Using Fenwick Tree
type NumArray struct {
    tree []int
    nums []int
    n    int
}

func Constructor(nums []int) NumArray {
    n := len(nums)
    na := NumArray{
        tree: make([]int, n+1),
        nums: make([]int, n),
        n:    n,
    }
    copy(na.nums, nums)
    for i, v := range nums {
        na.add(i+1, v)
    }
    return na
}

func (na *NumArray) add(i, delta int) {
    for ; i <= na.n; i += i & (-i) {
        na.tree[i] += delta
    }
}

func (na *NumArray) prefixSum(i int) int {
    sum := 0
    for ; i > 0; i -= i & (-i) {
        sum += na.tree[i]
    }
    return sum
}

func (na *NumArray) Update(index int, val int) {
    delta := val - na.nums[index]
    na.nums[index] = val
    na.add(index+1, delta)
}

func (na *NumArray) SumRange(left int, right int) int {
    return na.prefixSum(right+1) - na.prefixSum(left)
}
```

**Complexity:** Update O(log n), Query O(log n), Space O(n)

---

### Problem 2: Count of Smaller Numbers After Self (LC 315)

> For each `nums[i]`, count how many `nums[j]` where j > i and `nums[j] < nums[i]`.

**Pattern:** Process right to left. Use Fenwick Tree as a frequency array — query prefix sum to count smaller elements.

```go
func countSmaller(nums []int) []int {
    // Coordinate compression
    sorted := make([]int, len(nums))
    copy(sorted, nums)
    sort.Ints(sorted)
    sorted = unique(sorted)
    rank := make(map[int]int)
    for i, v := range sorted {
        rank[v] = i + 1 // 1-indexed
    }

    n := len(sorted)
    ft := make([]int, n+1)
    
    update := func(i int) {
        for ; i <= n; i += i & (-i) {
            ft[i]++
        }
    }
    query := func(i int) int {
        sum := 0
        for ; i > 0; i -= i & (-i) {
            sum += ft[i]
        }
        return sum
    }

    result := make([]int, len(nums))
    for i := len(nums) - 1; i >= 0; i-- {
        r := rank[nums[i]]
        result[i] = query(r - 1) // Count elements smaller than nums[i]
        update(r)                 // Add nums[i] to the tree
    }
    return result
}

func unique(a []int) []int {
    if len(a) == 0 {
        return a
    }
    result := []int{a[0]}
    for i := 1; i < len(a); i++ {
        if a[i] != a[i-1] {
            result = append(result, a[i])
        }
    }
    return result
}
```

**Key Insight:** Coordinate compression maps values to ranks [1..m]. Fenwick Tree tracks frequency of each rank. Query prefix sum up to rank-1 = count of smaller elements.

**Complexity:** Time O(n log n), Space O(n)

---

### Problem 3: Count of Range Sum (LC 327)

> Count range sums `[i, j]` where `lower ≤ sum ≤ upper`.

**Pattern:** Merge Sort / Segment Tree on prefix sums.

Using Segment Tree approach with coordinate compression:

```go
func countRangeSum(nums []int, lower int, upper int) int {
    n := len(nums)
    prefix := make([]int64, n+1)
    for i := 0; i < n; i++ {
        prefix[i+1] = prefix[i] + int64(nums[i])
    }

    // Coordinate compress all values we'll query
    vals := make([]int64, 0)
    for _, p := range prefix {
        vals = append(vals, p, p-int64(lower), p-int64(upper))
    }
    sort.Slice(vals, func(i, j int) bool { return vals[i] < vals[j] })
    vals = uniqueInt64(vals)
    rank := make(map[int64]int)
    for i, v := range vals {
        rank[v] = i + 1
    }

    m := len(vals)
    ft := make([]int, m+1)
    update := func(i int) {
        for ; i <= m; i += i & (-i) {
            ft[i]++
        }
    }
    query := func(i int) int {
        s := 0
        for ; i > 0; i -= i & (-i) {
            s += ft[i]
        }
        return s
    }

    count := 0
    for _, p := range prefix {
        lo := rank[p-int64(upper)]
        hi := rank[p-int64(lower)]
        count += query(hi) - query(lo-1)
        update(rank[p])
    }
    return count
}

func uniqueInt64(a []int64) []int64 {
    if len(a) == 0 { return a }
    r := []int64{a[0]}
    for i := 1; i < len(a); i++ {
        if a[i] != a[i-1] { r = append(r, a[i]) }
    }
    return r
}
```

**Complexity:** Time O(n log n), Space O(n)

---

## Practice Problems

| Problem | Difficulty | Data Structure | LC # |
|---------|-----------|---------------|------|
| Range Sum Query - Mutable | Medium | Fenwick / Segment Tree | 307 |
| Count of Smaller Numbers After Self | Hard | Fenwick + Compression | 315 |
| Count of Range Sum | Hard | Fenwick / Merge Sort | 327 |
| Reverse Pairs | Hard | Fenwick / Merge Sort | 493 |
| Range Sum Query 2D - Mutable | Hard | 2D Fenwick | 308 |
| My Calendar I / II / III | Medium-Hard | Segment Tree | 729/731/732 |

---

## Hints

1. **"Range query + update"** → Segment Tree or Fenwick Tree
2. **Sum, XOR queries?** → Fenwick Tree (simpler code)
3. **Min/Max queries?** → Segment Tree (Fenwick can't do min/max natively)
4. **"Count of elements in range"** → Fenwick Tree as frequency array with coordinate compression
5. **Fenwick is 1-indexed** — always add 1 when converting from 0-indexed arrays
6. **`i & (-i)`** gives the lowest set bit — the magic behind Fenwick Tree
7. **Segment Tree uses `4n` space** — always allocate `4 * len(nums)` for the tree array

---

## Quick Reference

```
Segment Tree:
  Build:  O(n)     — build(nums, 1, 0, n-1)
  Update: O(log n) — walk down to leaf, update back
  Query:  O(log n) — merge overlapping segments
  Space:  O(4n)

Fenwick Tree:
  Build:  O(n log n) — n updates of O(log n)
  Update: O(log n) — i += i & (-i)
  Query:  O(log n) — i -= i & (-i)
  Space:  O(n)
```

---

[← Monotonic Queue](24-monotonic-queue.md) | [Index](../README.md) | [String Matching →](26-string-matching.md)
