# Pattern 22: Greedy Algorithms

## Theory

A **Greedy** algorithm makes the **locally optimal choice** at each step, hoping to find the **global optimum**. Unlike DP (which considers all possibilities), greedy commits to the best immediate choice.

### When Does Greedy Work?
1. **Greedy Choice Property** — A local optimum leads to a global optimum
2. **Optimal Substructure** — An optimal solution contains optimal sub-solutions

### Greedy vs DP
| Greedy | DP |
|--------|-----|
| Makes one choice per step | Considers all choices |
| O(n) or O(n log n) usually | O(n²) or more |
| Hard to prove correct | Always correct (if applicable) |
| Not always optimal | Always optimal |

### How to Verify Greedy Works
- Try to find a **counterexample** where greedy fails
- If you can't, try to prove the **exchange argument** (swapping any non-greedy choice with a greedy one doesn't improve the solution)

---

## How to Identify This Pattern

### Use Greedy when:
- ✅ "**Maximum/minimum** with a simple choice per step"
- ✅ "Activity selection" / scheduling
- ✅ "**Jump game**" — can you reach the end?
- ✅ "**Assign** cookies/tasks optimally"
- ✅ "Make change with fewest coins" (specific denominations like US coins)
- ✅ The problem has an obvious "sort then process" structure

---

## Solved Problems with Explanations

### Problem 1: Jump Game (LeetCode 55)

```go
func canJump(nums []int) bool {
    farthest := 0

    for i := 0; i < len(nums); i++ {
        if i > farthest {
            return false // Can't reach this index
        }
        if i+nums[i] > farthest {
            farthest = i + nums[i]
        }
    }

    return true
}
// Greedy: track the farthest index we can reach
// Time: O(n) | Space: O(1)
```

### Problem 2: Jump Game II — Minimum Jumps (LeetCode 45)

```go
func jump(nums []int) int {
    jumps := 0
    currentEnd := 0
    farthest := 0

    for i := 0; i < len(nums)-1; i++ {
        if i+nums[i] > farthest {
            farthest = i + nums[i]
        }
        if i == currentEnd { // Must jump
            jumps++
            currentEnd = farthest
        }
    }

    return jumps
}
// Greedy: delay jump until forced, then jump to farthest
// Time: O(n) | Space: O(1)
```

### Problem 3: Gas Station (LeetCode 134)

```go
func canCompleteCircuit(gas []int, cost []int) int {
    totalGas, totalCost := 0, 0
    tank := 0
    start := 0

    for i := 0; i < len(gas); i++ {
        totalGas += gas[i]
        totalCost += cost[i]
        tank += gas[i] - cost[i]

        if tank < 0 {
            start = i + 1 // Can't start from any station 0..i
            tank = 0
        }
    }

    if totalGas < totalCost {
        return -1 // Not enough gas overall
    }
    return start
}
// Time: O(n) | Space: O(1)
```

### Problem 4: Best Time to Buy and Sell Stock II — Multiple Transactions (LeetCode 122)

```go
func maxProfit(prices []int) int {
    profit := 0
    for i := 1; i < len(prices); i++ {
        if prices[i] > prices[i-1] {
            profit += prices[i] - prices[i-1] // Collect every uphill
        }
    }
    return profit
}
// Greedy: capture every increase
// Time: O(n) | Space: O(1)
```

### Problem 5: Task Scheduler (LeetCode 621)

```go
func leastInterval(tasks []byte, n int) int {
    freq := [26]int{}
    maxFreq := 0

    for _, t := range tasks {
        freq[t-'A']++
        if freq[t-'A'] > maxFreq {
            maxFreq = freq[t-'A']
        }
    }

    // Count how many tasks have max frequency
    maxCount := 0
    for _, f := range freq {
        if f == maxFreq {
            maxCount++
        }
    }

    // Formula: (maxFreq-1) * (n+1) + maxCount
    // e.g., A=3, B=3, n=2: A_B_A_B_AB → (3-1)*(2+1)+2 = 8
    result := (maxFreq-1)*(n+1) + maxCount

    if len(tasks) > result {
        return len(tasks) // No idle needed
    }
    return result
}
// Time: O(n) | Space: O(1)
```

### Problem 6: Partition Labels (LeetCode 763)

```go
func partitionLabels(s string) []int {
    // Find last occurrence of each character
    lastIdx := [26]int{}
    for i := 0; i < len(s); i++ {
        lastIdx[s[i]-'a'] = i
    }

    result := []int{}
    start, end := 0, 0

    for i := 0; i < len(s); i++ {
        if lastIdx[s[i]-'a'] > end {
            end = lastIdx[s[i]-'a']
        }
        if i == end { // All chars in current partition are contained
            result = append(result, end-start+1)
            start = end + 1
        }
    }

    return result
}
// Time: O(n) | Space: O(1)
```

### Problem 7: Minimum Number of Arrows to Burst Balloons (LeetCode 452)

```go
func findMinArrowShots(points [][]int) int {
    sort.Slice(points, func(i, j int) bool {
        return points[i][1] < points[j][1] // Sort by END
    })

    arrows := 1
    arrowPos := points[0][1]

    for i := 1; i < len(points); i++ {
        if points[i][0] > arrowPos {
            // This balloon starts after current arrow → need new arrow
            arrows++
            arrowPos = points[i][1]
        }
    }

    return arrows
}
// Greedy: shoot at the earliest ending balloon, covers max overlap
// Time: O(n log n) | Space: O(1)
```

### Problem 8: Hand of Straights / Group Cards (LeetCode 846)

```go
func isNStraightHand(hand []int, groupSize int) bool {
    if len(hand)%groupSize != 0 {
        return false
    }

    freq := make(map[int]int)
    for _, card := range hand {
        freq[card]++
    }

    sort.Ints(hand)

    for _, card := range hand {
        if freq[card] == 0 {
            continue
        }

        // Try to form a group starting from this card
        for i := 0; i < groupSize; i++ {
            if freq[card+i] == 0 {
                return false
            }
            freq[card+i]--
        }
    }

    return true
}
// Time: O(n log n) | Space: O(n)
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Jump Game | Medium | Track farthest reachable |
| 2 | Jump Game II | Medium | BFS-like: extend range each "round" |
| 3 | Gas Station | Medium | Reset start when tank < 0 |
| 4 | Best Time to Buy/Sell Stock II | Easy | Collect every increase |
| 5 | Task Scheduler | Medium | Most frequent task determines idle slots |
| 6 | Partition Labels | Medium | Track last index of each char |
| 7 | Min Arrows to Burst Balloons | Medium | Sort by end, greedy shoot |
| 8 | Assign Cookies | Easy | Sort both, match smallest |
| 9 | Meeting Rooms II | Medium | Sort events, count overlaps |
| 10 | Queue Reconstruction by Height | Medium | Sort desc height, insert by position |

---

## Hints

1. **Hint:** If you can't prove greedy works, try DP. If DP is too slow AND you suspect greedy, test with examples.
2. **Hint:** Most greedy problems start with **sorting** — by start, end, value, or some priority.
3. **Hint:** "Sort by end time" is a classic greedy strategy for interval/scheduling problems.
4. **Hint:** For Jump Game type problems, think of BFS layers — each "jump" expands the reachable range.
5. **Hint:** When greedy fails, the usual sign is that a locally good choice leads to a globally worse outcome.

---

[← Prefix Sum](21-prefix-sum.md) | [Back to Index](../README.md)
