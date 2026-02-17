# Pattern 24: Monotonic Queue (Deque)

## Theory

A **Monotonic Queue** maintains elements in either strictly increasing or decreasing order. It uses a **deque** (double-ended queue) where we remove elements that violate the monotonic property before inserting a new element.

### Key Difference from Monotonic Stack

| Feature | Monotonic Stack | Monotonic Queue |
|---------|----------------|-----------------|
| **Data Structure** | Stack (one end) | Deque (both ends) |
| **Removes from** | Top only | Both front and back |
| **Use Case** | Next greater/smaller element | Sliding window min/max |
| **Window constraint** | No fixed window | Fixed/variable window |

### How It Works (Monotonic Decreasing Queue for Max)

```
Window: [1, 3, -1, -3, 5, 3, 6, 7], k=3

Step 1: Add 1 → deque: [1]
Step 2: Add 3 → remove 1 (3 > 1), deque: [3]
Step 3: Add -1 → deque: [3, -1], window full → max = 3
Step 4: Add -3 → deque: [3, -1, -3], max = 3
Step 5: Add 5  → remove all (5 > everyone), also 3 exits window → deque: [5], max = 5
...
```

The deque stores **indices**, not values, so we can check if the front is outside the window.

---

## How to Identify

- "Maximum/minimum in every sliding window of size k"
- "Sliding window" + "max/min" in the same problem
- Need to maintain ordered view of a window as it slides
- Subarray problems where you need running max or min efficiently
- Jump game variants with range constraints

---

## Template: Sliding Window Maximum (Decreasing Queue)

```go
func maxSlidingWindow(nums []int, k int) []int {
    deque := []int{}   // Stores indices, values are decreasing
    result := []int{}

    for i := 0; i < len(nums); i++ {
        // Remove indices outside the window
        if len(deque) > 0 && deque[0] < i-k+1 {
            deque = deque[1:]
        }

        // Remove smaller elements from back (they'll never be max)
        for len(deque) > 0 && nums[deque[len(deque)-1]] <= nums[i] {
            deque = deque[:len(deque)-1]
        }

        deque = append(deque, i)

        // Window is full, record the max (front of deque)
        if i >= k-1 {
            result = append(result, nums[deque[0]])
        }
    }
    return result
}
```

### Template: Sliding Window Minimum (Increasing Queue)

```go
func minSlidingWindow(nums []int, k int) []int {
    deque := []int{}   // Stores indices, values are increasing
    result := []int{}

    for i := 0; i < len(nums); i++ {
        // Remove indices outside the window
        if len(deque) > 0 && deque[0] < i-k+1 {
            deque = deque[1:]
        }

        // Remove LARGER elements from back (they'll never be min)
        for len(deque) > 0 && nums[deque[len(deque)-1]] >= nums[i] {
            deque = deque[:len(deque)-1]
        }

        deque = append(deque, i)

        if i >= k-1 {
            result = append(result, nums[deque[0]])
        }
    }
    return result
}
```

---

## Solved Problems

### Problem 1: Sliding Window Maximum (LC 239)

> Given an array `nums` and window size `k`, return the max in each sliding window.

**Pattern:** Monotonic decreasing queue — front always holds the current window max.

```go
func maxSlidingWindow(nums []int, k int) []int {
    deque := []int{} // Indices, values monotonically decreasing
    result := []int{}

    for i, num := range nums {
        // Remove elements outside window
        if len(deque) > 0 && deque[0] <= i-k {
            deque = deque[1:]
        }
        // Maintain decreasing order: remove smaller from back
        for len(deque) > 0 && nums[deque[len(deque)-1]] <= num {
            deque = deque[:len(deque)-1]
        }
        deque = append(deque, i)
        if i >= k-1 {
            result = append(result, nums[deque[0]])
        }
    }
    return result
}
```

**Why Monotonic Queue?** Brute force is O(nk). The deque keeps candidates in decreasing order so the max is always at the front → O(n) total.

**Complexity:** Time O(n), Space O(k)

---

### Problem 2: Shortest Subarray with Sum at Least K (LC 862)

> Return length of shortest subarray with sum ≥ k. Array may contain **negative numbers**.

**Pattern:** Prefix sum + monotonic increasing deque.

**Why not just sliding window?** Negative numbers mean shrinking the window doesn't always increase the sum — can't use standard sliding window.

```go
func shortestSubarray(nums []int, k int) int {
    n := len(nums)
    prefix := make([]int64, n+1)
    for i := 0; i < n; i++ {
        prefix[i+1] = prefix[i] + int64(nums[i])
    }

    deque := []int{} // Indices into prefix, values increasing
    result := n + 1

    for i := 0; i <= n; i++ {
        // Check if current prefix - front of deque >= k
        for len(deque) > 0 && prefix[i]-prefix[deque[0]] >= int64(k) {
            length := i - deque[0]
            if length < result {
                result = length
            }
            deque = deque[1:] // Pop front, we found a valid window
        }

        // Maintain increasing order of prefix sum
        for len(deque) > 0 && prefix[deque[len(deque)-1]] >= prefix[i] {
            deque = deque[:len(deque)-1]
        }

        deque = append(deque, i)
    }

    if result > n {
        return -1
    }
    return result
}
```

**Key Insight:** 
- Build prefix sums. Subarray sum `[j+1..i] = prefix[i] - prefix[j]`
- We want `prefix[i] - prefix[j] >= k` with minimum `i - j`
- Monotonic increasing deque on prefix sums lets us find the earliest valid `j` quickly

**Complexity:** Time O(n), Space O(n)

---

### Problem 3: Jump Game VI (LC 1696)

> Array `nums`, start at index 0. From index `i` you can jump to `[i+1, i+k]`. Score = sum of values at visited indices. Find max score reaching the last index.

**Pattern:** DP + monotonic decreasing queue to get max in window of size k.

```go
func maxResult(nums []int, k int) int {
    n := len(nums)
    dp := make([]int, n)
    dp[0] = nums[0]
    deque := []int{0} // Indices, dp values decreasing

    for i := 1; i < n; i++ {
        // Remove elements outside the window [i-k, i-1]
        for len(deque) > 0 && deque[0] < i-k {
            deque = deque[1:]
        }

        // dp[i] = nums[i] + max(dp[i-k..i-1])
        dp[i] = nums[i] + dp[deque[0]]

        // Maintain decreasing order
        for len(deque) > 0 && dp[deque[len(deque)-1]] <= dp[i] {
            deque = deque[:len(deque)-1]
        }
        deque = append(deque, i)
    }
    return dp[n-1]
}
```

**Why Monotonic Queue?** Without it, finding max in window [i-k, i-1] is O(k) per step → O(nk) total. With deque → O(n).

**Complexity:** Time O(n), Space O(n)

---

### Problem 4: Longest Continuous Subarray With Absolute Diff ≤ Limit (LC 1438)

> Find the longest subarray where the difference between max and min is ≤ limit.

**Pattern:** Two monotonic queues (one for max, one for min) + sliding window.

```go
func longestSubarray(nums []int, limit int) int {
    maxDeque := []int{} // Decreasing: front = max
    minDeque := []int{} // Increasing: front = min
    left, result := 0, 0

    for right := 0; right < len(nums); right++ {
        // Maintain max deque (decreasing)
        for len(maxDeque) > 0 && nums[maxDeque[len(maxDeque)-1]] <= nums[right] {
            maxDeque = maxDeque[:len(maxDeque)-1]
        }
        maxDeque = append(maxDeque, right)

        // Maintain min deque (increasing)
        for len(minDeque) > 0 && nums[minDeque[len(minDeque)-1]] >= nums[right] {
            minDeque = minDeque[:len(minDeque)-1]
        }
        minDeque = append(minDeque, right)

        // Shrink window if max - min > limit
        for nums[maxDeque[0]]-nums[minDeque[0]] > limit {
            left++
            if maxDeque[0] < left {
                maxDeque = maxDeque[1:]
            }
            if minDeque[0] < left {
                minDeque = minDeque[1:]
            }
        }

        if right-left+1 > result {
            result = right - left + 1
        }
    }
    return result
}
```

**Complexity:** Time O(n), Space O(n)

---

## Practice Problems

| Problem | Difficulty | Variant | LC # |
|---------|-----------|---------|------|
| Sliding Window Maximum | Hard | Classic monotonic queue | 239 |
| Shortest Subarray with Sum ≥ K | Hard | Prefix sum + deque | 862 |
| Jump Game VI | Medium | DP + deque | 1696 |
| Longest Subarray Abs Diff ≤ Limit | Medium | Two deques | 1438 |
| Constrained Subsequence Sum | Hard | DP + deque | 1425 |
| Max Value of Equation | Hard | Deque optimization | 1499 |

---

## Hints

1. **"Sliding window max/min"** → Monotonic queue, not heap (O(n) vs O(n log n))
2. Store **indices** in the deque, not values — you need to check if elements are inside the window
3. **Decreasing deque** → front = max, **Increasing deque** → front = min
4. Remove from **front** when out of window, remove from **back** when new element violates monotonicity
5. If the problem has both max AND min constraints → use **two deques**
6. **DP + sliding window max?** → Combine DP with monotonic queue for O(n) optimization

---

## Complexity

| Operation | Time |
|-----------|------|
| Each element enters deque | O(1) amortized |
| Each element leaves deque | O(1) amortized |
| Total for n elements | O(n) |
| Space | O(k) for fixed window, O(n) worst case |

---

[← Shortest Path](23-shortest-path.md) | [Index](../README.md) | [Segment Tree →](25-segment-tree.md)
