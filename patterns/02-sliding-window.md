# Pattern 02: Sliding Window

## Theory

The **Sliding Window** pattern maintains a "window" (a contiguous range) over a data structure and slides it to efficiently compute something over all possible windows. It converts a nested O(n²) or O(n×k) approach into O(n).

### Why It Works
Instead of recalculating from scratch for each position, we **add** the new element entering the window and **remove** the element leaving. This incremental update takes O(1) per step.

### Types of Sliding Windows

| Type | Window Size | Use Case |
|------|------------|----------|
| **Fixed Window** | Always `k` | Max sum of subarray of size k, averages |
| **Variable Window (Shrinking)** | Grows until invalid, then shrinks | Smallest subarray with sum ≥ S |
| **Variable Window (Growing)** | Shrinks until valid, then grows | Longest substring without repeating chars |
| **Window with HashMap** | Tracks element frequencies | Minimum window substring, anagram |

---

## How to Identify This Pattern

### You should use Sliding Window when:
- ✅ Problem involves **contiguous subarray** or **substring**
- ✅ Need to find **max/min/count** of something in a window
- ✅ Given a window size `k`, find something for all windows
- ✅ "Longest/shortest subarray/substring with condition X"
- ✅ Input is **linear** (array, string, linked list)

### You should NOT use Sliding Window when:
- ❌ Elements don't need to be contiguous (use DP or Backtracking)
- ❌ Problem involves subsequences (not substrings)
- ❌ Window condition can't be maintained incrementally(e.g., median — use heap)

### Magic Words in Problem Statements:
> "subarray", "substring", "contiguous", "window of size k", "longest", "shortest", "at most k distinct", "maximum sum"

---

## Template Code in Go

### Template 1: Fixed-Size Window

```go
func fixedWindow(arr []int, k int) []int {
    result := []int{}
    windowSum := 0

    for i := 0; i < len(arr); i++ {
        windowSum += arr[i] // Add incoming element

        if i >= k-1 { // Window is full
            result = append(result, windowSum) // Process window
            windowSum -= arr[i-(k-1)]          // Remove outgoing element
        }
    }

    return result
}
```

**Mental Model:** The window is like a conveyor belt of size `k`. As a new item gets on from the right, the oldest item falls off from the left.

### Template 2: Variable Window — Find Smallest

```go
func smallestWindowWithSum(arr []int, targetSum int) int {
    windowStart := 0
    windowSum := 0
    minLength := math.MaxInt32

    for windowEnd := 0; windowEnd < len(arr); windowEnd++ {
        windowSum += arr[windowEnd] // Expand window

        // Shrink window while condition is satisfied
        for windowSum >= targetSum {
            currentLength := windowEnd - windowStart + 1
            if currentLength < minLength {
                minLength = currentLength
            }
            windowSum -= arr[windowStart] // Remove from left
            windowStart++                  // Shrink
        }
    }

    if minLength == math.MaxInt32 {
        return 0
    }
    return minLength
}
```

**Key Insight:** We GROW the window (move `windowEnd` right) to satisfy the condition, then SHRINK (move `windowStart` right) to find the minimum.

### Template 3: Variable Window — Find Longest (with HashMap)

```go
func longestWithCondition(s string) int {
    charFreq := make(map[byte]int)
    windowStart := 0
    maxLength := 0

    for windowEnd := 0; windowEnd < len(s); windowEnd++ {
        rightChar := s[windowEnd]
        charFreq[rightChar]++ // Add to window

        // Shrink window while condition is violated
        for /* condition violated */ len(charFreq) > /* some limit */ 2 {
            leftChar := s[windowStart]
            charFreq[leftChar]--
            if charFreq[leftChar] == 0 {
                delete(charFreq, leftChar)
            }
            windowStart++
        }

        // Update answer (window is valid here)
        currentLength := windowEnd - windowStart + 1
        if currentLength > maxLength {
            maxLength = currentLength
        }
    }

    return maxLength
}
```

---

## Solved Problems with Explanations

### Problem 1: Maximum Sum Subarray of Size K (Fixed Window)

```go
func maxSumSubarray(arr []int, k int) int {
    maxSum, windowSum := 0, 0

    for i := 0; i < len(arr); i++ {
        windowSum += arr[i]

        if i >= k-1 {
            if windowSum > maxSum {
                maxSum = windowSum
            }
            windowSum -= arr[i-(k-1)]
        }
    }

    return maxSum
}
// Time: O(n) | Space: O(1)
```

### Problem 2: Longest Substring Without Repeating Characters

```go
func lengthOfLongestSubstring(s string) int {
    charIndex := make(map[byte]int) // char -> last seen index
    windowStart := 0
    maxLength := 0

    for windowEnd := 0; windowEnd < len(s); windowEnd++ {
        rightChar := s[windowEnd]

        // If we've seen this char and it's in our current window
        if lastIdx, exists := charIndex[rightChar]; exists && lastIdx >= windowStart {
            // Jump windowStart past the duplicate
            windowStart = lastIdx + 1
        }

        charIndex[rightChar] = windowEnd

        currentLength := windowEnd - windowStart + 1
        if currentLength > maxLength {
            maxLength = currentLength
        }
    }

    return maxLength
}
// Time: O(n) | Space: O(min(n, 26)) for lowercase English letters
```

**Why it works:** We maintain a window where all characters are unique. When we find a duplicate, we jump the start past the previous occurrence. The HashMap stores the last seen index of each character.

### Problem 3: Minimum Window Substring
**Given:** Strings `s` and `t`, find the smallest substring of `s` that contains all characters of `t`.

```go
func minWindow(s string, t string) string {
    if len(s) == 0 || len(t) == 0 {
        return ""
    }

    // Count characters needed from t
    need := make(map[byte]int)
    for i := 0; i < len(t); i++ {
        need[t[i]]++
    }

    have := make(map[byte]int)
    formed := 0       // Number of unique chars in t that are fully matched
    required := len(need) // Number of unique chars in t

    windowStart := 0
    minLen := math.MaxInt32
    minStart := 0

    for windowEnd := 0; windowEnd < len(s); windowEnd++ {
        // Add character from right
        char := s[windowEnd]
        have[char]++

        // Check if this char's frequency matches what's needed
        if need[char] > 0 && have[char] == need[char] {
            formed++
        }

        // Try to shrink window while it's valid
        for formed == required {
            // Update answer
            windowLen := windowEnd - windowStart + 1
            if windowLen < minLen {
                minLen = windowLen
                minStart = windowStart
            }

            // Remove from left
            leftChar := s[windowStart]
            have[leftChar]--
            if need[leftChar] > 0 && have[leftChar] < need[leftChar] {
                formed--
            }
            windowStart++
        }
    }

    if minLen == math.MaxInt32 {
        return ""
    }
    return s[minStart : minStart+minLen]
}
// Time: O(|s| + |t|) | Space: O(|s| + |t|)
```

### Problem 4: Longest Substring with At Most K Distinct Characters

```go
func lengthOfLongestSubstringKDistinct(s string, k int) int {
    if k == 0 {
        return 0
    }

    charFreq := make(map[byte]int)
    windowStart := 0
    maxLength := 0

    for windowEnd := 0; windowEnd < len(s); windowEnd++ {
        rightChar := s[windowEnd]
        charFreq[rightChar]++

        // Shrink window until we have at most k distinct chars
        for len(charFreq) > k {
            leftChar := s[windowStart]
            charFreq[leftChar]--
            if charFreq[leftChar] == 0 {
                delete(charFreq, leftChar)
            }
            windowStart++
        }

        currentLength := windowEnd - windowStart + 1
        if currentLength > maxLength {
            maxLength = currentLength
        }
    }

    return maxLength
}
// Time: O(n) | Space: O(k)
```

### Problem 5: Find All Anagrams in a String

```go
func findAnagrams(s string, p string) []int {
    result := []int{}
    if len(s) < len(p) {
        return result
    }

    pCount := [26]int{}
    sCount := [26]int{}

    for i := 0; i < len(p); i++ {
        pCount[p[i]-'a']++
    }

    for i := 0; i < len(s); i++ {
        sCount[s[i]-'a']++

        // Remove element going out of window
        if i >= len(p) {
            sCount[s[i-len(p)]-'a']--
        }

        // Compare window with pattern
        if sCount == pCount {
            result = append(result, i-len(p)+1)
        }
    }

    return result
}
// Time: O(n) | Space: O(1) — fixed 26-size array
```

**Pro Tip:** Using `[26]int` arrays and comparing with `==` is faster than comparing HashMaps in Go!

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Maximum Sum Subarray of Size K | Easy | Fixed window, add right, remove left |
| 2 | Smallest Subarray with Sum ≥ S | Easy | Variable window, shrink while valid |
| 3 | Longest Substring Without Repeating | Medium | HashMap tracks last index, jump start |
| 4 | Longest Substring with K Distinct | Medium | HashMap + shrink when > k distinct |
| 5 | Minimum Window Substring | Hard | Track "formed" count vs "required" |
| 6 | Find All Anagrams in a String | Medium | Fixed window + frequency array |
| 7 | Permutation in String | Medium | Fixed window size = pattern length |
| 8 | Fruit Into Baskets | Medium | Longest subarray with at most 2 distinct |
| 9 | Max Consecutive Ones III | Medium | Variable window, at most k zeros |
| 10 | Sliding Window Maximum | Hard | Monotonic deque (see Pattern 16) |

---

## Hints

1. **Hint:** Fixed or Variable? If the problem gives you window size `k`, it's fixed. If it says "smallest/longest", it's variable.
2. **Hint:** For "longest" → expand first, then shrink when invalid. For "shortest" → shrink while valid.
3. **Hint:** In Go, use `map` for variable character sets and `[26]int` for lowercase English letters (faster).
4. **Hint:** Always `delete(charFreq, key)` when count reaches 0 — this keeps `len(charFreq)` accurate.
5. **Hint:** The window always represents a valid or candidate answer. Never let it represent garbage.

---

## Complexity Analysis

| Problem Type | Time | Space |
|-------------|------|-------|
| Fixed Window | O(n) | O(1) |
| Variable Window | O(n) | O(1) or O(k) |
| Window + HashMap | O(n) | O(k) where k = distinct elements |

---

[← Two Pointers](01-two-pointers.md) | [Back to Index](../README.md) | [Next: Fast & Slow Pointers →](03-fast-slow-pointers.md)
