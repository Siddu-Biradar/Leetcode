# Pattern 14: Topological Sort

## Theory

**Topological Sort** produces a linear ordering of vertices in a **Directed Acyclic Graph (DAG)** such that for every edge `u → v`, vertex `u` comes before `v`. Used for ordering with **dependencies**.

### Two Approaches

| Approach | Method | Cycle Detection |
|----------|--------|----------------|
| **Kahn's (BFS)** | Use in-degree, process nodes with 0 in-degree | If sorted nodes < total nodes → cycle |
| **DFS** | Post-order DFS, reverse at end | Back edge → cycle |

### Kahn's Algorithm (BFS)
1. Compute in-degree of every node
2. Add all nodes with in-degree 0 to a queue
3. For each node from queue: add to result, reduce in-degree of neighbors
4. If neighbor's in-degree becomes 0, add to queue

---

## How to Identify This Pattern

### Use Topological Sort when:
- ✅ "Course schedule" / prerequisites
- ✅ "Build order" / compilation order
- ✅ "Alien dictionary" — derive ordering
- ✅ Any problem with **dependencies** or **ordering constraints**
- ✅ "Is it possible to finish all courses?"

---

## Solved Problems with Explanations

### Problem 1: Course Schedule (LeetCode 207) — Can Finish?

```go
func canFinish(numCourses int, prerequisites [][]int) bool {
    // Build adjacency list and in-degree
    graph := make([][]int, numCourses)
    inDegree := make([]int, numCourses)

    for _, pre := range prerequisites {
        course, prereq := pre[0], pre[1]
        graph[prereq] = append(graph[prereq], course)
        inDegree[course]++
    }

    // Queue: all courses with no prerequisites
    queue := []int{}
    for i := 0; i < numCourses; i++ {
        if inDegree[i] == 0 {
            queue = append(queue, i)
        }
    }

    count := 0
    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]
        count++

        for _, neighbor := range graph[node] {
            inDegree[neighbor]--
            if inDegree[neighbor] == 0 {
                queue = append(queue, neighbor)
            }
        }
    }

    return count == numCourses // If not, there's a cycle
}
// Time: O(V + E) | Space: O(V + E)
```

### Problem 2: Course Schedule II (LeetCode 210) — Find Order

```go
func findOrder(numCourses int, prerequisites [][]int) []int {
    graph := make([][]int, numCourses)
    inDegree := make([]int, numCourses)

    for _, pre := range prerequisites {
        graph[pre[1]] = append(graph[pre[1]], pre[0])
        inDegree[pre[0]]++
    }

    queue := []int{}
    for i := 0; i < numCourses; i++ {
        if inDegree[i] == 0 {
            queue = append(queue, i)
        }
    }

    order := []int{}
    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]
        order = append(order, node)

        for _, neighbor := range graph[node] {
            inDegree[neighbor]--
            if inDegree[neighbor] == 0 {
                queue = append(queue, neighbor)
            }
        }
    }

    if len(order) != numCourses {
        return []int{} // Cycle detected
    }
    return order
}
```

### Problem 3: Alien Dictionary (LeetCode 269)

```go
func alienOrder(words []string) string {
    // Build graph from adjacent word pairs
    graph := make(map[byte][]byte)
    inDegree := make(map[byte]int)

    // Initialize all characters
    for _, word := range words {
        for i := 0; i < len(word); i++ {
            if _, exists := inDegree[word[i]]; !exists {
                inDegree[word[i]] = 0
            }
        }
    }

    // Compare adjacent words to derive edges
    for i := 0; i < len(words)-1; i++ {
        w1, w2 := words[i], words[i+1]
        minLen := len(w1)
        if len(w2) < minLen {
            minLen = len(w2)
        }

        // Edge case: "abc" before "ab" → invalid
        if len(w1) > len(w2) && w1[:minLen] == w2[:minLen] {
            return ""
        }

        for j := 0; j < minLen; j++ {
            if w1[j] != w2[j] {
                graph[w1[j]] = append(graph[w1[j]], w2[j])
                inDegree[w2[j]]++
                break // Only first difference matters
            }
        }
    }

    // Kahn's BFS
    queue := []byte{}
    for ch, deg := range inDegree {
        if deg == 0 {
            queue = append(queue, ch)
        }
    }

    result := []byte{}
    for len(queue) > 0 {
        ch := queue[0]
        queue = queue[1:]
        result = append(result, ch)

        for _, neighbor := range graph[ch] {
            inDegree[neighbor]--
            if inDegree[neighbor] == 0 {
                queue = append(queue, neighbor)
            }
        }
    }

    if len(result) != len(inDegree) {
        return "" // Cycle
    }
    return string(result)
}
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Course Schedule | Medium | Kahn's BFS, check count == numCourses |
| 2 | Course Schedule II | Medium | Same but record the order |
| 3 | Alien Dictionary | Hard | Build graph from adjacent word pairs |
| 4 | Minimum Height Trees | Medium | Trim leaves layer by layer |
| 5 | Parallel Courses | Medium | BFS layers = parallel rounds |

---

## Hints

1. **Hint:** Cycle means topological sort is impossible → return false/empty.
2. **Hint:** Kahn's is usually easier to implement than DFS-based topological sort.
3. **Hint:** In Alien Dictionary, compare ADJACENT words only, and only the FIRST different character matters.
4. **Hint:** The number of nodes with in-degree 0 at any point determines parallelism (tasks that can run simultaneously).

---

[← K-way Merge](13-k-way-merge.md) | [Back to Index](../README.md) | [Next: Dynamic Programming →](15-dynamic-programming.md)
