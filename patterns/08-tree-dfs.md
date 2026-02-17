# Pattern 08: Tree DFS (Depth-First Search)

## Theory

**Tree DFS** explores as deep as possible before backtracking. There are three traversal orders:

| Order | Sequence | Use Case |
|-------|----------|----------|
| **Pre-order** | Root → Left → Right | Copy tree, serialize |
| **In-order** | Left → Root → Right | BST gives sorted order |
| **Post-order** | Left → Right → Root | Delete tree, calculate height |

### DFS vs BFS
- **DFS:** Uses recursion (stack). Better for path problems, depth calculations.
- **BFS:** Uses queue. Better for level-by-level processing, shortest path.

---

## How to Identify This Pattern

### Use Tree DFS when:
- ✅ "Path sum" / "all paths from root to leaf"
- ✅ "Maximum depth" / "diameter"
- ✅ "Is this a valid BST?"
- ✅ "Lowest Common Ancestor"
- ✅ "Serialize/Deserialize tree"
- ✅ Any problem requiring root-to-leaf exploration

---

## Template Code in Go

```go
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

// Template: DFS with return value
func dfs(root *TreeNode) int {
    if root == nil {
        return 0 // Base case
    }
    left := dfs(root.Left)
    right := dfs(root.Right)
    // Combine: use left, right, and root.Val
    return max(left, right) + 1
}

// Template: DFS with path tracking
func dfsPath(root *TreeNode, path []int, result *[][]int) {
    if root == nil {
        return
    }
    path = append(path, root.Val)

    if root.Left == nil && root.Right == nil {
        // Leaf: save a copy of the path
        temp := make([]int, len(path))
        copy(temp, path)
        *result = append(*result, temp)
        return
    }

    dfsPath(root.Left, path, result)
    dfsPath(root.Right, path, result)
}
```

---

## Solved Problems with Explanations

### Problem 1: Maximum Depth (LeetCode 104)

```go
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    left := maxDepth(root.Left)
    right := maxDepth(root.Right)
    return max(left, right) + 1
}

func max(a, b int) int { if a > b { return a }; return b }
// Time: O(n) | Space: O(h) where h = height
```

### Problem 2: Path Sum (LeetCode 112)

```go
func hasPathSum(root *TreeNode, targetSum int) bool {
    if root == nil {
        return false
    }
    // Leaf node: check if remaining sum equals node value
    if root.Left == nil && root.Right == nil {
        return targetSum == root.Val
    }
    // Subtract current value and recurse
    return hasPathSum(root.Left, targetSum-root.Val) ||
           hasPathSum(root.Right, targetSum-root.Val)
}
// Time: O(n) | Space: O(h)
```

### Problem 3: All Root-to-Leaf Paths with Target Sum (LeetCode 113)

```go
func pathSum(root *TreeNode, targetSum int) [][]int {
    result := [][]int{}
    findPaths(root, targetSum, []int{}, &result)
    return result
}

func findPaths(node *TreeNode, remaining int, currentPath []int, result *[][]int) {
    if node == nil {
        return
    }

    currentPath = append(currentPath, node.Val)

    // Check if leaf and sum matches
    if node.Left == nil && node.Right == nil && remaining == node.Val {
        pathCopy := make([]int, len(currentPath))
        copy(pathCopy, currentPath)
        *result = append(*result, pathCopy)
        return
    }

    findPaths(node.Left, remaining-node.Val, currentPath, result)
    findPaths(node.Right, remaining-node.Val, currentPath, result)
}
// Time: O(n²) worst case | Space: O(n)
```

**Important in Go:** You MUST copy the path slice before appending to result. Slices share underlying arrays, so without `copy`, all paths would reference the same data.

### Problem 4: Diameter of Binary Tree (LeetCode 543)

```go
func diameterOfBinaryTree(root *TreeNode) int {
    diameter := 0

    var height func(node *TreeNode) int
    height = func(node *TreeNode) int {
        if node == nil {
            return 0
        }
        left := height(node.Left)
        right := height(node.Right)

        // Diameter through this node = left height + right height
        if left+right > diameter {
            diameter = left + right
        }

        return max(left, right) + 1
    }

    height(root)
    return diameter
}
// Time: O(n) | Space: O(h)
```

### Problem 5: Validate BST (LeetCode 98)

```go
func isValidBST(root *TreeNode) bool {
    return validate(root, math.MinInt64, math.MaxInt64)
}

func validate(node *TreeNode, minVal, maxVal int) bool {
    if node == nil {
        return true
    }
    if node.Val <= minVal || node.Val >= maxVal {
        return false
    }
    return validate(node.Left, minVal, node.Val) &&
           validate(node.Right, node.Val, maxVal)
}
// Time: O(n) | Space: O(h)
```

### Problem 6: Lowest Common Ancestor (LeetCode 236)

```go
func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    if root == nil || root == p || root == q {
        return root
    }

    left := lowestCommonAncestor(root.Left, p, q)
    right := lowestCommonAncestor(root.Right, p, q)

    if left != nil && right != nil {
        return root // p and q are in different subtrees → root is LCA
    }
    if left != nil {
        return left
    }
    return right
}
// Time: O(n) | Space: O(h)
```

### Problem 7: Invert Binary Tree (LeetCode 226)

```go
func invertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }
    root.Left, root.Right = invertTree(root.Right), invertTree(root.Left)
    return root
}
// Time: O(n) | Space: O(h)
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Maximum Depth | Easy | `max(left, right) + 1` |
| 2 | Path Sum | Easy | Subtract value, check at leaf |
| 3 | Path Sum II (All Paths) | Medium | Track path, copy at leaf |
| 4 | Diameter of Binary Tree | Easy | `left + right` through each node |
| 5 | Validate BST | Medium | Pass valid range down |
| 6 | Lowest Common Ancestor | Medium | If both sides return non-nil → root |
| 7 | Symmetric Tree | Easy | Compare left.left with right.right |
| 8 | Flatten Tree to Linked List | Medium | Post-order: right, left, root |
| 9 | Construct from Preorder+Inorder | Medium | First of preorder = root, split inorder |
| 10 | Binary Tree Max Path Sum | Hard | Track through-node sum, update global |

---

## Hints

1. **Hint:** Most tree DFS problems follow the pattern: base case → recurse left → recurse right → combine.
2. **Hint:** When tracking paths, copy the slice in Go before appending to results.
3. **Hint:** For diameter/max-path-sum, use a closure variable to track the global answer while the function returns the local answer (height).
4. **Hint:** BST + In-order traversal = sorted sequence. Use this for validation.
5. **Hint:** For LCA, if a node matches p or q, return it immediately. Let the parent decide based on which sides returned non-nil.

---

[← Tree BFS](07-tree-bfs.md) | [Back to Index](../README.md) | [Next: Two Heaps →](09-two-heaps.md)
