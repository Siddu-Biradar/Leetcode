# Pattern 10: Subsets / Backtracking

## Theory

**Backtracking** is a systematic way to explore **all possible solutions** by building candidates incrementally and abandoning (backtracking from) candidates that cannot lead to a valid solution.

### The Backtracking Framework
```
function backtrack(state, choices):
    if is_goal(state):
        record(state)
        return
    
    for choice in choices:
        if is_valid(choice):
            make(choice)           # Choose
            backtrack(new_state)    # Explore
            undo(choice)           # Un-choose (backtrack)
```

### Types of Problems

| Type | Description | Duplicates? |
|------|------------|-------------|
| **Subsets** | All possible subsets | Typically no |
| **Permutations** | All orderings | Order matters |
| **Combinations** | Choose k from n | Order doesn't matter |
| **Partition** | Split into valid groups | Depends |

---

## How to Identify This Pattern

### Use Backtracking when:
- ✅ "Generate **all** subsets/permutations/combinations"
- ✅ "Find **all** valid configurations" (N-Queens, Sudoku)
- ✅ "**Partition** string/array into valid parts"
- ✅ Input size is **small** (n ≤ 15-20)
- ✅ "Letter combinations of phone number"
- ✅ "Word search in grid"

---

## Template Code in Go

### Template 1: Subsets

```go
func subsets(nums []int) [][]int {
    result := [][]int{}
    
    var backtrack func(start int, current []int)
    backtrack = func(start int, current []int) {
        // Add current subset (every state is valid)
        temp := make([]int, len(current))
        copy(temp, current)
        result = append(result, temp)

        for i := start; i < len(nums); i++ {
            current = append(current, nums[i])  // Choose
            backtrack(i+1, current)               // Explore
            current = current[:len(current)-1]    // Un-choose
        }
    }

    backtrack(0, []int{})
    return result
}
```

### Template 2: Permutations

```go
func permute(nums []int) [][]int {
    result := [][]int{}
    used := make([]bool, len(nums))

    var backtrack func(current []int)
    backtrack = func(current []int) {
        if len(current) == len(nums) {
            temp := make([]int, len(current))
            copy(temp, current)
            result = append(result, temp)
            return
        }

        for i := 0; i < len(nums); i++ {
            if used[i] {
                continue
            }
            used[i] = true
            current = append(current, nums[i])
            backtrack(current)
            current = current[:len(current)-1]
            used[i] = false
        }
    }

    backtrack([]int{})
    return result
}
```

### Template 3: Combinations

```go
func combine(n int, k int) [][]int {
    result := [][]int{}

    var backtrack func(start int, current []int)
    backtrack = func(start int, current []int) {
        if len(current) == k {
            temp := make([]int, k)
            copy(temp, current)
            result = append(result, temp)
            return
        }

        for i := start; i <= n; i++ {
            current = append(current, i)
            backtrack(i+1, current)
            current = current[:len(current)-1]
        }
    }

    backtrack(1, []int{})
    return result
}
```

---

## Solved Problems with Explanations

### Problem 1: Subsets (LeetCode 78)

```go
func subsets(nums []int) [][]int {
    result := [][]int{}
    
    var backtrack func(start int, current []int)
    backtrack = func(start int, current []int) {
        temp := make([]int, len(current))
        copy(temp, current)
        result = append(result, temp)

        for i := start; i < len(nums); i++ {
            current = append(current, nums[i])
            backtrack(i+1, current)
            current = current[:len(current)-1]
        }
    }

    backtrack(0, []int{})
    return result
}
// Time: O(n × 2^n) | Space: O(n)
```

### Problem 2: Subsets II — With Duplicates (LeetCode 90)

```go
func subsetsWithDup(nums []int) [][]int {
    sort.Ints(nums) // MUST sort to handle duplicates
    result := [][]int{}
    
    var backtrack func(start int, current []int)
    backtrack = func(start int, current []int) {
        temp := make([]int, len(current))
        copy(temp, current)
        result = append(result, temp)

        for i := start; i < len(nums); i++ {
            // Skip duplicates at the same level
            if i > start && nums[i] == nums[i-1] {
                continue
            }
            current = append(current, nums[i])
            backtrack(i+1, current)
            current = current[:len(current)-1]
        }
    }

    backtrack(0, []int{})
    return result
}
// Time: O(n × 2^n) | Space: O(n)
```

### Problem 3: Permutations (LeetCode 46)

```go
func permute(nums []int) [][]int {
    result := [][]int{}
    used := make([]bool, len(nums))

    var backtrack func(current []int)
    backtrack = func(current []int) {
        if len(current) == len(nums) {
            temp := make([]int, len(nums))
            copy(temp, current)
            result = append(result, temp)
            return
        }

        for i := 0; i < len(nums); i++ {
            if used[i] {
                continue
            }
            used[i] = true
            current = append(current, nums[i])
            backtrack(current)
            current = current[:len(current)-1]
            used[i] = false
        }
    }

    backtrack([]int{})
    return result
}
// Time: O(n × n!) | Space: O(n)
```

### Problem 4: Combination Sum (LeetCode 39)
**Can reuse same number unlimited times.**

```go
func combinationSum(candidates []int, target int) [][]int {
    result := [][]int{}

    var backtrack func(start int, remaining int, current []int)
    backtrack = func(start int, remaining int, current []int) {
        if remaining == 0 {
            temp := make([]int, len(current))
            copy(temp, current)
            result = append(result, temp)
            return
        }
        if remaining < 0 {
            return
        }

        for i := start; i < len(candidates); i++ {
            current = append(current, candidates[i])
            backtrack(i, remaining-candidates[i], current) // i, not i+1 (reuse!)
            current = current[:len(current)-1]
        }
    }

    backtrack(0, target, []int{})
    return result
}
// Time: O(n^(T/M)) where T=target, M=min candidate | Space: O(T/M)
```

### Problem 5: N-Queens (LeetCode 51)

```go
func solveNQueens(n int) [][]string {
    result := [][]string{}
    board := make([][]byte, n)
    for i := range board {
        board[i] = make([]byte, n)
        for j := range board[i] {
            board[i][j] = '.'
        }
    }

    cols := make(map[int]bool)
    diag1 := make(map[int]bool) // row - col
    diag2 := make(map[int]bool) // row + col

    var backtrack func(row int)
    backtrack = func(row int) {
        if row == n {
            solution := make([]string, n)
            for i := range board {
                solution[i] = string(board[i])
            }
            result = append(result, solution)
            return
        }

        for col := 0; col < n; col++ {
            if cols[col] || diag1[row-col] || diag2[row+col] {
                continue // Not safe
            }

            board[row][col] = 'Q'
            cols[col] = true
            diag1[row-col] = true
            diag2[row+col] = true

            backtrack(row + 1)

            board[row][col] = '.'
            cols[col] = false
            diag1[row-col] = false
            diag2[row+col] = false
        }
    }

    backtrack(0)
    return result
}
```

### Problem 6: Word Search (LeetCode 79)

```go
func exist(board [][]byte, word string) bool {
    rows, cols := len(board), len(board[0])

    var backtrack func(r, c, idx int) bool
    backtrack = func(r, c, idx int) bool {
        if idx == len(word) {
            return true
        }
        if r < 0 || r >= rows || c < 0 || c >= cols || board[r][c] != word[idx] {
            return false
        }

        // Mark visited
        temp := board[r][c]
        board[r][c] = '#'

        // Explore all 4 directions
        found := backtrack(r+1, c, idx+1) ||
                 backtrack(r-1, c, idx+1) ||
                 backtrack(r, c+1, idx+1) ||
                 backtrack(r, c-1, idx+1)

        // Restore
        board[r][c] = temp

        return found
    }

    for r := 0; r < rows; r++ {
        for c := 0; c < cols; c++ {
            if backtrack(r, c, 0) {
                return true
            }
        }
    }
    return false
}
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Subsets | Medium | Every node in recursion tree is a valid subset |
| 2 | Subsets II (Duplicates) | Medium | Sort + skip `nums[i]==nums[i-1]` when `i>start` |
| 3 | Permutations | Medium | Use `used[]` boolean array |
| 4 | Permutations II | Medium | Sort + skip same-level duplicates |
| 5 | Combination Sum | Medium | `backtrack(i, ...)` — same element reusable |
| 6 | Combination Sum II | Medium | `backtrack(i+1, ...)` + sort + skip dups |
| 7 | Palindrome Partitioning | Medium | At each position, try all palindrome prefixes |
| 8 | N-Queens | Hard | Place row by row, check cols and diagonals |
| 9 | Word Search | Medium | DFS on grid with visited marking |
| 10 | Letter Combinations | Medium | Map digits to letters, BFS or DFS |

---

## Hints

1. **Hint:** Subsets vs Permutations: Subsets loop from `start` (no repeats). Permutations loop from `0` with `used[]`.
2. **Hint:** To handle duplicates: **sort first**, then skip `nums[i] == nums[i-1]` at the same recursion level.
3. **Hint:** Always **copy** the slice before appending to results in Go.
4. **Hint:** Combination Sum (reuse): recurse with `i` not `i+1`. Combination Sum II (no reuse): recurse with `i+1`.
5. **Hint:** Pruning is key — return early when remaining sum < 0 or when the state is clearly invalid.

---

[← Two Heaps](09-two-heaps.md) | [Back to Index](../README.md) | [Next: Binary Search →](11-binary-search.md)
