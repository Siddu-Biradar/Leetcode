# Pattern 19: Bit Manipulation

## Theory

**Bit manipulation** operates directly on binary representations of integers. It's extremely fast (single CPU instruction) and uses O(1) space.

### Essential Bit Operations

| Operation | Code | Effect |
|-----------|------|--------|
| AND | `a & b` | 1 only if both bits are 1 |
| OR | `a \| b` | 1 if either bit is 1 |
| XOR | `a ^ b` | 1 if bits are different |
| NOT | `^a` (Go) | Flip all bits |
| Left Shift | `a << n` | Multiply by 2^n |
| Right Shift | `a >> n` | Divide by 2^n |

### Key Bit Tricks

| Trick | Code | Example |
|-------|------|---------|
| Check if even | `n & 1 == 0` | 6 & 1 = 0 (even) |
| Check if power of 2 | `n & (n-1) == 0` | 8 & 7 = 0 (power of 2) |
| Get i-th bit | `(n >> i) & 1` | Get bit at position i |
| Set i-th bit | `n \| (1 << i)` | Force bit i to 1 |
| Clear i-th bit | `n & ^(1 << i)` | Force bit i to 0 |
| Toggle i-th bit | `n ^ (1 << i)` | Flip bit i |
| Count set bits | `bits.OnesCount(uint(n))` | Population count |
| Remove lowest set bit | `n & (n-1)` | Turn off rightmost 1 |
| Isolate lowest set bit | `n & (-n)` | Keep only rightmost 1 |

### XOR Properties (Most Important!)
```
a ^ a = 0        (same numbers cancel)
a ^ 0 = a        (XOR with 0 = identity)
a ^ b ^ a = b    (find the "odd one out")
Commutative: a ^ b = b ^ a
Associative: (a ^ b) ^ c = a ^ (b ^ c)
```

---

## How to Identify This Pattern

### Use Bit Manipulation when:
- ✅ "Single number" (find element appearing odd times)
- ✅ "Power of 2"
- ✅ "Count bits" / "Hamming distance"
- ✅ Problem constraints suggest O(1) space for number operations
- ✅ Input involves sets that can be represented as bitmasks
- ✅ XOR can cancel paired elements

---

## Solved Problems with Explanations

### Problem 1: Single Number (LeetCode 136)
**Every element appears twice except one. Find it.**

```go
func singleNumber(nums []int) int {
    result := 0
    for _, num := range nums {
        result ^= num // Pairs cancel out via XOR
    }
    return result
}
// Time: O(n) | Space: O(1)
// Example: [4,1,2,1,2] → 4^1^2^1^2 = (1^1)^(2^2)^4 = 0^0^4 = 4
```

### Problem 2: Single Number II — Element Appears Once, Others 3 Times (LeetCode 137)

```go
func singleNumber(nums []int) int {
    ones, twos := 0, 0
    for _, num := range nums {
        ones = (ones ^ num) & ^twos
        twos = (twos ^ num) & ^ones
    }
    return ones
}
// Time: O(n) | Space: O(1)
```

### Problem 3: Number of 1 Bits (LeetCode 191)

```go
func hammingWeight(n uint32) int {
    count := 0
    for n != 0 {
        n &= n - 1 // Remove lowest set bit
        count++
    }
    return count
}
// Time: O(number of 1-bits) | Space: O(1)
```

### Problem 4: Counting Bits (LeetCode 338)

```go
func countBits(n int) []int {
    dp := make([]int, n+1)
    for i := 1; i <= n; i++ {
        dp[i] = dp[i&(i-1)] + 1 // Remove lowest set bit, add 1
    }
    return dp
}
// Time: O(n) | Space: O(n)
```

### Problem 5: Missing Number (Bit approach) (LeetCode 268)

```go
func missingNumber(nums []int) int {
    xor := len(nums)
    for i, num := range nums {
        xor ^= i ^ num // XOR all indices and values
    }
    return xor
}
// XOR of [0,1,2,...,n] XOR [nums] → missing number survives
```

### Problem 6: Power of Two (LeetCode 231)

```go
func isPowerOfTwo(n int) bool {
    return n > 0 && n&(n-1) == 0
}
// Powers of 2 have exactly one bit set: 1, 10, 100, 1000...
// n-1 flips that bit and all below: 1000 - 1 = 0111
// AND gives 0 only for powers of 2
```

### Problem 7: Subsets Using Bitmask

```go
func subsets(nums []int) [][]int {
    n := len(nums)
    result := [][]int{}

    for mask := 0; mask < (1 << n); mask++ {
        subset := []int{}
        for i := 0; i < n; i++ {
            if mask&(1<<i) != 0 {
                subset = append(subset, nums[i])
            }
        }
        result = append(result, subset)
    }

    return result
}
// Each number from 0 to 2^n - 1 represents a unique subset
// Bit i = 1 means include nums[i]
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Single Number | Easy | XOR all elements |
| 2 | Single Number II | Medium | Bit counting modulo 3 |
| 3 | Number of 1 Bits | Easy | `n & (n-1)` removes lowest bit |
| 4 | Counting Bits | Easy | `dp[i] = dp[i & (i-1)] + 1` |
| 5 | Missing Number | Easy | XOR indices with values |
| 6 | Reverse Bits | Easy | Extract bit by bit, build reverse |
| 7 | Power of Two | Easy | `n & (n-1) == 0` |
| 8 | Subsets (Bitmask) | Medium | Enumerate all 2^n masks |
| 9 | Hamming Distance | Easy | XOR, then count 1-bits |
| 10 | Single Number III | Medium | XOR all → split by differing bit |

---

## Hints

1. **Hint:** XOR is your best friend. `a ^ a = 0` and `a ^ 0 = a`.
2. **Hint:** `n & (n-1)` turns off the lowest set bit — use it for counting bits and power-of-2 checks.
3. **Hint:** Bitmasks can represent sets of up to 20 elements (2^20 ≈ 10^6 fits in memory).
4. **Hint:** In Go, use `math/bits` package for `OnesCount`, `LeadingZeros`, etc.
5. **Hint:** For "elements appearing k times except one" — think about counting each bit position modulo k.

---

[← Trie](18-trie.md) | [Back to Index](../README.md) | [Next: Graph BFS/DFS →](20-graph-bfs-dfs.md)
