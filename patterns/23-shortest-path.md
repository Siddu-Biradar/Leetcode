# Pattern 23: Shortest Path Algorithms

## Theory

Shortest path problems ask for the minimum cost/distance between nodes in a **weighted graph**. The algorithm choice depends on graph properties:

| Algorithm | Graph Type | Negative Weights? | Time Complexity | Use Case |
|-----------|-----------|-------------------|-----------------|----------|
| **BFS** | Unweighted | N/A | O(V + E) | Shortest path in unweighted graph |
| **Dijkstra** | Weighted, non-negative | No | O((V + E) log V) | Single source, non-negative weights |
| **Bellman-Ford** | Weighted, any | Yes | O(V × E) | Single source, negative weights allowed |
| **Floyd-Warshall** | Weighted, any | Yes (no neg cycle) | O(V³) | All pairs shortest path |
| **0-1 BFS** | Weights 0 or 1 only | N/A | O(V + E) | Binary weight edges |

---

## How to Identify

- "Find shortest/minimum path between two nodes in a **weighted** graph"
- "Network delay time", "cheapest flights"
- "Minimum cost to reach destination"
- Graph with **non-negative edge weights** → **Dijkstra**
- Graph with **possible negative weights** → **Bellman-Ford**
- "All pairs" shortest distance → **Floyd-Warshall**
- "At most K stops/edges" → **Bellman-Ford** (iterate K times)

---

## Algorithm 1: Dijkstra's Algorithm

### Theory
- Works with **non-negative weights** only
- Uses a **min-heap** to always process the closest unvisited node
- Greedy: once a node is popped from heap, its shortest distance is finalized

### Template

```go
import "container/heap"

type Edge struct {
    to, weight int
}

type Item struct {
    node, dist int
}

type MinHeap []Item
func (h MinHeap) Len() int            { return len(h) }
func (h MinHeap) Less(i, j int) bool  { return h[i].dist < h[j].dist }
func (h MinHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }
func (h *MinHeap) Push(x interface{}) { *h = append(*h, x.(Item)) }
func (h *MinHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

func dijkstra(graph map[int][]Edge, src int, n int) []int {
    dist := make([]int, n)
    for i := range dist {
        dist[i] = math.MaxInt64
    }
    dist[src] = 0

    h := &MinHeap{{src, 0}}
    heap.Init(h)

    for h.Len() > 0 {
        curr := heap.Pop(h).(Item)
        if curr.dist > dist[curr.node] {
            continue // Skip outdated entry
        }
        for _, edge := range graph[curr.node] {
            newDist := dist[curr.node] + edge.weight
            if newDist < dist[edge.to] {
                dist[edge.to] = newDist
                heap.Push(h, Item{edge.to, newDist})
            }
        }
    }
    return dist
}
```

---

## Algorithm 2: Bellman-Ford

### Theory
- Works with **negative weights** (and detects negative cycles)
- Relaxes **all edges** V-1 times
- If any edge can still be relaxed after V-1 iterations → negative cycle exists

### Template

```go
func bellmanFord(edges [][]int, n, src int) []int {
    // edges[i] = [from, to, weight]
    dist := make([]int, n)
    for i := range dist {
        dist[i] = math.MaxInt64
    }
    dist[src] = 0

    // Relax all edges V-1 times
    for i := 0; i < n-1; i++ {
        updated := false
        for _, e := range edges {
            from, to, w := e[0], e[1], e[2]
            if dist[from] != math.MaxInt64 && dist[from]+w < dist[to] {
                dist[to] = dist[from] + w
                updated = true
            }
        }
        if !updated {
            break // Early termination
        }
    }

    // Check for negative cycle
    for _, e := range edges {
        from, to, w := e[0], e[1], e[2]
        if dist[from] != math.MaxInt64 && dist[from]+w < dist[to] {
            return nil // Negative cycle detected
        }
    }
    return dist
}
```

---

## Algorithm 3: Floyd-Warshall

### Theory
- Computes shortest path between **all pairs** of vertices
- Uses **3 nested loops**: try every vertex as an intermediate node
- Simple but O(V³) — good for small graphs (V ≤ 400)

### Template

```go
func floydWarshall(n int, edges [][]int) [][]int {
    const INF = math.MaxInt64 / 2
    dist := make([][]int, n)
    for i := range dist {
        dist[i] = make([]int, n)
        for j := range dist[i] {
            if i == j {
                dist[i][j] = 0
            } else {
                dist[i][j] = INF
            }
        }
    }

    for _, e := range edges {
        dist[e[0]][e[1]] = e[2]
        // dist[e[1]][e[0]] = e[2]  // Uncomment for undirected
    }

    // k = intermediate node
    for k := 0; k < n; k++ {
        for i := 0; i < n; i++ {
            for j := 0; j < n; j++ {
                if dist[i][k]+dist[k][j] < dist[i][j] {
                    dist[i][j] = dist[i][k] + dist[k][j]
                }
            }
        }
    }
    return dist
}
```

---

## Solved Problems

### Problem 1: Network Delay Time (LC 743)

> Given `n` nodes and `times[i] = [u, v, w]`, find the time it takes for all nodes to receive a signal sent from node `k`. Return -1 if impossible.

**Pattern:** Dijkstra — single source shortest path, non-negative weights.

```go
func networkDelayTime(times [][]int, n int, k int) int {
    graph := make(map[int][]Edge)
    for _, t := range times {
        graph[t[0]] = append(graph[t[0]], Edge{t[1], t[2]})
    }

    dist := make([]int, n+1)
    for i := range dist {
        dist[i] = math.MaxInt64
    }
    dist[k] = 0

    h := &MinHeap{{k, 0}}
    heap.Init(h)

    for h.Len() > 0 {
        curr := heap.Pop(h).(Item)
        if curr.dist > dist[curr.node] {
            continue
        }
        for _, e := range graph[curr.node] {
            nd := dist[curr.node] + e.weight
            if nd < dist[e.to] {
                dist[e.to] = nd
                heap.Push(h, Item{e.to, nd})
            }
        }
    }

    maxDist := 0
    for i := 1; i <= n; i++ {
        if dist[i] == math.MaxInt64 {
            return -1
        }
        if dist[i] > maxDist {
            maxDist = dist[i]
        }
    }
    return maxDist
}
```

**Complexity:** Time O((V + E) log V), Space O(V + E)

---

### Problem 2: Cheapest Flights Within K Stops (LC 787)

> Find cheapest price from `src` to `dst` with at most `k` stops.

**Pattern:** Bellman-Ford limited to K+1 iterations (K stops = K+1 edges).

```go
func findCheapestPrice(n int, flights [][]int, src int, dst int, k int) int {
    const INF = math.MaxInt64 / 2
    dist := make([]int, n)
    for i := range dist {
        dist[i] = INF
    }
    dist[src] = 0

    // K stops = K+1 edges, so iterate K+1 times
    for i := 0; i <= k; i++ {
        // Use copy to prevent using updates from this round
        temp := make([]int, n)
        copy(temp, dist)
        for _, f := range flights {
            from, to, price := f[0], f[1], f[2]
            if dist[from]+price < temp[to] {
                temp[to] = dist[from] + price
            }
        }
        dist = temp
    }

    if dist[dst] >= INF {
        return -1
    }
    return dist[dst]
}
```

**Why Bellman-Ford?** We need "at most K stops" — BF naturally limits relaxation rounds.

**Complexity:** Time O(K × E), Space O(V)

---

### Problem 3: Path With Minimum Effort (LC 1631)

> Grid where effort = max absolute difference in heights along the path. Find minimum effort path from top-left to bottom-right.

**Pattern:** Dijkstra on grid — "cost" is the max difference along the path.

```go
func minimumEffortPath(heights [][]int) int {
    rows, cols := len(heights), len(heights[0])
    dist := make([][]int, rows)
    for i := range dist {
        dist[i] = make([]int, cols)
        for j := range dist[i] {
            dist[i][j] = math.MaxInt64
        }
    }
    dist[0][0] = 0

    h := &GridHeap{{0, 0, 0}}
    heap.Init(h)
    dirs := [][]int{{0, 1}, {0, -1}, {1, 0}, {-1, 0}}

    for h.Len() > 0 {
        curr := heap.Pop(h).(GridItem)
        if curr.r == rows-1 && curr.c == cols-1 {
            return curr.effort
        }
        if curr.effort > dist[curr.r][curr.c] {
            continue
        }
        for _, d := range dirs {
            nr, nc := curr.r+d[0], curr.c+d[1]
            if nr >= 0 && nr < rows && nc >= 0 && nc < cols {
                diff := abs(heights[nr][nc] - heights[curr.r][curr.c])
                newEffort := max(curr.effort, diff)
                if newEffort < dist[nr][nc] {
                    dist[nr][nc] = newEffort
                    heap.Push(h, GridItem{nr, nc, newEffort})
                }
            }
        }
    }
    return 0
}

type GridItem struct {
    r, c, effort int
}
type GridHeap []GridItem
func (h GridHeap) Len() int            { return len(h) }
func (h GridHeap) Less(i, j int) bool  { return h[i].effort < h[j].effort }
func (h GridHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }
func (h *GridHeap) Push(x interface{}) { *h = append(*h, x.(GridItem)) }
func (h *GridHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}
```

**Complexity:** Time O(M × N × log(M × N)), Space O(M × N)

---

### Problem 4: Find the City With the Smallest Number of Neighbors (LC 1334)

> Find the city with smallest number of reachable cities within distance threshold.

**Pattern:** Floyd-Warshall — need all-pairs shortest path.

```go
func findTheCity(n int, edges [][]int, distanceThreshold int) int {
    const INF = math.MaxInt64 / 2
    dist := make([][]int, n)
    for i := range dist {
        dist[i] = make([]int, n)
        for j := range dist[i] {
            if i == j {
                dist[i][j] = 0
            } else {
                dist[i][j] = INF
            }
        }
    }

    for _, e := range edges {
        dist[e[0]][e[1]] = e[2]
        dist[e[1]][e[0]] = e[2]
    }

    for k := 0; k < n; k++ {
        for i := 0; i < n; i++ {
            for j := 0; j < n; j++ {
                if dist[i][k]+dist[k][j] < dist[i][j] {
                    dist[i][j] = dist[i][k] + dist[k][j]
                }
            }
        }
    }

    result, minCount := -1, n+1
    for i := 0; i < n; i++ {
        count := 0
        for j := 0; j < n; j++ {
            if i != j && dist[i][j] <= distanceThreshold {
                count++
            }
        }
        if count <= minCount {
            minCount = count
            result = i
        }
    }
    return result
}
```

**Complexity:** Time O(V³), Space O(V²)

---

## Practice Problems

| Problem | Difficulty | Algorithm | LC # |
|---------|-----------|-----------|------|
| Network Delay Time | Medium | Dijkstra | 743 |
| Cheapest Flights Within K Stops | Medium | Bellman-Ford | 787 |
| Path With Minimum Effort | Medium | Dijkstra | 1631 |
| Swim in Rising Water | Hard | Dijkstra / Binary Search + BFS | 778 |
| Find City With Smallest Neighbors | Medium | Floyd-Warshall | 1334 |
| Path With Maximum Probability | Medium | Dijkstra (max) | 1514 |
| Shortest Path in Binary Matrix | Medium | BFS (unweighted) | 1091 |
| Shortest Path Visiting All Nodes | Hard | BFS + Bitmask | 847 |

---

## Hints

1. **Non-negative weights?** → Always try Dijkstra first.
2. **Negative weights possible?** → Must use Bellman-Ford.
3. **"At most K edges/stops"** → Bellman-Ford with K iterations.
4. **All pairs needed + V ≤ 400?** → Floyd-Warshall.
5. **Grid shortest path with varying costs** → Dijkstra on grid cells.
6. **0-1 weights?** → Use deque-based BFS (0 → push front, 1 → push back).
7. **Dijkstra never revisits a finalized node** — skip if `curr.dist > dist[node]`.

---

## Complexity Summary

| Algorithm | Time | Space |
|-----------|------|-------|
| Dijkstra | O((V + E) log V) | O(V + E) |
| Bellman-Ford | O(V × E) | O(V) |
| Floyd-Warshall | O(V³) | O(V²) |
| 0-1 BFS | O(V + E) | O(V + E) |

---

[← Graph BFS/DFS](20-graph-bfs-dfs.md) | [Index](../README.md) | [Monotonic Queue →](24-monotonic-queue.md)
