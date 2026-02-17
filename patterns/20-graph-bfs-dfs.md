# Pattern 20: Graph BFS / DFS

## Theory

Graphs consist of **vertices** (nodes) and **edges**. Two fundamental traversal strategies:

| Traversal | Data Structure | Best For |
|-----------|---------------|----------|
| **BFS** | Queue | Shortest path (unweighted), level-by-level |
| **DFS** | Stack / Recursion | Explore all paths, cycle detection, connected components |

### Graph Representations

```go
// Adjacency List (most common, space efficient)
graph := make(map[int][]int)
graph[0] = []int{1, 2}
graph[1] = []int{0, 3}

// Adjacency Matrix (dense graphs)
matrix := [][]int{
    {0, 1, 1, 0},
    {1, 0, 0, 1},
    {1, 0, 0, 0},
    {0, 1, 0, 0},
}

// Edge List
edges := [][]int{{0,1}, {0,2}, {1,3}}
```

---

## How to Identify This Pattern

### Use Graph BFS when:
- ✅ "**Shortest path** in unweighted graph"
- ✅ "Minimum number of moves/steps"
- ✅ "**Number of islands** / connected components"
- ✅ "Level-by-level" in a graph

### Use Graph DFS when:
- ✅ "**All paths** from source to target"
- ✅ "**Detect cycle**"
- ✅ "Connected components"
- ✅ "**Clone graph**"
- ✅ Exploring exhaustively

---

## Template Code in Go

### BFS Template

```go
func bfs(graph map[int][]int, start int) []int {
    visited := make(map[int]bool)
    queue := []int{start}
    visited[start] = true
    order := []int{}

    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]
        order = append(order, node)

        for _, neighbor := range graph[node] {
            if !visited[neighbor] {
                visited[neighbor] = true
                queue = append(queue, neighbor)
            }
        }
    }

    return order
}
```

### DFS Template

```go
func dfs(graph map[int][]int, node int, visited map[int]bool) {
    visited[node] = true
    // Process node

    for _, neighbor := range graph[node] {
        if !visited[neighbor] {
            dfs(graph, neighbor, visited)
        }
    }
}
```

---

## Solved Problems with Explanations

### Problem 1: Number of Islands (LeetCode 200)

```go
func numIslands(grid [][]byte) int {
    if len(grid) == 0 {
        return 0
    }
    rows, cols := len(grid), len(grid[0])
    count := 0

    var dfs func(r, c int)
    dfs = func(r, c int) {
        if r < 0 || r >= rows || c < 0 || c >= cols || grid[r][c] == '0' {
            return
        }
        grid[r][c] = '0' // Mark visited by sinking
        dfs(r+1, c)
        dfs(r-1, c)
        dfs(r, c+1)
        dfs(r, c-1)
    }

    for r := 0; r < rows; r++ {
        for c := 0; c < cols; c++ {
            if grid[r][c] == '1' {
                count++
                dfs(r, c)
            }
        }
    }

    return count
}
// Time: O(m×n) | Space: O(m×n) recursion stack
```

### Problem 2: Clone Graph (LeetCode 133)

```go
type Node struct {
    Val       int
    Neighbors []*Node
}

func cloneGraph(node *Node) *Node {
    if node == nil {
        return nil
    }

    cloned := make(map[*Node]*Node)

    var dfs func(n *Node) *Node
    dfs = func(n *Node) *Node {
        if clone, exists := cloned[n]; exists {
            return clone
        }

        clone := &Node{Val: n.Val}
        cloned[n] = clone

        for _, neighbor := range n.Neighbors {
            clone.Neighbors = append(clone.Neighbors, dfs(neighbor))
        }

        return clone
    }

    return dfs(node)
}
// Time: O(V + E) | Space: O(V)
```

### Problem 3: Shortest Path in Binary Matrix (BFS) (LeetCode 1091)

```go
func shortestPathBinaryMatrix(grid [][]int) int {
    n := len(grid)
    if grid[0][0] == 1 || grid[n-1][n-1] == 1 {
        return -1
    }

    directions := [][]int{
        {-1,-1},{-1,0},{-1,1},{0,-1},{0,1},{1,-1},{1,0},{1,1},
    }

    queue := [][]int{{0, 0}}
    grid[0][0] = 1 // Mark visited
    distance := 1

    for len(queue) > 0 {
        levelSize := len(queue)
        for i := 0; i < levelSize; i++ {
            r, c := queue[0][0], queue[0][1]
            queue = queue[1:]

            if r == n-1 && c == n-1 {
                return distance
            }

            for _, d := range directions {
                nr, nc := r+d[0], c+d[1]
                if nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 0 {
                    grid[nr][nc] = 1
                    queue = append(queue, []int{nr, nc})
                }
            }
        }
        distance++
    }

    return -1
}
// Time: O(n²) | Space: O(n²)
```

### Problem 4: Course Schedule — Detect Cycle with DFS (LeetCode 207)

```go
func canFinish(numCourses int, prerequisites [][]int) bool {
    graph := make([][]int, numCourses)
    for _, pre := range prerequisites {
        graph[pre[1]] = append(graph[pre[1]], pre[0])
    }

    // 0: unvisited, 1: in current path, 2: fully processed
    state := make([]int, numCourses)

    var hasCycle func(node int) bool
    hasCycle = func(node int) bool {
        if state[node] == 1 { return true }  // Back edge → cycle
        if state[node] == 2 { return false } // Already processed

        state[node] = 1 // Mark as in-progress

        for _, next := range graph[node] {
            if hasCycle(next) {
                return true
            }
        }

        state[node] = 2 // Mark as complete
        return false
    }

    for i := 0; i < numCourses; i++ {
        if hasCycle(i) {
            return false
        }
    }

    return true
}
```

### Problem 5: Pacific Atlantic Water Flow (LeetCode 417)

```go
func pacificAtlantic(heights [][]int) [][]int {
    rows, cols := len(heights), len(heights[0])
    pacific := make([][]bool, rows)
    atlantic := make([][]bool, rows)
    for i := range pacific {
        pacific[i] = make([]bool, cols)
        atlantic[i] = make([]bool, cols)
    }

    var dfs func(r, c int, reachable [][]bool, prevHeight int)
    dfs = func(r, c int, reachable [][]bool, prevHeight int) {
        if r < 0 || r >= rows || c < 0 || c >= cols || 
           reachable[r][c] || heights[r][c] < prevHeight {
            return
        }
        reachable[r][c] = true
        dfs(r+1, c, reachable, heights[r][c])
        dfs(r-1, c, reachable, heights[r][c])
        dfs(r, c+1, reachable, heights[r][c])
        dfs(r, c-1, reachable, heights[r][c])
    }

    // DFS from Pacific borders (top and left)
    for c := 0; c < cols; c++ {
        dfs(0, c, pacific, 0)
        dfs(rows-1, c, atlantic, 0)
    }
    for r := 0; r < rows; r++ {
        dfs(r, 0, pacific, 0)
        dfs(r, cols-1, atlantic, 0)
    }

    result := [][]int{}
    for r := 0; r < rows; r++ {
        for c := 0; c < cols; c++ {
            if pacific[r][c] && atlantic[r][c] {
                result = append(result, []int{r, c})
            }
        }
    }
    return result
}
```

### Problem 6: Word Ladder — BFS Shortest Transformation (LeetCode 127)

```go
func ladderLength(beginWord string, endWord string, wordList []string) int {
    wordSet := make(map[string]bool)
    for _, w := range wordList {
        wordSet[w] = true
    }
    if !wordSet[endWord] {
        return 0
    }

    queue := []string{beginWord}
    visited := map[string]bool{beginWord: true}
    steps := 1

    for len(queue) > 0 {
        levelSize := len(queue)
        for i := 0; i < levelSize; i++ {
            word := queue[0]
            queue = queue[1:]

            if word == endWord {
                return steps
            }

            // Try changing each character
            wordBytes := []byte(word)
            for j := 0; j < len(wordBytes); j++ {
                original := wordBytes[j]
                for ch := byte('a'); ch <= 'z'; ch++ {
                    wordBytes[j] = ch
                    newWord := string(wordBytes)

                    if wordSet[newWord] && !visited[newWord] {
                        visited[newWord] = true
                        queue = append(queue, newWord)
                    }
                }
                wordBytes[j] = original
            }
        }
        steps++
    }

    return 0
}
// Time: O(M² × N) where M = word length, N = wordList size
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Number of Islands | Medium | DFS/BFS, sink visited cells |
| 2 | Clone Graph | Medium | DFS with HashMap for cloned nodes |
| 3 | Shortest Path in Binary Matrix | Medium | BFS with 8 directions |
| 4 | Course Schedule | Medium | Cycle detection: DFS coloring |
| 5 | Pacific Atlantic Water Flow | Medium | DFS from borders, reverse flow |
| 6 | Word Ladder | Hard | BFS on word transformations |
| 7 | Surrounded Regions | Medium | DFS from border 'O's |
| 8 | Rotting Oranges | Medium | Multi-source BFS |
| 9 | Walls and Gates | Medium | Multi-source BFS from gates |
| 10 | Graph Valid Tree | Medium | n-1 edges + all connected |

---

## Hints

1. **Hint:** For grid problems, "visited" can be done by modifying the grid itself (sink '1' to '0').
2. **Hint:** BFS guarantees shortest path in unweighted graphs. DFS does NOT.
3. **Hint:** For cycle detection in directed graphs, use 3-color DFS (white/gray/black).
4. **Hint:** Multi-source BFS: add ALL sources to queue initially (e.g., Rotting Oranges).
5. **Hint:** For "can water flow to ocean" — think REVERSE: start from ocean and flow inland (uphill).
6. **Hint:** Grid directions: `dirs := [][]int{{0,1},{0,-1},{1,0},{-1,0}}` — memorize this.

---

[← Bit Manipulation](19-bit-manipulation.md) | [Back to Index](../README.md) | [Next: Prefix Sum →](21-prefix-sum.md)
