# Pattern 15: Dynamic Programming (DP)

## Theory

**Dynamic Programming** solves problems by breaking them into **overlapping subproblems** and storing results to avoid redundant computation. 

### Two Properties That Signal DP
1. **Optimal Substructure** — Optimal solution is built from optimal solutions of subproblems
2. **Overlapping Subproblems** — Same subproblems are solved multiple times

### Two Approaches
| Approach | Direction | Method |
|----------|-----------|--------|
| **Top-Down (Memoization)** | Start from problem → break down | Recursion + cache |
| **Bottom-Up (Tabulation)** | Start from base cases → build up | Iterative + table |

### DP Sub-Patterns

| Sub-Pattern | Examples |
|------------|---------|
| **0/1 Knapsack** | Subset sum, partition equal subset, target sum |
| **Unbounded Knapsack** | Coin change, rod cutting |
| **Fibonacci** | Climbing stairs, house robber |
| **LCS (Longest Common Subsequence)** | Edit distance, shortest common supersequence |
| **LIS (Longest Increasing Subsequence)** | Patience sorting |
| **Matrix Chain / Grid** | Unique paths, minimum path sum |
| **String DP** | Palindrome partitioning, regex matching |
| **Interval DP** | Burst balloons, matrix multiplication |
| **State Machine DP** | Stock buy/sell, state transitions |

---

## How to Identify DP

### Use DP when:
- ✅ "Minimum/maximum cost/profit/count"
- ✅ "Number of ways to..."
- ✅ "Can we achieve...?" (feasibility)
- ✅ "Longest/shortest subsequence"
- ✅ Choices at each step + optimization
- ✅ Brute force leads to exponential time with repeated work

### DP vs Greedy
- **DP:** Need to consider ALL choices → overlapping subproblems
- **Greedy:** LOCAL best choice → GLOBAL best (no need to backtrack)

---

## The DP Framework (5 Steps)

```
1. DEFINE state: What does dp[i] (or dp[i][j]) represent?
2. IDENTIFY transitions: How does dp[i] relate to smaller subproblems?
3. BASE CASE: What are dp[0], dp[1], etc.?
4. ITERATION ORDER: Fill table so dependencies are computed first
5. ANSWER: Which dp entry gives the final answer?
```

---

## Sub-Pattern 1: Fibonacci / Linear DP

### Problem: Climbing Stairs (LeetCode 70)

```go
func climbStairs(n int) int {
    if n <= 2 {
        return n
    }
    prev2, prev1 := 1, 2 // dp[1]=1, dp[2]=2

    for i := 3; i <= n; i++ {
        curr := prev1 + prev2
        prev2 = prev1
        prev1 = curr
    }

    return prev1
}
// Time: O(n) | Space: O(1)
```

### Problem: House Robber (LeetCode 198)

```go
func rob(nums []int) int {
    if len(nums) == 0 { return 0 }
    if len(nums) == 1 { return nums[0] }

    prev2, prev1 := nums[0], max(nums[0], nums[1])

    for i := 2; i < len(nums); i++ {
        curr := max(prev1, prev2+nums[i])
        prev2 = prev1
        prev1 = curr
    }

    return prev1
}
// dp[i] = max money robbing houses 0..i
// dp[i] = max(dp[i-1], dp[i-2] + nums[i])
// Time: O(n) | Space: O(1)
```

### Problem: House Robber II — Circular (LeetCode 213)

```go
func rob(nums []int) int {
    if len(nums) == 1 { return nums[0] }
    // Case 1: Rob houses 0..n-2 (skip last)
    // Case 2: Rob houses 1..n-1 (skip first)
    return max(robRange(nums, 0, len(nums)-2), robRange(nums, 1, len(nums)-1))
}

func robRange(nums []int, lo, hi int) int {
    prev2, prev1 := 0, 0
    for i := lo; i <= hi; i++ {
        curr := max(prev1, prev2+nums[i])
        prev2 = prev1
        prev1 = curr
    }
    return prev1
}
```

---

## Sub-Pattern 2: 0/1 Knapsack

### Theory
Given items with weights and values, maximize value within capacity. Each item used at most once.

```
dp[i][w] = max value using first i items with capacity w
dp[i][w] = max(dp[i-1][w],           // don't take item i
               dp[i-1][w-wt[i]] + val[i]) // take item i
```

### Problem: Partition Equal Subset Sum (LeetCode 416)

```go
func canPartition(nums []int) bool {
    total := 0
    for _, n := range nums {
        total += n
    }
    if total%2 != 0 {
        return false
    }

    target := total / 2
    dp := make([]bool, target+1)
    dp[0] = true

    for _, num := range nums {
        // Traverse RIGHT to LEFT to avoid using same item twice
        for j := target; j >= num; j-- {
            dp[j] = dp[j] || dp[j-num]
        }
    }

    return dp[target]
}
// Time: O(n × target) | Space: O(target)
```

### Problem: Target Sum (LeetCode 494)

```go
func findTargetSumWays(nums []int, target int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    
    // sum(P) - sum(N) = target, sum(P) + sum(N) = total
    // sum(P) = (target + total) / 2
    if (target+total)%2 != 0 || target+total < 0 {
        return 0
    }
    
    subsetSum := (target + total) / 2
    dp := make([]int, subsetSum+1)
    dp[0] = 1

    for _, num := range nums {
        for j := subsetSum; j >= num; j-- {
            dp[j] += dp[j-num]
        }
    }

    return dp[subsetSum]
}
```

---

## Sub-Pattern 3: Unbounded Knapsack

### Problem: Coin Change (LeetCode 322)

```go
func coinChange(coins []int, amount int) int {
    dp := make([]int, amount+1)
    for i := range dp {
        dp[i] = amount + 1 // "infinity"
    }
    dp[0] = 0

    for i := 1; i <= amount; i++ {
        for _, coin := range coins {
            if coin <= i && dp[i-coin]+1 < dp[i] {
                dp[i] = dp[i-coin] + 1
            }
        }
    }

    if dp[amount] > amount {
        return -1
    }
    return dp[amount]
}
// dp[i] = min coins to make amount i
// Time: O(amount × n) | Space: O(amount)
```

### Problem: Coin Change 2 — Number of Ways (LeetCode 518)

```go
func change(amount int, coins []int) int {
    dp := make([]int, amount+1)
    dp[0] = 1

    // Loop coins FIRST (outer) to avoid counting permutations
    for _, coin := range coins {
        for i := coin; i <= amount; i++ {
            dp[i] += dp[i-coin]
        }
    }

    return dp[amount]
}
// Time: O(amount × n) | Space: O(amount)
```

**Key Difference:** In Coin Change 2, coins are the OUTER loop. This ensures each combination is counted once (not permutations). In Coin Change, the order doesn't matter because we take minimum.

---

## Sub-Pattern 4: Longest Common Subsequence (LCS)

### Problem: LCS (LeetCode 1143)

```go
func longestCommonSubsequence(text1 string, text2 string) int {
    m, n := len(text1), len(text2)
    dp := make([][]int, m+1)
    for i := range dp {
        dp[i] = make([]int, n+1)
    }

    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if text1[i-1] == text2[j-1] {
                dp[i][j] = dp[i-1][j-1] + 1
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
            }
        }
    }

    return dp[m][n]
}
// Time: O(m×n) | Space: O(m×n), can optimize to O(n)
```

### Problem: Edit Distance (LeetCode 72)

```go
func minDistance(word1 string, word2 string) int {
    m, n := len(word1), len(word2)
    dp := make([][]int, m+1)
    for i := range dp {
        dp[i] = make([]int, n+1)
        dp[i][0] = i // Delete all chars from word1
    }
    for j := 0; j <= n; j++ {
        dp[0][j] = j // Insert all chars of word2
    }

    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if word1[i-1] == word2[j-1] {
                dp[i][j] = dp[i-1][j-1] // No operation needed
            } else {
                dp[i][j] = 1 + min(dp[i-1][j-1], // Replace
                                   min(dp[i-1][j],  // Delete
                                       dp[i][j-1]))  // Insert
            }
        }
    }

    return dp[m][n]
}
// Time: O(m×n) | Space: O(m×n)
```

---

## Sub-Pattern 5: Longest Increasing Subsequence (LIS)

### Problem: LIS (LeetCode 300)

```go
// O(n²) DP approach
func lengthOfLIS(nums []int) int {
    n := len(nums)
    dp := make([]int, n)
    for i := range dp {
        dp[i] = 1
    }

    maxLen := 1
    for i := 1; i < n; i++ {
        for j := 0; j < i; j++ {
            if nums[i] > nums[j] && dp[j]+1 > dp[i] {
                dp[i] = dp[j] + 1
            }
        }
        if dp[i] > maxLen {
            maxLen = dp[i]
        }
    }

    return maxLen
}

// O(n log n) approach with binary search
func lengthOfLISOptimal(nums []int) int {
    tails := []int{} // tails[i] = smallest tail of IS of length i+1

    for _, num := range nums {
        pos := sort.SearchInts(tails, num)
        if pos == len(tails) {
            tails = append(tails, num)
        } else {
            tails[pos] = num
        }
    }

    return len(tails)
}
// Time: O(n log n) | Space: O(n)
```

---

## Sub-Pattern 6: Grid DP

### Problem: Unique Paths (LeetCode 62)

```go
func uniquePaths(m int, n int) int {
    dp := make([][]int, m)
    for i := range dp {
        dp[i] = make([]int, n)
        dp[i][0] = 1
    }
    for j := 0; j < n; j++ {
        dp[0][j] = 1
    }

    for i := 1; i < m; i++ {
        for j := 1; j < n; j++ {
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
        }
    }

    return dp[m-1][n-1]
}
```

### Problem: Minimum Path Sum (LeetCode 64)

```go
func minPathSum(grid [][]int) int {
    m, n := len(grid), len(grid[0])

    for i := 1; i < m; i++ {
        grid[i][0] += grid[i-1][0]
    }
    for j := 1; j < n; j++ {
        grid[0][j] += grid[0][j-1]
    }

    for i := 1; i < m; i++ {
        for j := 1; j < n; j++ {
            grid[i][j] += min(grid[i-1][j], grid[i][j-1])
        }
    }

    return grid[m-1][n-1]
}
```

---

## Sub-Pattern 7: State Machine DP

### Problem: Best Time to Buy and Sell Stock with Cooldown (LeetCode 309)

```go
func maxProfit(prices []int) int {
    if len(prices) <= 1 {
        return 0
    }

    // States: held (have stock), sold (just sold), rest (cooldown/no stock)
    held := -prices[0]  // Max profit when holding stock
    sold := 0           // Max profit on day we sell
    rest := 0           // Max profit when not holding and not just sold

    for i := 1; i < len(prices); i++ {
        prevHeld := held
        prevSold := sold
        prevRest := rest

        held = max(prevHeld, prevRest-prices[i]) // Keep holding or buy
        sold = prevHeld + prices[i]               // Sell today
        rest = max(prevRest, prevSold)             // Rest or cooldown
    }

    return max(sold, rest)
}
```

---

## Practice Problems by Sub-Pattern

| Sub-Pattern | Problem | Difficulty |
|------------|---------|-----------|
| Fibonacci | Climbing Stairs | Easy |
| Fibonacci | House Robber | Medium |
| 0/1 Knapsack | Partition Equal Subset Sum | Medium |
| 0/1 Knapsack | Target Sum | Medium |
| Unbounded Knapsack | Coin Change | Medium |
| Unbounded Knapsack | Coin Change 2 | Medium |
| LCS | Longest Common Subsequence | Medium |
| LCS | Edit Distance | Medium |
| LIS | Longest Increasing Subsequence | Medium |
| Grid | Unique Paths | Medium |
| Grid | Minimum Path Sum | Medium |
| String | Longest Palindromic Substring | Medium |
| String | Palindrome Partitioning II | Hard |
| String | Regular Expression Matching | Hard |
| State Machine | Stock Buy/Sell (all variants) | Medium-Hard |
| Interval | Burst Balloons | Hard |

---

## Hints

1. **Hint:** Start with brute-force recursion, then add memoization, then convert to bottom-up.
2. **Hint:** 0/1 Knapsack: iterate RIGHT to LEFT in 1D DP. Unbounded: iterate LEFT to RIGHT.
3. **Hint:** For LCS/Edit Distance: if chars match, `dp[i-1][j-1] + 1`. If not, `min/max` of neighbors.
4. **Hint:** Can often reduce 2D DP to 1D by noticing we only use the previous row.
5. **Hint:** If you think of DP but the state space is too large, look for ways to reduce dimensions.
6. **Hint:** String DP almost always has `dp[i][j]` where `i` and `j` are positions in one or two strings.
7. **Hint:** "Number of ways" → SUM transitions. "Min/Max" → MIN/MAX transitions.

---

[← Topological Sort](14-topological-sort.md) | [Back to Index](../README.md) | [Next: Monotonic Stack →](16-monotonic-stack.md)
