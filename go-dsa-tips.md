# Go-Specific DSA Tips & Tricks

## Go Data Structures for DSA

### Slice as Stack

```go
// Push
stack = append(stack, val)

// Pop
top := stack[len(stack)-1]
stack = stack[:len(stack)-1]

// Peek
top := stack[len(stack)-1]

// Empty check
if len(stack) == 0 { }
```

### Slice as Queue

```go
// Enqueue
queue = append(queue, val)

// Dequeue
front := queue[0]
queue = queue[1:]

// Empty check
if len(queue) == 0 { }
```

> **Performance Note:** `queue = queue[1:]` doesn't free memory. For large queues, consider using `container/list` or a ring buffer.

### Map (HashMap)

```go
m := make(map[string]int)

// Insert
m["key"] = 42

// Lookup with existence check
val, exists := m["key"]
if exists { }

// Delete
delete(m, "key")

// Iterate (random order!)
for key, val := range m {
    fmt.Println(key, val)
}

// Count pattern
freq := make(map[int]int)
for _, num := range nums {
    freq[num]++
}
```

### Set (using map)

```go
set := make(map[int]bool)   // or map[int]struct{} for less memory

// Add
set[42] = true

// Check membership
if set[42] { }

// Remove
delete(set, 42)

// Using struct{} for memory efficiency
set2 := make(map[int]struct{})
set2[42] = struct{}{}
_, exists := set2[42]
```

### Heap (Priority Queue)

```go
import "container/heap"

type MinHeap []int
func (h MinHeap) Len() int           { return len(h) }
func (h MinHeap) Less(i, j int) bool { return h[i] < h[j] }
func (h MinHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *MinHeap) Push(x interface{}) { *h = append(*h, x.(int)) }
func (h *MinHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

// Usage
h := &MinHeap{}
heap.Init(h)
heap.Push(h, 5)
heap.Push(h, 2)
min := heap.Pop(h).(int) // 2
peek := (*h)[0]           // Peek without popping
```

### Sorted Operations

```go
import "sort"

// Sort slice
sort.Ints(nums)
sort.Strings(strs)
sort.Float64s(floats)

// Custom sort
sort.Slice(intervals, func(i, j int) bool {
    return intervals[i][0] < intervals[j][0]
})

// Binary search in sorted slice
idx := sort.SearchInts(nums, target)
// Returns insertion point (like C++ lower_bound)

// Generic binary search
idx := sort.Search(len(nums), func(i int) bool {
    return nums[i] >= target
})
```

---

## Go-Specific Gotchas for DSA

### 1. Slice Sharing (CRITICAL!)

```go
// WRONG: Slices share underlying array
path := []int{1, 2, 3}
result = append(result, path)    // path may be modified later!

// CORRECT: Copy before saving
temp := make([]int, len(path))
copy(temp, path)
result = append(result, temp)

// Also correct: use append to a nil slice (creates new backing array)
result = append(result, append([]int{}, path...))
```

### 2. Map Iteration Order is Random

```go
// DON'T rely on map iteration order
for k, v := range m { } // Random order each time!

// If you need ordered keys:
keys := make([]int, 0, len(m))
for k := range m {
    keys = append(keys, k)
}
sort.Ints(keys)
for _, k := range keys {
    fmt.Println(k, m[k])
}
```

### 3. Integer Division Truncates Toward Zero

```go
// Go truncates toward zero
-7 / 2  // = -3 (not -4)
7 / -2  // = -3

// Ceiling division (positive numbers)
ceil := (a + b - 1) / b
```

### 4. String Immutability

```go
// Strings are immutable in Go
s := "hello"
// s[0] = 'H'  // COMPILE ERROR

// Convert to byte slice to modify
b := []byte(s)
b[0] = 'H'
s = string(b)

// String iteration
for i, ch := range s { } // ch is rune (int32)
for i := 0; i < len(s); i++ { } // s[i] is byte
```

### 5. Max/Min Int

```go
import "math"

math.MaxInt32   // 2^31 - 1 = 2147483647
math.MinInt32   // -2^31
math.MaxInt64   // 2^63 - 1
math.MinInt64   // -2^63
math.MaxFloat64
```

### 6. Go Has No Built-in Max/Min for Integers (Before Go 1.21)

```go
// Before Go 1.21, define your own:
func max(a, b int) int {
    if a > b { return a }
    return b
}

func min(a, b int) int {
    if a < b { return a }
    return b
}

func abs(a int) int {
    if a < 0 { return -a }
    return a
}

// Go 1.21+: built-in min() and max() for all ordered types
```

---

## Common Patterns in Go

### Grid Direction Helpers

```go
// 4 directions (up, down, left, right)
dirs := [][]int{{0, 1}, {0, -1}, {1, 0}, {-1, 0}}

// 8 directions (including diagonals)
dirs8 := [][]int{
    {0, 1}, {0, -1}, {1, 0}, {-1, 0},
    {1, 1}, {1, -1}, {-1, 1}, {-1, -1},
}

// Bounds checking
func inBounds(r, c, rows, cols int) bool {
    return r >= 0 && r < rows && c >= 0 && c < cols
}
```

### 2D Slice Initialization

```go
// Create m × n grid initialized to 0
grid := make([][]int, m)
for i := range grid {
    grid[i] = make([]int, n)
}

// Create with default value
grid := make([][]bool, m)
for i := range grid {
    grid[i] = make([]bool, n)
}
```

### Conversion Between Types

```go
// String ↔ Integer
import "strconv"
n, _ := strconv.Atoi("123")       // string → int
s := strconv.Itoa(123)             // int → string

// String ↔ Byte Slice
b := []byte("hello")
s := string(b)

// Rune ↔ Int
ch := 'a'                          // rune = int32
idx := ch - 'a'                    // 0
ch2 := rune('a' + 2)              // 'c'

// Byte arithmetic
digit := byte('5') - '0'           // 5
isLower := s[i] >= 'a' && s[i] <= 'z'
```

### Useful Built-in Functions

```go
import "strings"
strings.Contains(s, "sub")        // Check substring
strings.Split(s, ",")             // Split by delimiter
strings.Join(parts, ",")          // Join with delimiter
strings.Repeat("ab", 3)           // "ababab"
strings.ToLower(s)                // Lowercase
strings.Builder{}                 // Efficient string building

import "math/bits"
bits.OnesCount(uint(n))           // Count 1-bits
bits.Len(uint(n))                 // Number of bits needed
bits.LeadingZeros(uint(n))        // Leading zeros

import "unicode"
unicode.IsLetter(ch)
unicode.IsDigit(ch)
unicode.ToLower(ch)
```

---

## Performance Tips

### 1. Pre-allocate Slices

```go
// Slow: many reallocations
result := []int{}
for i := 0; i < n; i++ {
    result = append(result, i)
}

// Fast: one allocation
result := make([]int, 0, n) // pre-allocate capacity
for i := 0; i < n; i++ {
    result = append(result, i)
}
```

### 2. Use Arrays Instead of Maps for Small Fixed Domains

```go
// Slow: map for character frequency
freq := make(map[byte]int)

// Fast: array for lowercase letters
freq := [26]int{}
freq[s[i]-'a']++

// Bonus: arrays support == comparison
var a, b [26]int
if a == b { } // Direct comparison!
```

### 3. String Builder for Concatenation

```go
// Slow: string concatenation in loop
s := ""
for _, ch := range chars {
    s += string(ch) // Creates new string each time!
}

// Fast: strings.Builder
var sb strings.Builder
for _, ch := range chars {
    sb.WriteByte(ch)
}
s := sb.String()
```

### 4. Avoid Pointer Indirection in Hot Loops

```go
// Slower (pointer dereference)
for i := 0; i < len(*nums); i++ { }

// Faster (local copy)
n := *nums
for i := 0; i < len(n); i++ { }
```

---

## LeetCode-Specific Go Tips

1. **Global variables persist between test cases.** Always initialize inside the function.
2. **Use `sort.Search` as your binary search** — it returns the index where the function first becomes true.
3. **`container/heap` requires implementing 5 methods.** Keep the boilerplate ready to paste.
4. **For DFS with memoization**, use closures to capture the memo map:
   ```go
   memo := make(map[int]int)
   var dp func(n int) int
   dp = func(n int) int {
       if v, ok := memo[n]; ok { return v }
       // compute
       memo[n] = result
       return result
   }
   ```
5. **Go's `sort.Slice` is unstable.** Use `sort.SliceStable` if stability matters.
6. **No generics before Go 1.18.** Use `interface{}` and type assertion for generic data structures.

---

## Template: Quick Copy-Paste Snippets

### Binary Search

```go
lo, hi := 0, len(nums)-1
for lo <= hi {
    mid := lo + (hi-lo)/2
    if nums[mid] == target { return mid }
    if nums[mid] < target { lo = mid + 1 } else { hi = mid - 1 }
}
```

### BFS

```go
queue := []int{start}
visited := map[int]bool{start: true}
for len(queue) > 0 {
    node := queue[0]; queue = queue[1:]
    for _, next := range graph[node] {
        if !visited[next] { visited[next] = true; queue = append(queue, next) }
    }
}
```

### DFS

```go
var dfs func(node int)
visited := make(map[int]bool)
dfs = func(node int) {
    visited[node] = true
    for _, next := range graph[node] {
        if !visited[next] { dfs(next) }
    }
}
```

---

[Back to Index](README.md)
