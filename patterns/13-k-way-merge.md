# Pattern 13: K-way Merge

## Theory

The **K-way Merge** pattern merges K sorted lists using a **Min-Heap**. Instead of merging two at a time (which would be O(n·k)), we use a heap to always pick the smallest among the current heads of all K lists.

### How It Works
1. Push the first element from each list into a min-heap
2. Pop the smallest element from the heap → add to result
3. Push the next element from the same list
4. Repeat until the heap is empty

---

## How to Identify This Pattern

### Use K-way Merge when:
- ✅ "Merge K sorted lists/arrays"
- ✅ "K-th smallest in sorted matrix"
- ✅ "Smallest range covering elements from K lists"

---

## Solved Problems with Explanations

### Problem 1: Merge K Sorted Lists (LeetCode 23)

```go
type ListNode struct {
    Val  int
    Next *ListNode
}

type NodeHeap []*ListNode
func (h NodeHeap) Len() int           { return len(h) }
func (h NodeHeap) Less(i, j int) bool { return h[i].Val < h[j].Val }
func (h NodeHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *NodeHeap) Push(x interface{}) { *h = append(*h, x.(*ListNode)) }
func (h *NodeHeap) Pop() interface{} {
    old := *h; n := len(old); x := old[n-1]; *h = old[:n-1]; return x
}

func mergeKLists(lists []*ListNode) *ListNode {
    h := &NodeHeap{}
    heap.Init(h)

    // Push first node of each list
    for _, list := range lists {
        if list != nil {
            heap.Push(h, list)
        }
    }

    dummy := &ListNode{}
    curr := dummy

    for h.Len() > 0 {
        smallest := heap.Pop(h).(*ListNode)
        curr.Next = smallest
        curr = curr.Next

        if smallest.Next != nil {
            heap.Push(h, smallest.Next)
        }
    }

    return dummy.Next
}
// Time: O(N log K) where N = total nodes | Space: O(K)
```

### Problem 2: Kth Smallest Element in a Sorted Matrix (LeetCode 378)

```go
type Element struct {
    val, row, col int
}

type ElemHeap []Element
func (h ElemHeap) Len() int           { return len(h) }
func (h ElemHeap) Less(i, j int) bool { return h[i].val < h[j].val }
func (h ElemHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *ElemHeap) Push(x interface{}) { *h = append(*h, x.(Element)) }
func (h *ElemHeap) Pop() interface{} {
    old := *h; n := len(old); x := old[n-1]; *h = old[:n-1]; return x
}

func kthSmallest(matrix [][]int, k int) int {
    n := len(matrix)
    h := &ElemHeap{}
    heap.Init(h)

    // Push first element of each row
    for i := 0; i < n && i < k; i++ {
        heap.Push(h, Element{matrix[i][0], i, 0})
    }

    result := 0
    for i := 0; i < k; i++ {
        elem := heap.Pop(h).(Element)
        result = elem.val

        if elem.col+1 < n {
            heap.Push(h, Element{matrix[elem.row][elem.col+1], elem.row, elem.col + 1})
        }
    }

    return result
}
// Time: O(K log n) | Space: O(n)
```

### Problem 3: Find K Pairs with Smallest Sums (LeetCode 373)

```go
type Pair struct {
    sum, i, j int
}

type PairHeap []Pair
func (h PairHeap) Len() int           { return len(h) }
func (h PairHeap) Less(a, b int) bool { return h[a].sum < h[b].sum }
func (h PairHeap) Swap(a, b int)      { h[a], h[b] = h[b], h[a] }
func (h *PairHeap) Push(x interface{}) { *h = append(*h, x.(Pair)) }
func (h *PairHeap) Pop() interface{} {
    old := *h; n := len(old); x := old[n-1]; *h = old[:n-1]; return x
}

func kSmallestPairs(nums1 []int, nums2 []int, k int) [][]int {
    h := &PairHeap{}
    heap.Init(h)

    // Push (nums1[i], nums2[0]) for all i
    for i := 0; i < len(nums1) && i < k; i++ {
        heap.Push(h, Pair{nums1[i] + nums2[0], i, 0})
    }

    result := [][]int{}
    for h.Len() > 0 && len(result) < k {
        p := heap.Pop(h).(Pair)
        result = append(result, []int{nums1[p.i], nums2[p.j]})

        if p.j+1 < len(nums2) {
            heap.Push(h, Pair{nums1[p.i] + nums2[p.j+1], p.i, p.j + 1})
        }
    }

    return result
}
// Time: O(K log K) | Space: O(K)
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Merge K Sorted Lists | Hard | Min-heap of list heads |
| 2 | Kth Smallest in Sorted Matrix | Medium | K-way merge rows, or binary search |
| 3 | K Pairs with Smallest Sums | Medium | Expand only `j+1` from popped pair |
| 4 | Smallest Range Covering K Lists | Hard | Min-heap + track global max |
| 5 | Merge Sorted Arrays | Easy | Two pointers (K=2 special case) |

---

## Hints

1. **Hint:** Always push the FIRST element from each list to the heap initially.
2. **Hint:** When popping, push the NEXT element from the SAME list.
3. **Hint:** Heap size stays at most K — this is what gives us O(N log K).
4. **Hint:** For matrix problems, each row is a "sorted list" — apply K-way merge treating rows as lists.

---

[← Top K Elements](12-top-k-elements.md) | [Back to Index](../README.md) | [Next: Topological Sort →](14-topological-sort.md)
