# Pattern 11: Modified Binary Search

## Theory

**Binary Search** finds targets in O(log n) by eliminating half the search space at each step. The "modified" versions adapt this to rotated arrays, 2D matrices, finding boundaries, and more.

### The Three Binary Search Templates

| Template | Condition | Use Case |
|----------|-----------|----------|
| `lo <= hi` | Standard, terminates when lo > hi | Exact match |
| `lo < hi` | Terminates when lo == hi | Find boundary/insertion point |
| `lo + 1 < hi` | Terminates when lo and hi are adjacent | Avoid off-by-one |

### Key Decision: What to Do at `mid`
```
if target found at mid      → return mid
if target is in right half  → lo = mid + 1
if target is in left half   → hi = mid - 1
```

---

## How to Identify This Pattern

### Use Binary Search when:
- ✅ **Sorted** array/matrix
- ✅ "Search for a target" in O(log n)
- ✅ "Find the first/last occurrence"
- ✅ "Find the smallest/largest that satisfies condition"
- ✅ "Search in rotated sorted array"
- ✅ Answer space is **monotonic** (binary search on answer)

---

## Template Code in Go

### Template 1: Standard Binary Search

```go
func binarySearch(nums []int, target int) int {
    lo, hi := 0, len(nums)-1

    for lo <= hi {
        mid := lo + (hi-lo)/2 // Prevents integer overflow

        if nums[mid] == target {
            return mid
        } else if nums[mid] < target {
            lo = mid + 1
        } else {
            hi = mid - 1
        }
    }

    return -1 // Not found
}
```

### Template 2: Find Left Boundary (First Occurrence)

```go
func findFirst(nums []int, target int) int {
    lo, hi := 0, len(nums)-1
    result := -1

    for lo <= hi {
        mid := lo + (hi-lo)/2

        if nums[mid] == target {
            result = mid  // Record and keep searching left
            hi = mid - 1
        } else if nums[mid] < target {
            lo = mid + 1
        } else {
            hi = mid - 1
        }
    }

    return result
}
```

### Template 3: Binary Search on Answer

```go
func binarySearchOnAnswer(lo, hi int) int {
    for lo < hi {
        mid := lo + (hi-lo)/2

        if canAchieve(mid) {
            hi = mid // mid works, try to minimize
        } else {
            lo = mid + 1 // mid doesn't work, need more
        }
    }

    return lo // lo == hi, optimal answer
}

func canAchieve(value int) bool {
    // Problem-specific feasibility check
    return true
}
```

---

## Solved Problems with Explanations

### Problem 1: Search in Rotated Sorted Array (LeetCode 33)

```go
func search(nums []int, target int) int {
    lo, hi := 0, len(nums)-1

    for lo <= hi {
        mid := lo + (hi-lo)/2

        if nums[mid] == target {
            return mid
        }

        // Determine which half is sorted
        if nums[lo] <= nums[mid] {
            // Left half is sorted
            if target >= nums[lo] && target < nums[mid] {
                hi = mid - 1 // Target in sorted left half
            } else {
                lo = mid + 1 // Target in right half
            }
        } else {
            // Right half is sorted
            if target > nums[mid] && target <= nums[hi] {
                lo = mid + 1 // Target in sorted right half
            } else {
                hi = mid - 1 // Target in left half
            }
        }
    }

    return -1
}
// Time: O(log n) | Space: O(1)
```

**Key Insight:** In a rotated sorted array, one half is ALWAYS sorted. Check if target is in the sorted half. If yes, search there. If no, search the other half.

### Problem 2: Find Minimum in Rotated Sorted Array (LeetCode 153)

```go
func findMin(nums []int) int {
    lo, hi := 0, len(nums)-1

    for lo < hi {
        mid := lo + (hi-lo)/2

        if nums[mid] > nums[hi] {
            lo = mid + 1 // Minimum is in right half
        } else {
            hi = mid // Minimum could be mid or in left half
        }
    }

    return nums[lo]
}
// Time: O(log n) | Space: O(1)
```

### Problem 3: Search a 2D Matrix (LeetCode 74)

```go
func searchMatrix(matrix [][]int, target int) bool {
    rows, cols := len(matrix), len(matrix[0])
    lo, hi := 0, rows*cols-1

    for lo <= hi {
        mid := lo + (hi-lo)/2
        // Convert 1D index to 2D
        row, col := mid/cols, mid%cols
        val := matrix[row][col]

        if val == target {
            return true
        } else if val < target {
            lo = mid + 1
        } else {
            hi = mid - 1
        }
    }

    return false
}
// Time: O(log(m*n)) | Space: O(1)
```

### Problem 4: Find First and Last Position (LeetCode 34)

```go
func searchRange(nums []int, target int) []int {
    return []int{findBound(nums, target, true), findBound(nums, target, false)}
}

func findBound(nums []int, target int, isFirst bool) int {
    lo, hi := 0, len(nums)-1
    result := -1

    for lo <= hi {
        mid := lo + (hi-lo)/2

        if nums[mid] == target {
            result = mid
            if isFirst {
                hi = mid - 1 // Keep searching left for first
            } else {
                lo = mid + 1 // Keep searching right for last
            }
        } else if nums[mid] < target {
            lo = mid + 1
        } else {
            hi = mid - 1
        }
    }

    return result
}
// Time: O(log n) | Space: O(1)
```

### Problem 5: Koko Eating Bananas — Binary Search on Answer (LeetCode 875)

```go
func minEatingSpeed(piles []int, h int) int {
    lo, hi := 1, 0

    // Find max pile (upper bound for speed)
    for _, p := range piles {
        if p > hi {
            hi = p
        }
    }

    for lo < hi {
        mid := lo + (hi-lo)/2

        if canFinish(piles, mid, h) {
            hi = mid // Can finish at this speed, try slower
        } else {
            lo = mid + 1 // Too slow
        }
    }

    return lo
}

func canFinish(piles []int, speed, hours int) bool {
    totalHours := 0
    for _, p := range piles {
        totalHours += (p + speed - 1) / speed // Ceiling division
    }
    return totalHours <= hours
}
// Time: O(n log m) where m = max pile | Space: O(1)
```

**Binary Search on Answer explained:** Instead of searching in an array, we search in the ANSWER SPACE. The answer (eating speed) ranges from 1 to max(piles). We binary search for the minimum speed that allows finishing in ≤ h hours.

### Problem 6: Split Array Largest Sum (LeetCode 410)

```go
func splitArray(nums []int, k int) int {
    lo, hi := 0, 0

    for _, n := range nums {
        if n > lo {
            lo = n // Min possible: largest single element
        }
        hi += n // Max possible: sum of all
    }

    for lo < hi {
        mid := lo + (hi-lo)/2

        if canSplit(nums, mid, k) {
            hi = mid
        } else {
            lo = mid + 1
        }
    }

    return lo
}

func canSplit(nums []int, maxSum, k int) bool {
    splits, currentSum := 1, 0
    for _, n := range nums {
        if currentSum+n > maxSum {
            splits++
            currentSum = n
            if splits > k {
                return false
            }
        } else {
            currentSum += n
        }
    }
    return true
}
// Time: O(n log S) where S = sum of array | Space: O(1)
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Binary Search | Easy | Standard template |
| 2 | Search in Rotated Array | Medium | One half is always sorted |
| 3 | Find Minimum in Rotated Array | Medium | Compare `mid` with `hi` |
| 4 | First and Last Position | Medium | Two searches: first + last |
| 5 | Search 2D Matrix | Medium | Flatten to 1D: `row=mid/cols, col=mid%cols` |
| 6 | Find Peak Element | Medium | Go towards the higher neighbor |
| 7 | Koko Eating Bananas | Medium | Binary search on speed |
| 8 | Split Array Largest Sum | Hard | Binary search on the max subarray sum |
| 9 | Capacity to Ship Packages | Medium | Binary search on weight capacity |
| 10 | Median of Two Sorted Arrays | Hard | Binary search on partition |

---

## Hints

1. **Hint:** Use `mid = lo + (hi-lo)/2` instead of `(lo+hi)/2` to prevent integer overflow.
2. **Hint:** For "find first/last" — when you find target, DON'T return immediately. Record and keep searching.
3. **Hint:** For rotated arrays — identify which half is sorted, then check if target is in that sorted half.
4. **Hint:** "Binary Search on Answer" — when the answer is a number in a range and you can CHECK if a value works, binary search the answer space.
5. **Hint:** In Go, `sort.Search(n, func(i int) bool)` does binary search. It returns the smallest index where `f(i)` is true.

---

[← Subsets/Backtracking](10-subsets-backtracking.md) | [Back to Index](../README.md) | [Next: Top K Elements →](12-top-k-elements.md)
