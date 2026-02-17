# Pattern 09: Two Heaps

## Theory

The **Two Heaps** pattern uses a **Max-Heap** and a **Min-Heap** together to efficiently track the **median** of a dynamic data stream or to **balance** two halves of data.

### Core Idea
- **Max-Heap** holds the **smaller half** of numbers (top = largest of small half)
- **Min-Heap** holds the **larger half** of numbers (top = smallest of large half)
- Median = top of max-heap (odd count) or average of both tops (even count)

### Balancing Rule
- Both heaps should differ in size by at most 1
- Max-heap can have at most 1 extra element

Go doesn't have built-in heaps with max-heap, so we use `container/heap` with a custom implementation.

---

## How to Identify This Pattern

### Use Two Heaps when:
- ✅ "Find the **median** of a data stream"
- ✅ "Sliding window **median**"
- ✅ Need to efficiently track the **middle** element(s)
- ✅ Problems that need to **partition** into two balanced halves

---

## Go Heap Implementation

```go
import "container/heap"

// MaxHeap
type MaxHeap []int
func (h MaxHeap) Len() int            { return len(h) }
func (h MaxHeap) Less(i, j int) bool  { return h[i] > h[j] } // Max
func (h MaxHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }
func (h *MaxHeap) Push(x interface{}) { *h = append(*h, x.(int)) }
func (h *MaxHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

// MinHeap
type MinHeap []int
func (h MinHeap) Len() int            { return len(h) }
func (h MinHeap) Less(i, j int) bool  { return h[i] < h[j] } // Min
func (h MinHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }
func (h *MinHeap) Push(x interface{}) { *h = append(*h, x.(int)) }
func (h *MinHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}
```

---

## Solved Problems with Explanations

### Problem 1: Find Median from Data Stream (LeetCode 295)

```go
type MedianFinder struct {
    maxHeap *MaxHeap // Smaller half
    minHeap *MinHeap // Larger half
}

func Constructor() MedianFinder {
    lo := &MaxHeap{}
    hi := &MinHeap{}
    heap.Init(lo)
    heap.Init(hi)
    return MedianFinder{maxHeap: lo, minHeap: hi}
}

func (mf *MedianFinder) AddNum(num int) {
    // Step 1: Add to max-heap (smaller half)
    heap.Push(mf.maxHeap, num)

    // Step 2: Ensure max-heap's top <= min-heap's top
    if mf.maxHeap.Len() > 0 && mf.minHeap.Len() > 0 &&
        (*mf.maxHeap)[0] > (*mf.minHeap)[0] {
        val := heap.Pop(mf.maxHeap).(int)
        heap.Push(mf.minHeap, val)
    }

    // Step 3: Balance sizes (max-heap can have at most 1 extra)
    if mf.maxHeap.Len() > mf.minHeap.Len()+1 {
        val := heap.Pop(mf.maxHeap).(int)
        heap.Push(mf.minHeap, val)
    } else if mf.minHeap.Len() > mf.maxHeap.Len() {
        val := heap.Pop(mf.minHeap).(int)
        heap.Push(mf.maxHeap, val)
    }
}

func (mf *MedianFinder) FindMedian() float64 {
    if mf.maxHeap.Len() > mf.minHeap.Len() {
        return float64((*mf.maxHeap)[0])
    }
    return (float64((*mf.maxHeap)[0]) + float64((*mf.minHeap)[0])) / 2.0
}
// AddNum: O(log n) | FindMedian: O(1) | Space: O(n)
```

**Walkthrough:**
```
Add 3: maxHeap=[3], minHeap=[]       → median=3
Add 1: maxHeap=[3], add 1 → [3,1], balance: maxHeap=[1], minHeap=[3]  → median=(1+3)/2=2
Add 5: maxHeap=[1], add 5 → [5,1], 5>3 → move: maxHeap=[1], minHeap=[3,5] → balance: maxH=[3,1] minH=[5] → median=3
Add 4: maxH=[3,1], add 4 → [4,3,1], 4>3 nope wait... 4>minH top? Actually:
       Add to maxH: [4,3,1], maxH top=4 > minH top=5? No. But maxH.Len(3) > minH.Len(1)+1 → move 4 to minH
       maxH=[3,1], minH=[4,5] → median=(3+4)/2=3.5
```

### Problem 2: Sliding Window Median (LeetCode 480)

```go
// This problem is trickier because we also need to REMOVE elements.
// Using sorted slice approach for clarity:

func medianSlidingWindow(nums []int, k int) []float64 {
    result := []float64{}
    window := make([]int, 0, k)

    // Helper: insert into sorted slice
    insertSorted := func(val int) {
        idx := sort.SearchInts(window, val)
        window = append(window, 0)
        copy(window[idx+1:], window[idx:])
        window[idx] = val
    }

    // Helper: remove from sorted slice
    removeSorted := func(val int) {
        idx := sort.SearchInts(window, val)
        window = append(window[:idx], window[idx+1:]...)
    }

    getMedian := func() float64 {
        if k%2 == 1 {
            return float64(window[k/2])
        }
        return (float64(window[k/2-1]) + float64(window[k/2])) / 2.0
    }

    for i := 0; i < len(nums); i++ {
        insertSorted(nums[i])

        if i >= k {
            removeSorted(nums[i-k])
        }

        if i >= k-1 {
            result = append(result, getMedian())
        }
    }

    return result
}
// Time: O(n*k) due to insertion in sorted slice | Space: O(k)
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Find Median from Data Stream | Hard | MaxHeap (small half) + MinHeap (large half) |
| 2 | Sliding Window Median | Hard | Two heaps + lazy deletion |
| 3 | IPO (Maximize Capital) | Hard | MinHeap by cost, MaxHeap by profit |

---

## Hints

1. **Hint:** In Go, `container/heap` is an interface — you must implement `Len`, `Less`, `Swap`, `Push`, `Pop`.
2. **Hint:** For max-heap in Go, reverse the `Less` comparison: `return h[i] > h[j]`.
3. **Hint:** Always balance after insertion: neither heap should be more than 1 element larger.
4. **Hint:** Peek at heap top without popping: `(*myHeap)[0]`.
5. **Hint:** The median insight: if we split numbers into two equal halves, the median is at the boundary.

---

[← Tree DFS](08-tree-dfs.md) | [Back to Index](../README.md) | [Next: Subsets / Backtracking →](10-subsets-backtracking.md)
