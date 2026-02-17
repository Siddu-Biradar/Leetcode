# Pattern 17: Union Find (Disjoint Set Union)

## Theory

**Union Find** (DSU) manages a collection of disjoint sets. It supports two operations:
- **Find:** Determine which set an element belongs to (find root)
- **Union:** Merge two sets

### Optimizations
| Optimization | Effect |
|-------------|--------|
| **Path Compression** | In `Find`, point every node directly to root → nearly O(1) |
| **Union by Rank** | Attach shorter tree under taller one → keeps trees flat |

With both optimizations: amortized **O(α(n))** per operation (nearly constant).

---

## How to Identify This Pattern

### Use Union Find when:
- ✅ "Connected components"
- ✅ "Is there a path between X and Y?"
- ✅ "Redundant connection" (detect cycle in undirected graph)
- ✅ "Number of provinces / islands (matrix union)"
- ✅ Dynamic connectivity queries
- ✅ Kruskal's MST

---

## Template Code in Go

```go
type UnionFind struct {
    parent []int
    rank   []int
    count  int // Number of connected components
}

func NewUnionFind(n int) *UnionFind {
    parent := make([]int, n)
    rank := make([]int, n)
    for i := range parent {
        parent[i] = i // Each element is its own root
    }
    return &UnionFind{parent: parent, rank: rank, count: n}
}

func (uf *UnionFind) Find(x int) int {
    if uf.parent[x] != x {
        uf.parent[x] = uf.Find(uf.parent[x]) // Path compression
    }
    return uf.parent[x]
}

func (uf *UnionFind) Union(x, y int) bool {
    rootX, rootY := uf.Find(x), uf.Find(y)
    if rootX == rootY {
        return false // Already connected
    }

    // Union by rank
    if uf.rank[rootX] < uf.rank[rootY] {
        uf.parent[rootX] = rootY
    } else if uf.rank[rootX] > uf.rank[rootY] {
        uf.parent[rootY] = rootX
    } else {
        uf.parent[rootY] = rootX
        uf.rank[rootX]++
    }

    uf.count--
    return true
}

func (uf *UnionFind) Connected(x, y int) bool {
    return uf.Find(x) == uf.Find(y)
}
```

---

## Solved Problems with Explanations

### Problem 1: Number of Provinces (LeetCode 547)

```go
func findCircleNum(isConnected [][]int) int {
    n := len(isConnected)
    uf := NewUnionFind(n)

    for i := 0; i < n; i++ {
        for j := i + 1; j < n; j++ {
            if isConnected[i][j] == 1 {
                uf.Union(i, j)
            }
        }
    }

    return uf.count
}
// Time: O(n² α(n)) | Space: O(n)
```

### Problem 2: Redundant Connection (LeetCode 684)
**Find the edge that creates a cycle in an undirected graph.**

```go
func findRedundantConnection(edges [][]int) []int {
    n := len(edges)
    uf := NewUnionFind(n + 1) // 1-indexed nodes

    for _, edge := range edges {
        if !uf.Union(edge[0], edge[1]) {
            return edge // Union returned false → already connected → cycle
        }
    }

    return nil
}
// Time: O(n α(n)) | Space: O(n)
```

### Problem 3: Number of Islands (Union Find approach) (LeetCode 200)

```go
func numIslands(grid [][]byte) int {
    if len(grid) == 0 { return 0 }
    rows, cols := len(grid), len(grid[0])
    
    uf := NewUnionFind(rows * cols)
    waterCount := 0

    directions := [][]int{{1, 0}, {0, 1}} // Only right and down

    for r := 0; r < rows; r++ {
        for c := 0; c < cols; c++ {
            if grid[r][c] == '0' {
                waterCount++
                continue
            }
            for _, d := range directions {
                nr, nc := r+d[0], c+d[1]
                if nr < rows && nc < cols && grid[nr][nc] == '1' {
                    uf.Union(r*cols+c, nr*cols+nc)
                }
            }
        }
    }

    return uf.count - waterCount
}
```

### Problem 4: Accounts Merge (LeetCode 721)

```go
func accountsMerge(accounts [][]string) [][]string {
    uf := NewUnionFind(len(accounts))
    emailToID := make(map[string]int) // email → account index

    // Union accounts that share emails
    for i, account := range accounts {
        for _, email := range account[1:] {
            if id, exists := emailToID[email]; exists {
                uf.Union(i, id)
            }
            emailToID[email] = i
        }
    }

    // Group emails by root account
    rootEmails := make(map[int][]string)
    for email, id := range emailToID {
        root := uf.Find(id)
        rootEmails[root] = append(rootEmails[root], email)
    }

    // Build result
    result := [][]string{}
    for root, emails := range rootEmails {
        sort.Strings(emails)
        merged := append([]string{accounts[root][0]}, emails...)
        result = append(result, merged)
    }

    return result
}
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Number of Provinces | Medium | Union connected cities |
| 2 | Redundant Connection | Medium | Edge that fails Union = answer |
| 3 | Number of Islands | Medium | Union adjacent '1' cells |
| 4 | Accounts Merge | Medium | Union accounts sharing emails |
| 5 | Longest Consecutive Sequence | Medium | Union consecutive numbers |
| 6 | Graph Valid Tree | Medium | n-1 edges + no cycle |
| 7 | Smallest String with Swaps | Medium | Union swap indices, sort within groups |

---

## Hints

1. **Hint:** Path compression is critical — without it, `Find` can be O(n).
2. **Hint:** `Union` returning false means the elements were already connected → cycle detected.
3. **Hint:** For 2D grids, flatten `(row, col)` to `row * cols + col` as the node ID.
4. **Hint:** Track `count` of components: decrement on successful Union.
5. **Hint:** Union Find is better than DFS when you have dynamic queries ("is X connected to Y?" after each edge).

---

[← Monotonic Stack](16-monotonic-stack.md) | [Back to Index](../README.md) | [Next: Trie →](18-trie.md)
