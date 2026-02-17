# Pattern 07: Tree BFS (Breadth-First Search / Level Order Traversal)

## Theory

**Tree BFS** processes a tree level by level using a **queue**. It's also called **Level Order Traversal**. At each step:
1. Process all nodes at the current level
2. Add their children to the queue for the next level

### Why Use BFS Over DFS?
- When you need to process **level by level**
- When you need the **shortest path** in unweighted structures
- When the answer is **closer to the root** (BFS finds it first)

---

## How to Identify This Pattern

### Use Tree BFS when:
- ✅ "Level order traversal"
- ✅ "Minimum depth of tree"
- ✅ "Right side view", "Left side view"
- ✅ "Average of levels"
- ✅ "Zigzag level order"
- ✅ "Connect all level-order siblings"
- ✅ Any problem that processes tree **level by level**

---

## Template Code in Go

```go
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

func bfsTemplate(root *TreeNode) [][]int {
    result := [][]int{}
    if root == nil {
        return result
    }

    queue := []*TreeNode{root}

    for len(queue) > 0 {
        levelSize := len(queue) // Number of nodes at current level
        currentLevel := []int{}

        for i := 0; i < levelSize; i++ {
            node := queue[0]       // Dequeue
            queue = queue[1:]

            currentLevel = append(currentLevel, node.Val)

            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }

        result = append(result, currentLevel)
    }

    return result
}
```

**Critical:** The inner loop runs exactly `levelSize` times — this is what separates levels.

---

## Solved Problems with Explanations

### Problem 1: Binary Tree Level Order Traversal (LeetCode 102)

```go
func levelOrder(root *TreeNode) [][]int {
    result := [][]int{}
    if root == nil {
        return result
    }

    queue := []*TreeNode{root}

    for len(queue) > 0 {
        levelSize := len(queue)
        level := make([]int, 0, levelSize)

        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]
            level = append(level, node.Val)

            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }

        result = append(result, level)
    }

    return result
}
// Time: O(n) | Space: O(n)
```

### Problem 2: Zigzag Level Order Traversal (LeetCode 103)

```go
func zigzagLevelOrder(root *TreeNode) [][]int {
    result := [][]int{}
    if root == nil {
        return result
    }

    queue := []*TreeNode{root}
    leftToRight := true

    for len(queue) > 0 {
        levelSize := len(queue)
        level := make([]int, levelSize)

        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]

            // Determine index based on direction
            if leftToRight {
                level[i] = node.Val
            } else {
                level[levelSize-1-i] = node.Val
            }

            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }

        result = append(result, level)
        leftToRight = !leftToRight
    }

    return result
}
// Time: O(n) | Space: O(n)
```

### Problem 3: Binary Tree Right Side View (LeetCode 199)

```go
func rightSideView(root *TreeNode) []int {
    result := []int{}
    if root == nil {
        return result
    }

    queue := []*TreeNode{root}

    for len(queue) > 0 {
        levelSize := len(queue)

        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]

            // Last node in this level = rightmost = visible from right
            if i == levelSize-1 {
                result = append(result, node.Val)
            }

            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
    }

    return result
}
// Time: O(n) | Space: O(n)
```

### Problem 4: Minimum Depth of Binary Tree (LeetCode 111)

```go
func minDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }

    queue := []*TreeNode{root}
    depth := 0

    for len(queue) > 0 {
        depth++
        levelSize := len(queue)

        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]

            // First leaf node found = minimum depth
            if node.Left == nil && node.Right == nil {
                return depth
            }

            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
    }

    return depth
}
// Time: O(n) | Space: O(n)
```

**Why BFS is perfect here:** BFS explores level by level. The FIRST leaf node we encounter is guaranteed to be at the minimum depth. DFS might find a deep leaf first.

### Problem 5: Average of Levels in Binary Tree (LeetCode 637)

```go
func averageOfLevels(root *TreeNode) []float64 {
    result := []float64{}
    if root == nil {
        return result
    }

    queue := []*TreeNode{root}

    for len(queue) > 0 {
        levelSize := len(queue)
        levelSum := 0.0

        for i := 0; i < levelSize; i++ {
            node := queue[0]
            queue = queue[1:]
            levelSum += float64(node.Val)

            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }

        result = append(result, levelSum/float64(levelSize))
    }

    return result
}
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Level Order Traversal | Medium | Standard BFS template |
| 2 | Zigzag Level Order | Medium | Toggle direction each level |
| 3 | Right Side View | Medium | Last node of each level |
| 4 | Minimum Depth | Easy | Return at first leaf |
| 5 | Average of Levels | Easy | Sum / count per level |
| 6 | Level Order Bottom Up | Medium | Reverse the result |
| 7 | Populating Next Right Pointers | Medium | Connect siblings using queue |
| 8 | Largest Value in Each Row | Medium | Track max per level |

---

## Hints

1. **Hint:** In Go, use a slice as a queue: `append` to add, `queue[0]` + `queue = queue[1:]` to dequeue.
2. **Hint:** Always capture `levelSize := len(queue)` BEFORE the inner loop. The queue grows during the loop.
3. **Hint:** For "right side view" → it's just the LAST node processed at each level.
4. **Hint:** For "minimum depth" → BFS finds it at the first leaf, but DFS might not.
5. **Hint:** For performance in Go, pre-allocate the level slice: `make([]int, 0, levelSize)`.

---

[← Linked List Reversal](06-linked-list-reversal.md) | [Back to Index](../README.md) | [Next: Tree DFS →](08-tree-dfs.md)
