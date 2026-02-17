# Pattern 21: Prefix Sum

## Theory

A **Prefix Sum** array stores cumulative sums so that any range sum can be computed in O(1). Given array `nums`, the prefix sum `prefix[i] = nums[0] + nums[1] + ... + nums[i-1]`.

### Key Formula
```
sum(nums[i..j]) = prefix[j+1] - prefix[i]
```

### Why It Works
Instead of summing elements in a range for each query (O(n)), we precompute all prefix sums once (O(n)), then answer each query in O(1).

### Variant: Prefix Sum + HashMap
To count subarrays with sum = k: if `prefix[j] - prefix[i] = k`, then `prefix[i] = prefix[j] - k`. Use a HashMap to count prefix sums seen so far.

---

## How to Identify This Pattern

### Use Prefix Sum when:
- ✅ "**Subarray sum** equals k"
- ✅ "**Range sum** query" (multiple queries)
- ✅ "Count subarrays with sum..."
- ✅ "**Contiguous** subarray with target sum"
- ✅ "Product of array except self" (prefix product)

---

## Template Code in Go

### Basic Prefix Sum

```go
func buildPrefixSum(nums []int) []int {
    prefix := make([]int, len(nums)+1)
    for i, num := range nums {
        prefix[i+1] = prefix[i] + num
    }
    return prefix
    // sum(nums[i..j]) = prefix[j+1] - prefix[i]
}
```

### Prefix Sum + HashMap (Count Subarrays)

```go
func subarraySumEqualsK(nums []int, k int) int {
    count := 0
    prefixSum := 0
    seen := map[int]int{0: 1} // prefix sum → frequency

    for _, num := range nums {
        prefixSum += num
        if freq, ok := seen[prefixSum-k]; ok {
            count += freq
        }
        seen[prefixSum]++
    }

    return count
}
```

---

## Solved Problems with Explanations

### Problem 1: Subarray Sum Equals K (LeetCode 560)

```go
func subarraySum(nums []int, k int) int {
    count := 0
    sum := 0
    prefixCount := map[int]int{0: 1}

    for _, num := range nums {
        sum += num
        // If (sum - k) was a prefix sum, then subarray from that point to here = k
        if freq, ok := prefixCount[sum-k]; ok {
            count += freq
        }
        prefixCount[sum]++
    }

    return count
}
// Time: O(n) | Space: O(n)
```

**Walkthrough:** `nums = [1,1,1], k = 2`
```
i=0: sum=1, need sum-k=-1 → not found. map={0:1, 1:1}
i=1: sum=2, need sum-k=0  → found 1 time! count=1. map={0:1, 1:1, 2:1}
i=2: sum=3, need sum-k=1  → found 1 time! count=2. map={0:1, 1:1, 2:1, 3:1}
Answer: 2 → subarrays [1,1] (index 0-1) and [1,1] (index 1-2)
```

### Problem 2: Range Sum Query — Immutable (LeetCode 303)

```go
type NumArray struct {
    prefix []int
}

func Constructor(nums []int) NumArray {
    prefix := make([]int, len(nums)+1)
    for i, num := range nums {
        prefix[i+1] = prefix[i] + num
    }
    return NumArray{prefix: prefix}
}

func (na *NumArray) SumRange(left int, right int) int {
    return na.prefix[right+1] - na.prefix[left]
}
// Build: O(n) | Query: O(1)
```

### Problem 3: Product of Array Except Self (LeetCode 238)

```go
func productExceptSelf(nums []int) []int {
    n := len(nums)
    result := make([]int, n)

    // Prefix product from left
    result[0] = 1
    for i := 1; i < n; i++ {
        result[i] = result[i-1] * nums[i-1]
    }

    // Suffix product from right
    rightProduct := 1
    for i := n - 1; i >= 0; i-- {
        result[i] *= rightProduct
        rightProduct *= nums[i]
    }

    return result
}
// Time: O(n) | Space: O(1) excluding output
```

### Problem 4: Continuous Subarray Sum (LeetCode 523)
**Find subarray of size ≥ 2 whose sum is multiple of k.**

```go
func checkSubarraySum(nums []int, k int) bool {
    // Key insight: if prefix[j] % k == prefix[i] % k, then
    // sum(i..j-1) is divisible by k
    remainderMap := map[int]int{0: -1}
    sum := 0

    for i, num := range nums {
        sum += num
        remainder := sum % k

        if idx, ok := remainderMap[remainder]; ok {
            if i-idx >= 2 { // Subarray length ≥ 2
                return true
            }
        } else {
            remainderMap[remainder] = i
        }
    }

    return false
}
// Time: O(n) | Space: O(min(n, k))
```

### Problem 5: 2D Prefix Sum — Range Sum Query 2D (LeetCode 304)

```go
type NumMatrix struct {
    prefix [][]int
}

func Constructor(matrix [][]int) NumMatrix {
    rows, cols := len(matrix), len(matrix[0])
    prefix := make([][]int, rows+1)
    for i := range prefix {
        prefix[i] = make([]int, cols+1)
    }

    for i := 1; i <= rows; i++ {
        for j := 1; j <= cols; j++ {
            prefix[i][j] = matrix[i-1][j-1] +
                prefix[i-1][j] + prefix[i][j-1] - prefix[i-1][j-1]
        }
    }

    return NumMatrix{prefix: prefix}
}

func (nm *NumMatrix) SumRegion(r1, c1, r2, c2 int) int {
    return nm.prefix[r2+1][c2+1] - nm.prefix[r1][c2+1] -
           nm.prefix[r2+1][c1] + nm.prefix[r1][c1]
}
// Build: O(m×n) | Query: O(1)
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Range Sum Query — Immutable | Easy | Basic prefix sum |
| 2 | Subarray Sum Equals K | Medium | Prefix sum + HashMap |
| 3 | Product of Array Except Self | Medium | Left prefix × right suffix |
| 4 | Continuous Subarray Sum | Medium | Prefix sum mod k, check remainder |
| 5 | Range Sum Query 2D | Medium | 2D prefix sum with inclusion-exclusion |
| 6 | Subarray Sums Divisible by K | Medium | Like #4 but count all |
| 7 | Find Pivot Index | Easy | leftSum == totalSum - leftSum - nums[i] |

---

## Hints

1. **Hint:** `prefix[0] = 0` — this handles the case where the subarray starts from index 0.
2. **Hint:** For "count subarrays with sum k" — HashMap of prefix sums is the key technique.
3. **Hint:** For "divisible by k" — store `prefixSum % k`. Same remainder at two positions → subarray is divisible.
4. **Hint:** 2D prefix sum uses inclusion-exclusion: add, subtract overlaps, add back double-subtracted.
5. **Hint:** Prefix products work the same way as prefix sums but with multiplication.

---

[← Graph BFS/DFS](20-graph-bfs-dfs.md) | [Back to Index](../README.md) | [Next: Greedy →](22-greedy.md)
