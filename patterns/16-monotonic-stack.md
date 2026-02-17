# Pattern 16: Monotonic Stack

## Theory

A **Monotonic Stack** maintains elements in sorted order (either increasing or decreasing). As we process elements, we pop from the stack until the monotonic property is maintained. This efficiently solves **"next greater/smaller element"** problems in O(n).

### Types
| Type | Stack Order | Finds |
|------|------------|-------|
| **Monotonic Decreasing** | Top is smallest | Next Greater Element |
| **Monotonic Increasing** | Top is largest | Next Smaller Element |

### Why O(n)?
Each element is pushed **once** and popped **at most once** → total operations = 2n = O(n).

---

## How to Identify This Pattern

### Use Monotonic Stack when:
- ✅ "**Next greater/smaller** element"
- ✅ "**Largest rectangle** in histogram"
- ✅ "**Daily temperatures**" (days until warmer)
- ✅ "**Stock span**" problem
- ✅ Any problem comparing each element with the nearest larger/smaller

---

## Template Code in Go

### Next Greater Element (Right)

```go
func nextGreaterElement(nums []int) []int {
    n := len(nums)
    result := make([]int, n)
    for i := range result {
        result[i] = -1 // Default: no greater element
    }

    stack := []int{} // Store indices

    for i := 0; i < n; i++ {
        // Pop elements smaller than current
        for len(stack) > 0 && nums[stack[len(stack)-1]] < nums[i] {
            idx := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            result[idx] = nums[i]
        }
        stack = append(stack, i)
    }

    return result
}
```

---

## Solved Problems with Explanations

### Problem 1: Daily Temperatures (LeetCode 739)

```go
func dailyTemperatures(temperatures []int) []int {
    n := len(temperatures)
    result := make([]int, n)
    stack := []int{} // Indices of days waiting for warmer

    for i := 0; i < n; i++ {
        for len(stack) > 0 && temperatures[i] > temperatures[stack[len(stack)-1]] {
            prevDay := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            result[prevDay] = i - prevDay // Days to wait
        }
        stack = append(stack, i)
    }

    return result
}
// Time: O(n) | Space: O(n)
```

### Problem 2: Largest Rectangle in Histogram (LeetCode 84)

```go
func largestRectangleArea(heights []int) int {
    stack := []int{} // Indices, monotonic increasing by height
    maxArea := 0
    n := len(heights)

    for i := 0; i <= n; i++ {
        // Use 0 as sentinel for the end
        currHeight := 0
        if i < n {
            currHeight = heights[i]
        }

        for len(stack) > 0 && currHeight < heights[stack[len(stack)-1]] {
            height := heights[stack[len(stack)-1]]
            stack = stack[:len(stack)-1]

            width := i
            if len(stack) > 0 {
                width = i - stack[len(stack)-1] - 1
            }

            area := height * width
            if area > maxArea {
                maxArea = area
            }
        }

        stack = append(stack, i)
    }

    return maxArea
}
// Time: O(n) | Space: O(n)
```

**Why it works:** For each bar, we find how far left and right it can extend as the shortest bar. The monotonic stack efficiently gives us the left and right boundaries.

### Problem 3: Trapping Rain Water (Monotonic Stack approach)

```go
func trap(height []int) int {
    stack := []int{}
    water := 0

    for i := 0; i < len(height); i++ {
        for len(stack) > 0 && height[i] > height[stack[len(stack)-1]] {
            bottom := stack[len(stack)-1]
            stack = stack[:len(stack)-1]

            if len(stack) == 0 {
                break
            }

            left := stack[len(stack)-1]
            w := i - left - 1
            h := min(height[i], height[left]) - height[bottom]
            water += w * h
        }
        stack = append(stack, i)
    }

    return water
}
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Next Greater Element I | Easy | HashMap + monotonic stack |
| 2 | Daily Temperatures | Medium | Stack of indices, pop when warmer |
| 3 | Largest Rectangle in Histogram | Hard | Pop when shorter bar found, calc area |
| 4 | Maximal Rectangle | Hard | Row-by-row histogram + largest rect |
| 5 | Trapping Rain Water | Hard | Stack: pop when taller bar found |
| 6 | Stock Span Problem | Medium | Count days of consecutive lower prices |
| 7 | Remove Duplicate Letters | Medium | Monotonic stack + frequency tracking |

---

## Hints

1. **Hint:** Store **indices** in the stack, not values. You can always look up the value.
2. **Hint:** "Next Greater" → decreasing stack. "Next Smaller" → increasing stack.
3. **Hint:** For histogram, add a sentinel height of 0 at the end to flush remaining bars.
4. **Hint:** Each element is pushed once and popped once → O(n) guaranteed.

---

[← Dynamic Programming](15-dynamic-programming.md) | [Back to Index](../README.md) | [Next: Union Find →](17-union-find.md)
