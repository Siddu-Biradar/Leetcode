# Pattern 05: Cyclic Sort

## Theory

**Cyclic Sort** is a clever pattern for problems where the input contains numbers in a given range `[1, n]` or `[0, n]`. The core idea: **place each number at its correct index** (number `i` goes to index `i-1`). After one pass, any number not at its correct position reveals the answer (missing, duplicate, etc.).

### Why It Works
If numbers are in range [1, n] and array has n slots, each number has exactly one "home" index. We iterate and swap each number to its home. This takes O(n) because each number is swapped at most once.

### The Core Swap Logic
```
For range [1, n]: number i belongs at index i-1
For range [0, n]: number i belongs at index i
```

---

## How to Identify This Pattern

### Use Cyclic Sort when:
- ✅ Array contains numbers in range **[1, n]** or **[0, n]**
- ✅ Find **missing number(s)**
- ✅ Find **duplicate number(s)**
- ✅ Find the **first missing positive**
- ✅ Problem says "numbers in range" and needs O(n) time, O(1) space

### Magic Words:
> "numbers in range [1, n]", "missing number", "find duplicate", "first missing positive", "all missing numbers"

---

## Template Code in Go

```go
func cyclicSort(nums []int) {
    i := 0
    for i < len(nums) {
        correctIdx := nums[i] - 1 // For range [1, n]
        
        if nums[i] != nums[correctIdx] {
            // Swap to correct position
            nums[i], nums[correctIdx] = nums[correctIdx], nums[i]
        } else {
            i++ // Already in correct place
        }
    }
}
```

---

## Solved Problems with Explanations

### Problem 1: Missing Number (LeetCode 268)
**Given:** Array of n numbers in [0, n], find the missing one.

```go
func missingNumber(nums []int) int {
    i := 0
    n := len(nums)

    for i < n {
        // nums[i] should be at index nums[i] (for range [0, n])
        // Skip if value is n (no index n exists) or already correct
        if nums[i] < n && nums[i] != i {
            nums[i], nums[nums[i]] = nums[nums[i]], nums[i]
        } else {
            i++
        }
    }

    // Find which index doesn't have the right number
    for i := 0; i < n; i++ {
        if nums[i] != i {
            return i
        }
    }

    return n // All 0..n-1 present, so n is missing
}
// Time: O(n) | Space: O(1)
```

### Problem 2: Find All Numbers Disappeared in an Array (LeetCode 448)

```go
func findDisappearedNumbers(nums []int) []int {
    // Place each number at its correct index (num i at index i-1)
    i := 0
    for i < len(nums) {
        correctIdx := nums[i] - 1
        if nums[i] != nums[correctIdx] {
            nums[i], nums[correctIdx] = nums[correctIdx], nums[i]
        } else {
            i++
        }
    }

    // Numbers not at their correct index are missing
    result := []int{}
    for i := 0; i < len(nums); i++ {
        if nums[i] != i+1 {
            result = append(result, i+1)
        }
    }

    return result
}
// Time: O(n) | Space: O(1) excluding output
```

### Problem 3: Find the Duplicate Number (LeetCode 287)
(Cyclic Sort approach — alternative to Floyd's)

```go
func findDuplicate(nums []int) int {
    i := 0
    for i < len(nums) {
        if nums[i] != i+1 {
            correctIdx := nums[i] - 1
            if nums[i] != nums[correctIdx] {
                nums[i], nums[correctIdx] = nums[correctIdx], nums[i]
            } else {
                return nums[i] // Duplicate found!
            }
        } else {
            i++
        }
    }
    return -1
}
// Time: O(n) | Space: O(1)
```

### Problem 4: Find All Duplicates in an Array (LeetCode 442)

```go
func findDuplicates(nums []int) []int {
    i := 0
    for i < len(nums) {
        correctIdx := nums[i] - 1
        if nums[i] != nums[correctIdx] {
            nums[i], nums[correctIdx] = nums[correctIdx], nums[i]
        } else {
            i++
        }
    }

    result := []int{}
    for i := 0; i < len(nums); i++ {
        if nums[i] != i+1 {
            result = append(result, nums[i])
        }
    }

    return result
}
// Time: O(n) | Space: O(1)
```

### Problem 5: First Missing Positive (LeetCode 41) — HARD
**Given:** Unsorted array, find the smallest missing positive integer in O(n) time, O(1) space.

```go
func firstMissingPositive(nums []int) int {
    n := len(nums)
    i := 0

    for i < n {
        // Only place numbers in range [1, n]
        correctIdx := nums[i] - 1
        if nums[i] > 0 && nums[i] <= n && nums[i] != nums[correctIdx] {
            nums[i], nums[correctIdx] = nums[correctIdx], nums[i]
        } else {
            i++
        }
    }

    // First index where nums[i] != i+1 → answer is i+1
    for i := 0; i < n; i++ {
        if nums[i] != i+1 {
            return i + 1
        }
    }

    return n + 1 // All [1, n] present
}
// Time: O(n) | Space: O(1)
```

**Key Insight:** We only care about numbers in [1, n]. Negative numbers and numbers > n are irrelevant. After placing valid numbers at their correct indices, the first "hole" is the answer.

### Problem 6: Set Mismatch (LeetCode 645)
**Given:** Array where one number is duplicated and one is missing. Find both.

```go
func findErrorNums(nums []int) []int {
    i := 0
    for i < len(nums) {
        correctIdx := nums[i] - 1
        if nums[i] != nums[correctIdx] {
            nums[i], nums[correctIdx] = nums[correctIdx], nums[i]
        } else {
            i++
        }
    }

    for i := 0; i < len(nums); i++ {
        if nums[i] != i+1 {
            return []int{nums[i], i + 1} // {duplicate, missing}
        }
    }

    return []int{}
}
// Time: O(n) | Space: O(1)
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Missing Number | Easy | Range [0,n], skip value n during sort |
| 2 | Find All Disappeared Numbers | Easy | After sort, nums[i] != i+1 → i+1 is missing |
| 3 | Find the Duplicate Number | Medium | When swap destination has same value → duplicate |
| 4 | Find All Duplicates | Medium | Same as disappeared but collect nums[i] not i+1 |
| 5 | First Missing Positive | Hard | Ignore negatives and > n, cyclic sort rest |
| 6 | Set Mismatch | Easy | After sort, wrong position gives both answers |

---

## Hints

1. **Hint:** The swap condition is `nums[i] != nums[correctIdx]` NOT `nums[i] != correctIdx+1`. Comparing values at positions avoids infinite loops with duplicates.
2. **Hint:** Don't increment `i` after a swap! The swapped-in element might also need to be placed correctly.
3. **Hint:** For First Missing Positive, filter: only process numbers in range `[1, n]`.
4. **Hint:** After cyclic sort, a single pass reveals all missing/duplicate numbers.
5. **Hint:** This pattern always gives O(n) time, O(1) space — even though we have a `for` inside a `for`, each element is swapped at most once.

---

[← Merge Intervals](04-merge-intervals.md) | [Back to Index](../README.md) | [Next: Linked List Reversal →](06-linked-list-reversal.md)
