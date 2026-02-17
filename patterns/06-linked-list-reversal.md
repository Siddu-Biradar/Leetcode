# Pattern 06: In-place Linked List Reversal

## Theory

This pattern reverses nodes of a linked list **in-place** without extra memory. The key technique: for each node, point its `Next` to the **previous** node instead of the next one.

### Three-Pointer Technique
You need three pointers at all times:
- `prev` — the already reversed portion
- `curr` — the node being processed
- `next` — saved reference to the rest of the list (so we don't lose it)

```
Before: prev → nil    curr → [1] → [2] → [3] → nil
Step 1: Save next = curr.Next
Step 2: curr.Next = prev          (reverse the link)
Step 3: prev = curr               (advance prev)
Step 4: curr = next               (advance curr)
```

---

## How to Identify This Pattern

### Use Linked List Reversal when:
- ✅ "Reverse a linked list" (whole or partial)
- ✅ "Reverse every k nodes"
- ✅ "Reverse between positions m and n"
- ✅ "Swap nodes in pairs"
- ✅ Problem requires rearranging linked list node pointers

---

## Template Code in Go

```go
type ListNode struct {
    Val  int
    Next *ListNode
}

// Reverse entire linked list
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode
    curr := head

    for curr != nil {
        next := curr.Next  // Save next
        curr.Next = prev   // Reverse link
        prev = curr        // Advance prev
        curr = next        // Advance curr
    }

    return prev // New head
}
```

---

## Solved Problems with Explanations

### Problem 1: Reverse Linked List (LeetCode 206)

```go
// Iterative
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode
    curr := head

    for curr != nil {
        next := curr.Next
        curr.Next = prev
        prev = curr
        curr = next
    }

    return prev
}

// Recursive
func reverseListRecursive(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return head
    }

    newHead := reverseListRecursive(head.Next)
    head.Next.Next = head // The node after head should point back to head
    head.Next = nil       // head becomes the tail

    return newHead
}
// Time: O(n) | Space: O(1) iterative, O(n) recursive
```

### Problem 2: Reverse Linked List II — Between Positions m and n (LeetCode 92)

```go
func reverseBetween(head *ListNode, left int, right int) *ListNode {
    if head == nil || left == right {
        return head
    }

    dummy := &ListNode{Next: head}
    prev := dummy

    // Move prev to the node just before position 'left'
    for i := 1; i < left; i++ {
        prev = prev.Next
    }

    // 'curr' is the first node to reverse
    curr := prev.Next

    // Reverse 'right - left' links
    for i := 0; i < right-left; i++ {
        next := curr.Next
        curr.Next = next.Next
        next.Next = prev.Next
        prev.Next = next
    }

    return dummy.Next
}
// Time: O(n) | Space: O(1)
```

**How the reversal in place works:**
```
Example: 1 → 2 → 3 → 4 → 5, left=2, right=4
prev=1, curr=2

Iteration 1: next=3, 1→3→2→4→5    (3 inserted after prev)
Iteration 2: next=4, 1→4→3→2→5    (4 inserted after prev)
Result:       1 → 4 → 3 → 2 → 5  ✓
```

### Problem 3: Reverse Nodes in k-Group (LeetCode 25)

```go
func reverseKGroup(head *ListNode, k int) *ListNode {
    // Check if we have k nodes to reverse
    count := 0
    node := head
    for count < k {
        if node == nil {
            return head // Less than k nodes, don't reverse
        }
        node = node.Next
        count++
    }

    // Reverse first k nodes
    var prev *ListNode
    curr := head
    for i := 0; i < k; i++ {
        next := curr.Next
        curr.Next = prev
        prev = curr
        curr = next
    }

    // Recursively reverse next group and connect
    // 'head' is now the tail of the reversed group
    head.Next = reverseKGroup(curr, k)

    return prev // New head of this group
}
// Time: O(n) | Space: O(n/k) for recursion stack
```

### Problem 4: Swap Nodes in Pairs (LeetCode 24)

```go
func swapPairs(head *ListNode) *ListNode {
    dummy := &ListNode{Next: head}
    prev := dummy

    for prev.Next != nil && prev.Next.Next != nil {
        first := prev.Next
        second := prev.Next.Next

        // Swap
        first.Next = second.Next
        second.Next = first
        prev.Next = second

        prev = first // Move to next pair
    }

    return dummy.Next
}
// Time: O(n) | Space: O(1)
```

### Problem 5: Rotate List (LeetCode 61)

```go
func rotateRight(head *ListNode, k int) *ListNode {
    if head == nil || head.Next == nil || k == 0 {
        return head
    }

    // Find length and tail
    length := 1
    tail := head
    for tail.Next != nil {
        length++
        tail = tail.Next
    }

    k = k % length
    if k == 0 {
        return head
    }

    // Find the new tail (length - k - 1 steps from head)
    newTail := head
    for i := 0; i < length-k-1; i++ {
        newTail = newTail.Next
    }

    newHead := newTail.Next
    newTail.Next = nil
    tail.Next = head

    return newHead
}
// Time: O(n) | Space: O(1)
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Reverse Linked List | Easy | Three pointers: prev, curr, next |
| 2 | Reverse Linked List II | Medium | Use dummy node, insert after prev |
| 3 | Swap Nodes in Pairs | Medium | Reverse k=2 groups |
| 4 | Reverse K-Group | Hard | Check k nodes exist, then reverse |
| 5 | Rotate List | Medium | Connect tail→head, cut at length-k |
| 6 | Odd Even Linked List | Medium | Two pointers for odd/even lists |

---

## Hints

1. **Hint:** Use a **dummy node** when the head might change (e.g., Reverse Between). `dummy.Next` always gives the correct head.
2. **Hint:** Always save `curr.Next` BEFORE modifying `curr.Next`. Otherwise you lose the rest of the list.
3. **Hint:** Draw it out! Linked list reversal is much easier to understand with diagrams.
4. **Hint:** For partial reversal, track the "connection points" — the node before the reversed section and the node after.
5. **Hint:** For Rotate, `k % length` handles k > length. Then it's just "cut and reattach."

---

[← Cyclic Sort](05-cyclic-sort.md) | [Back to Index](../README.md) | [Next: Tree BFS →](07-tree-bfs.md)
