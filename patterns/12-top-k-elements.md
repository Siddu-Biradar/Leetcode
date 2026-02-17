# Pattern 12: Top K Elements

## Theory

The **Top K Elements** pattern uses a **Heap** (priority queue) to efficiently find the k-th largest/smallest elements or the k most frequent elements. Instead of sorting the entire dataset (O(n log n)), we maintain a heap of size k (O(n log k)).

### Strategy
| Need | Heap Type | Why |
|------|----------|-----|
| K largest elements | Min-Heap of size K | Min-Heap top = smallest of the K largest. If new element > top, pop and push. |
| K smallest elements | Max-Heap of size K | Max-Heap top = largest of the K smallest. |
| K-th largest | Min-Heap of size K | Top of heap = k-th largest |

---

## How to Identify This Pattern

### Use Top K Elements when:
- ✅ "K-th largest/smallest element"
- ✅ "Top K frequent elements"
- ✅ "K closest points"
- ✅ "Sort characters by frequency"
- ✅ Whenever you see **"K"** combined with **"largest/smallest/frequent/closest"**

---

## Go Heap Implementation

```go
import "container/heap"

// Generic heap using a slice
type IntHeap []int
func (h IntHeap) Len() int           { return len(h) }
func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] } // Min-Heap
func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *IntHeap) Push(x interface{}) { *h = append(*h, x.(int)) }
func (h *IntHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}
```

---

## Solved Problems with Explanations

### Problem 1: Kth Largest Element in an Array (LeetCode 215)

```go
func findKthLargest(nums []int, k int) int {
    h := &IntHeap{}
    heap.Init(h)

    for _, num := range nums {
        heap.Push(h, num)
        if h.Len() > k {
            heap.Pop(h) // Remove smallest, keep K largest
        }
    }

    return (*h)[0] // Top of min-heap = K-th largest
}
// Time: O(n log k) | Space: O(k)
```

**Alternative: QuickSelect (O(n) average)**
```go
func findKthLargest(nums []int, k int) int {
    target := len(nums) - k // k-th largest = (n-k)-th smallest (0-indexed)
    return quickSelect(nums, 0, len(nums)-1, target)
}

func quickSelect(nums []int, lo, hi, target int) int {
    pivot := nums[hi]
    left := lo

    for i := lo; i < hi; i++ {
        if nums[i] <= pivot {
            nums[i], nums[left] = nums[left], nums[i]
            left++
        }
    }
    nums[left], nums[hi] = nums[hi], nums[left]

    if left == target {
        return nums[left]
    } else if left < target {
        return quickSelect(nums, left+1, hi, target)
    }
    return quickSelect(nums, lo, left-1, target)
}
// Time: O(n) average, O(n²) worst | Space: O(1)
```

### Problem 2: Top K Frequent Elements (LeetCode 347)

```go
type FreqItem struct {
    val  int
    freq int
}

type FreqHeap []FreqItem
func (h FreqHeap) Len() int           { return len(h) }
func (h FreqHeap) Less(i, j int) bool { return h[i].freq < h[j].freq } // Min by freq
func (h FreqHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *FreqHeap) Push(x interface{}) { *h = append(*h, x.(FreqItem)) }
func (h *FreqHeap) Pop() interface{} {
    old := *h; n := len(old); x := old[n-1]; *h = old[:n-1]; return x
}

func topKFrequent(nums []int, k int) []int {
    // Count frequencies
    freq := make(map[int]int)
    for _, n := range nums {
        freq[n]++
    }

    // Use min-heap of size k
    h := &FreqHeap{}
    heap.Init(h)

    for val, count := range freq {
        heap.Push(h, FreqItem{val, count})
        if h.Len() > k {
            heap.Pop(h)
        }
    }

    // Extract results
    result := make([]int, k)
    for i := k - 1; i >= 0; i-- {
        result[i] = heap.Pop(h).(FreqItem).val
    }
    return result
}
// Time: O(n log k) | Space: O(n)
```

**Alternative: Bucket Sort (O(n))**
```go
func topKFrequent(nums []int, k int) []int {
    freq := make(map[int]int)
    for _, n := range nums {
        freq[n]++
    }

    // Bucket: index = frequency, value = list of numbers with that frequency
    buckets := make([][]int, len(nums)+1)
    for val, count := range freq {
        buckets[count] = append(buckets[count], val)
    }

    result := []int{}
    for i := len(buckets) - 1; i >= 0 && len(result) < k; i-- {
        result = append(result, buckets[i]...)
    }
    return result[:k]
}
// Time: O(n) | Space: O(n)
```

### Problem 3: K Closest Points to Origin (LeetCode 973)

```go
type Point struct {
    x, y int
    dist int // distance squared (avoid sqrt)
}

type MaxDistHeap []Point
func (h MaxDistHeap) Len() int           { return len(h) }
func (h MaxDistHeap) Less(i, j int) bool { return h[i].dist > h[j].dist } // MAX heap
func (h MaxDistHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *MaxDistHeap) Push(x interface{}) { *h = append(*h, x.(Point)) }
func (h *MaxDistHeap) Pop() interface{} {
    old := *h; n := len(old); x := old[n-1]; *h = old[:n-1]; return x
}

func kClosest(points [][]int, k int) [][]int {
    h := &MaxDistHeap{}
    heap.Init(h)

    for _, p := range points {
        dist := p[0]*p[0] + p[1]*p[1]
        heap.Push(h, Point{p[0], p[1], dist})
        if h.Len() > k {
            heap.Pop(h) // Remove farthest
        }
    }

    result := make([][]int, k)
    for i := 0; i < k; i++ {
        p := heap.Pop(h).(Point)
        result[i] = []int{p.x, p.y}
    }
    return result
}
// Time: O(n log k) | Space: O(k)
```

### Problem 4: Sort Characters By Frequency (LeetCode 451)

```go
func frequencySort(s string) string {
    freq := make(map[byte]int)
    for i := 0; i < len(s); i++ {
        freq[s[i]]++
    }

    // Use bucket sort
    buckets := make([][]byte, len(s)+1)
    for ch, count := range freq {
        buckets[count] = append(buckets[count], ch)
    }

    result := make([]byte, 0, len(s))
    for i := len(buckets) - 1; i >= 0; i-- {
        for _, ch := range buckets[i] {
            for j := 0; j < i; j++ {
                result = append(result, ch)
            }
        }
    }
    return string(result)
}
// Time: O(n) | Space: O(n)
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Kth Largest Element | Medium | Min-heap of size k |
| 2 | Top K Frequent Elements | Medium | Count freq + heap or bucket sort |
| 3 | K Closest Points to Origin | Medium | Max-heap by distance, size k |
| 4 | Sort Characters by Frequency | Medium | Freq count + bucket sort |
| 5 | Kth Largest in Stream | Easy | Maintain min-heap of size k |
| 6 | Reorganize String | Medium | Max-heap by frequency, alternate chars |
| 7 | Task Scheduler | Medium | Max-heap + cooldown tracking |

---

## Hints

1. **Hint:** For "K largest" → use MIN-heap (counterintuitive!). The top is the smallest of the K largest.
2. **Hint:** For "K closest/smallest" → use MAX-heap. If new element is closer than the farthest in heap, swap.
3. **Hint:** Bucket sort can achieve O(n) for frequency-based problems — no heap needed.
4. **Hint:** QuickSelect gives O(n) average for k-th element but O(n²) worst. Heap gives O(n log k) guaranteed.
5. **Hint:** In Go, compare squared distances to avoid floating-point with `math.Sqrt`.

---

[← Binary Search](11-binary-search.md) | [Back to Index](../README.md) | [Next: K-way Merge →](13-k-way-merge.md)
