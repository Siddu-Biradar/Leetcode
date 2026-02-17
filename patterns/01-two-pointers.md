# Pattern 01: Two Pointers

## Theory

The **Two Pointers** pattern uses two references (indices or pointers) that move through a data structure — typically an array or linked list — to solve problems with **O(n)** time and **O(1)** space.

### Why It Works
When an array is sorted, the relationship between elements at two positions gives us information about which pointer to move. Instead of checking every pair (O(n²)), we eliminate large chunks of the search space with each step.

### Types of Two Pointers

| Type | Description | Example |
|------|------------|---------|
| **Opposite Direction** | One pointer at start, one at end, move towards each other | Two Sum (sorted), Container with Most Water |
| **Same Direction** | Both start at beginning, one moves faster | Remove Duplicates, Partition |
| **One Array, Two Pointers** | Compare elements within same array | Dutch National Flag |
| **Two Arrays** | One pointer per array | Merge Sorted Arrays |

---

## How to Identify This Pattern

### You should use Two Pointers when:
- ✅ Array/string is **sorted** (or can be sorted without losing info)
- ✅ Need to find a **pair** with a certain sum/difference
- ✅ Need to find a **triplet** or **quadruplet** with constraints
- ✅ Need to **remove duplicates** in-place
- ✅ Need to **partition** an array by some condition
- ✅ Problem says "in-place" with O(1) extra space
- ✅ Comparing elements from **both ends** of an array

### You should NOT use Two Pointers when:
- ❌ Array is unsorted AND sorting would lose important info (like indices)
- ❌ Need to find **all** subsets or combinations (use Backtracking)
- ❌ Elements are not comparable or don't have an ordering

---

## Template Code in Go

### Template 1: Opposite Direction (Converging Pointers)

```go
func twoPointerOpposite(arr []int, target int) (int, int) {
    left, right := 0, len(arr)-1

    for left < right {
        currentSum := arr[left] + arr[right]

        if currentSum == target {
            return left, right // Found the pair
        } else if currentSum < target {
            left++ // Need larger sum, move left pointer right
        } else {
            right-- // Need smaller sum, move right pointer left
        }
    }

    return -1, -1 // No pair found
}
```

**Why it works:** In a sorted array, if `arr[left] + arr[right] < target`, moving `left` forward increases the sum. If the sum is too large, moving `right` backward decreases it. Each step eliminates one possibility, so we check at most `n` pairs.

### Template 2: Same Direction (Fast & Slow / Reader-Writer)

```go
func removeDuplicates(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    
    // 'writer' is where we write the next unique element
    // 'reader' scans through the array
    writer := 1

    for reader := 1; reader < len(nums); reader++ {
        if nums[reader] != nums[reader-1] {
            nums[writer] = nums[reader]
            writer++
        }
    }

    return writer // Length of unique portion
}
```

**Why it works:** The `writer` pointer only advances when we find a new unique element. The `reader` scans every element. This partitions the array in-place.

### Template 3: Three Pointers (Dutch National Flag)

```go
func sortColors(nums []int) {
    // Three regions: [0..low-1]=0, [low..mid-1]=1, [high+1..n-1]=2
    low, mid, high := 0, 0, len(nums)-1

    for mid <= high {
        switch nums[mid] {
        case 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low++
            mid++
        case 1:
            mid++
        case 2:
            nums[mid], nums[high] = nums[high], nums[mid]
            high--
            // Don't increment mid! The swapped element needs checking
        }
    }
}
```

---

## Solved Problems with Explanations

### Problem 1: Two Sum II (Sorted Array)
**Given:** Sorted array, find two numbers that add up to target.

```go
func twoSum(numbers []int, target int) []int {
    left, right := 0, len(numbers)-1

    for left < right {
        sum := numbers[left] + numbers[right]
        if sum == target {
            return []int{left + 1, right + 1} // 1-indexed
        } else if sum < target {
            left++
        } else {
            right--
        }
    }

    return []int{-1, -1}
}
// Time: O(n) | Space: O(1)
```

### Problem 2: Three Sum (Find All Triplets = 0)
**Given:** Array of integers, find all unique triplets that sum to zero.

```go
func threeSum(nums []int) [][]int {
    sort.Ints(nums) // MUST sort first
    result := [][]int{}

    for i := 0; i < len(nums)-2; i++ {
        // Skip duplicate first elements
        if i > 0 && nums[i] == nums[i-1] {
            continue
        }

        left, right := i+1, len(nums)-1
        target := -nums[i]

        for left < right {
            sum := nums[left] + nums[right]
            if sum == target {
                result = append(result, []int{nums[i], nums[left], nums[right]})
                // Skip duplicates for second element
                for left < right && nums[left] == nums[left+1] {
                    left++
                }
                // Skip duplicates for third element
                for left < right && nums[right] == nums[right-1] {
                    right--
                }
                left++
                right--
            } else if sum < target {
                left++
            } else {
                right--
            }
        }
    }

    return result
}
// Time: O(n²) | Space: O(1) excluding output
```

### Problem 3: Container With Most Water
**Given:** Array of heights, find two lines forming a container with maximum water.

```go
func maxArea(height []int) int {
    left, right := 0, len(height)-1
    maxWater := 0

    for left < right {
        // Width is distance between pointers
        width := right - left
        // Height is min of two sides (water can't go above shorter side)
        h := min(height[left], height[right])
        water := width * h

        if water > maxWater {
            maxWater = water
        }

        // Move the shorter side inward (only way to potentially increase area)
        if height[left] < height[right] {
            left++
        } else {
            right--
        }
    }

    return maxWater
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}
// Time: O(n) | Space: O(1)
```

### Problem 4: Trapping Rain Water
**Given:** Array of heights representing an elevation map, compute how much water it can trap.

```go
func trap(height []int) int {
    if len(height) == 0 {
        return 0
    }

    left, right := 0, len(height)-1
    leftMax, rightMax := 0, 0
    water := 0

    for left < right {
        if height[left] < height[right] {
            if height[left] >= leftMax {
                leftMax = height[left]
            } else {
                water += leftMax - height[left]
            }
            left++
        } else {
            if height[right] >= rightMax {
                rightMax = height[right]
            } else {
                water += rightMax - height[right]
            }
            right--
        }
    }

    return water
}
// Time: O(n) | Space: O(1)
```

**Why it works:** At each step, we process the shorter side. The water above a bar is determined by the minimum of the maximum heights on both sides. Since we process the shorter side, we know the water level is bounded by that side's max.

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Two Sum II - Input Array Is Sorted | Easy | Opposite pointers, adjust based on sum |
| 2 | Remove Duplicates from Sorted Array | Easy | Same direction, writer-reader |
| 3 | Squares of a Sorted Array | Easy | Opposite pointers, compare absolute values |
| 4 | 3Sum | Medium | Sort + fix one + two pointers |
| 5 | 3Sum Closest | Medium | Track minimum difference |
| 6 | Container With Most Water | Medium | Move shorter side inward |
| 7 | Sort Colors (Dutch National Flag) | Medium | Three pointers: low, mid, high |
| 8 | Trapping Rain Water | Hard | Track leftMax, rightMax, process shorter side |
| 9 | 4Sum | Medium | Two nested loops + two pointers |
| 10 | Backspace String Compare | Easy | Start from end, skip characters |

---

## Hints for Common Mistakes

1. **Hint:** Always check if the array needs sorting first. Two-pointer on an unsorted array gives wrong results.
2. **Hint:** For "skip duplicates" — use `for` loop (not `if`) to skip ALL duplicates.
3. **Hint:** In Dutch National Flag, DON'T increment `mid` when swapping with `high` — the swapped element hasn't been examined yet.
4. **Hint:** For Trapping Rain Water, think "which side is the bottleneck?" — always process the shorter side.
5. **Hint:** When the problem says "in-place with O(1) space" in a sorted array, think Two Pointers immediately.

---

## Complexity Analysis

| Variant | Time | Space |
|---------|------|-------|
| Two Sum (sorted) | O(n) | O(1) |
| Three Sum | O(n²) | O(1) |
| Four Sum | O(n³) | O(1) |
| Remove Duplicates | O(n) | O(1) |
| Dutch National Flag | O(n) | O(1) |

---

[← Back to Index](../README.md) | [Next: Sliding Window →](02-sliding-window.md)
