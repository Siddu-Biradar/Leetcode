# Pattern 29: Divide & Conquer

## Theory

**Divide & Conquer** solves a problem by:
1. **Divide** — Break the problem into smaller subproblems
2. **Conquer** — Solve each subproblem recursively
3. **Combine** — Merge the results into the final answer

### Key Difference from DP
| Feature | Divide & Conquer | Dynamic Programming |
|---------|-----------------|-------------------|
| Subproblems | **Independent** (no overlap) | **Overlapping** |
| Approach | Top-down recursion | Top-down memo or bottom-up table |
| Examples | Merge Sort, Quick Sort | Fibonacci, Knapsack |

### Master Theorem (for complexity analysis)

For recurrences of the form `T(n) = a × T(n/b) + O(n^d)`:

| Condition | Complexity |
|-----------|-----------|
| d > log_b(a) | O(n^d) |
| d = log_b(a) | O(n^d × log n) |
| d < log_b(a) | O(n^(log_b(a))) |

**Merge Sort:** T(n) = 2T(n/2) + O(n) → a=2, b=2, d=1 → d = log₂2 → **O(n log n)**

---

## How to Identify

- Problem can be split into **independent** halves/parts
- "Count inversions", "count smaller after self" (merge sort variant)
- "Closest pair of points"
- "Maximum subarray" (Kadane is better, but D&C works)
- "Kth largest element" (QuickSelect)
- "Median of two sorted arrays"
- Merge sort structure: split, solve, combine
- Problems on trees/recursion where subtrees are independent

---

## Template: General Divide & Conquer

```go
func divideAndConquer(data []int, lo, hi int) ResultType {
    // Base case
    if lo >= hi {
        return baseResult
    }

    // Divide
    mid := lo + (hi-lo)/2

    // Conquer
    leftResult := divideAndConquer(data, lo, mid)
    rightResult := divideAndConquer(data, mid+1, hi)

    // Combine
    return combine(leftResult, rightResult, data, lo, mid, hi)
}
```

---

## Solved Problems

### Problem 1: Merge Sort (Foundation)

> Sort an array using divide & conquer.

```go
func mergeSort(nums []int, lo, hi int) {
    if lo >= hi {
        return
    }
    mid := lo + (hi-lo)/2
    mergeSort(nums, lo, mid)
    mergeSort(nums, mid+1, hi)
    merge(nums, lo, mid, hi)
}

func merge(nums []int, lo, mid, hi int) {
    temp := make([]int, hi-lo+1)
    i, j, k := lo, mid+1, 0

    for i <= mid && j <= hi {
        if nums[i] <= nums[j] {
            temp[k] = nums[i]
            i++
        } else {
            temp[k] = nums[j]
            j++
        }
        k++
    }
    for i <= mid {
        temp[k] = nums[i]
        i++
        k++
    }
    for j <= hi {
        temp[k] = nums[j]
        j++
        k++
    }
    copy(nums[lo:hi+1], temp)
}
```

**Complexity:** Time O(n log n), Space O(n)

---

### Problem 2: Count of Smaller Numbers After Self (LC 315)

> For each `nums[i]`, count how many `nums[j]` where j > i and `nums[j] < nums[i]`.

**Pattern:** Merge sort — during the merge step, count how many elements from the right half are smaller than elements from the left half.

```go
func countSmaller(nums []int) []int {
    n := len(nums)
    result := make([]int, n)

    // Pair: (value, original index)
    indices := make([]int, n)
    for i := range indices {
        indices[i] = i
    }

    mergeSortCount(nums, indices, result, 0, n-1)
    return result
}

func mergeSortCount(nums, indices []int, result []int, lo, hi int) {
    if lo >= hi {
        return
    }
    mid := lo + (hi-lo)/2
    mergeSortCount(nums, indices, result, lo, mid)
    mergeSortCount(nums, indices, result, mid+1, hi)
    mergeCount(nums, indices, result, lo, mid, hi)
}

func mergeCount(nums, indices []int, result []int, lo, mid, hi int) {
    temp := make([]int, hi-lo+1)
    i, j, k := lo, mid+1, 0
    rightCount := 0 // Elements from right half that moved left

    for i <= mid && j <= hi {
        if nums[indices[j]] < nums[indices[i]] {
            rightCount++
            temp[k] = indices[j]
            j++
        } else {
            result[indices[i]] += rightCount
            temp[k] = indices[i]
            i++
        }
        k++
    }
    for i <= mid {
        result[indices[i]] += rightCount
        temp[k] = indices[i]
        i++
        k++
    }
    for j <= hi {
        temp[k] = indices[j]
        j++
        k++
    }
    copy(indices[lo:hi+1], temp)
}
```

**Key Insight:** During merge, when we pick an element from the right half before a left element, ALL remaining left elements have that as a "smaller after self".

**Complexity:** Time O(n log n), Space O(n)

---

### Problem 3: Reverse Pairs (LC 493)

> Count pairs (i, j) where i < j and nums[i] > 2 * nums[j].

**Pattern:** Merge sort — count cross-inversions before merging.

```go
func reversePairs(nums []int) int {
    return mergeSortReverse(nums, 0, len(nums)-1)
}

func mergeSortReverse(nums []int, lo, hi int) int {
    if lo >= hi {
        return 0
    }
    mid := lo + (hi-lo)/2
    count := mergeSortReverse(nums, lo, mid) + mergeSortReverse(nums, mid+1, hi)

    // Count cross-pairs BEFORE merging
    j := mid + 1
    for i := lo; i <= mid; i++ {
        for j <= hi && int64(nums[i]) > 2*int64(nums[j]) {
            j++
        }
        count += j - (mid + 1)
    }

    // Standard merge
    mergeStandard(nums, lo, mid, hi)
    return count
}

func mergeStandard(nums []int, lo, mid, hi int) {
    temp := make([]int, hi-lo+1)
    i, j, k := lo, mid+1, 0
    for i <= mid && j <= hi {
        if nums[i] <= nums[j] {
            temp[k] = nums[i]; i++
        } else {
            temp[k] = nums[j]; j++
        }
        k++
    }
    for i <= mid { temp[k] = nums[i]; i++; k++ }
    for j <= hi  { temp[k] = nums[j]; j++; k++ }
    copy(nums[lo:hi+1], temp)
}
```

**Complexity:** Time O(n log n), Space O(n)

---

### Problem 4: Median of Two Sorted Arrays (LC 4)

> Find the median of two sorted arrays in O(log(min(m,n))).

**Pattern:** Binary search (divide & conquer on partition position).

```go
func findMedianSortedArrays(nums1 []int, nums2 []int) float64 {
    // Ensure nums1 is the shorter array
    if len(nums1) > len(nums2) {
        return findMedianSortedArrays(nums2, nums1)
    }

    m, n := len(nums1), len(nums2)
    lo, hi := 0, m
    halfLen := (m + n + 1) / 2

    for lo <= hi {
        i := (lo + hi) / 2     // Partition in nums1
        j := halfLen - i        // Partition in nums2

        // Check if partition is correct
        leftMax1 := math.MinInt64
        if i > 0 { leftMax1 = nums1[i-1] }
        rightMin1 := math.MaxInt64
        if i < m { rightMin1 = nums1[i] }
        leftMax2 := math.MinInt64
        if j > 0 { leftMax2 = nums2[j-1] }
        rightMin2 := math.MaxInt64
        if j < n { rightMin2 = nums2[j] }

        if leftMax1 <= rightMin2 && leftMax2 <= rightMin1 {
            // Found correct partition
            if (m+n)%2 == 1 {
                return float64(max(leftMax1, leftMax2))
            }
            return float64(max(leftMax1, leftMax2)+min(rightMin1, rightMin2)) / 2.0
        } else if leftMax1 > rightMin2 {
            hi = i - 1
        } else {
            lo = i + 1
        }
    }
    return 0.0
}
```

**Key Insight:** Partition both arrays such that left halves have exactly `halfLen` elements and all left elements ≤ all right elements.

**Complexity:** Time O(log(min(m, n))), Space O(1)

---

### Problem 5: Maximum Subarray (LC 53) — D&C Approach

> Find the contiguous subarray with the largest sum.

**Pattern:** D&C — max subarray is either entirely in left half, entirely in right half, or crosses the midpoint.

```go
func maxSubArray(nums []int) int {
    return maxSubDC(nums, 0, len(nums)-1)
}

func maxSubDC(nums []int, lo, hi int) int {
    if lo == hi {
        return nums[lo]
    }

    mid := lo + (hi-lo)/2
    leftMax := maxSubDC(nums, lo, mid)
    rightMax := maxSubDC(nums, mid+1, hi)
    crossMax := maxCrossing(nums, lo, mid, hi)

    return max(leftMax, max(rightMax, crossMax))
}

func maxCrossing(nums []int, lo, mid, hi int) int {
    leftSum, best := 0, math.MinInt64
    for i := mid; i >= lo; i-- {
        leftSum += nums[i]
        if leftSum > best {
            best = leftSum
        }
    }
    leftBest := best

    rightSum, best := 0, math.MinInt64
    for i := mid + 1; i <= hi; i++ {
        rightSum += nums[i]
        if rightSum > best {
            best = rightSum
        }
    }
    return leftBest + best
}
```

> **Note:** Kadane's algorithm is O(n) and preferred. This D&C solution is O(n log n) but demonstrates the pattern.

**Complexity:** Time O(n log n), Space O(log n) recursion stack

---

### Problem 6: Kth Largest Element — QuickSelect (LC 215)

> Find the kth largest element (without full sorting).

**Pattern:** QuickSelect — partition-based D&C, average O(n).

```go
func findKthLargest(nums []int, k int) int {
    // kth largest = (n-k)th smallest (0-indexed)
    target := len(nums) - k
    return quickSelect(nums, 0, len(nums)-1, target)
}

func quickSelect(nums []int, lo, hi, k int) int {
    if lo == hi {
        return nums[lo]
    }

    pivotIdx := partition(nums, lo, hi)

    if pivotIdx == k {
        return nums[pivotIdx]
    } else if pivotIdx < k {
        return quickSelect(nums, pivotIdx+1, hi, k)
    } else {
        return quickSelect(nums, lo, pivotIdx-1, k)
    }
}

func partition(nums []int, lo, hi int) int {
    // Random pivot for better average case
    rand := lo + rand.Intn(hi-lo+1)
    nums[rand], nums[hi] = nums[hi], nums[rand]

    pivot := nums[hi]
    i := lo
    for j := lo; j < hi; j++ {
        if nums[j] < pivot {
            nums[i], nums[j] = nums[j], nums[i]
            i++
        }
    }
    nums[i], nums[hi] = nums[hi], nums[i]
    return i
}
```

**Complexity:** Time O(n) average, O(n²) worst case. Space O(1).

---

## Practice Problems

| Problem | Difficulty | Variant | LC # |
|---------|-----------|---------|------|
| Merge Sort | — | Foundation | — |
| Count Smaller After Self | Hard | Merge sort + count | 315 |
| Reverse Pairs | Hard | Merge sort + count | 493 |
| Median of Two Sorted Arrays | Hard | Binary partition | 4 |
| Maximum Subarray (D&C) | Medium | Cross-midpoint | 53 |
| Kth Largest Element | Medium | QuickSelect | 215 |
| Sort an Array | Medium | Merge/Quick sort | 912 |
| Count of Range Sum | Hard | Merge sort | 327 |
| Closest Pair of Points | Hard | Geometric D&C | — |
| Different Ways to Add Parentheses | Medium | Expression D&C | 241 |

---

## Hints

1. **"Count inversions" or "count pairs across halves"** → Merge Sort D&C
2. **D&C works when subproblems are independent** — if they share state, use DP instead
3. **QuickSelect** is the D&C way to find kth element in O(n) average — always use random pivot
4. **Merge sort D&C** problems count things during the merge step — the key insight is that both halves are sorted
5. **The "combine" step is where the magic happens** — that's where you solve what neither half could alone
6. **Recursion depth is O(log n)** for balanced splits — watch out for stack overflow with unbalanced partitions
7. **Median of two sorted arrays** is the hardest standard D&C problem — practice it multiple times

---

## Complexity Summary

| Problem Type | Time | Space |
|-------------|------|-------|
| Merge Sort | O(n log n) | O(n) |
| QuickSelect | O(n) avg | O(1) |
| Quick Sort | O(n log n) avg | O(log n) |
| Count inversions | O(n log n) | O(n) |
| Closest pair | O(n log n) | O(n) |

---

[← Math & Number Theory](28-math-number-theory.md) | [Index](../README.md)
