# Pattern 03: Fast & Slow Pointers (Floyd's Tortoise and Hare)

## Theory

The **Fast & Slow Pointers** (also called Floyd's Cycle Detection) pattern uses two pointers moving at **different speeds** through a data structure. The slow pointer moves one step at a time, while the fast pointer moves two steps. This technique is primarily used for **cycle detection** and **finding the middle** of a linked list.

### Why It Works
- **Cycle Detection:** If there's a cycle, the fast pointer will eventually "lap" the slow pointer and they'll meet inside the cycle. Think of two runners on a circular track — the faster one will always catch up.
- **Finding Middle:** When the fast pointer reaches the end, the slow pointer is at the middle (fast traveled 2x distance).

### Mathematical Proof (Cycle Detection)
If the cycle starts at position `μ` and has length `λ`:
- When slow enters the cycle (at step μ), fast is at position `2μ` in the cycle
- They meet after `λ - (2μ - μ) mod λ = λ - μ mod λ` more steps
- To find cycle start: reset one pointer to head, move both at speed 1 — they meet at cycle start

---

## How to Identify This Pattern

### You should use Fast & Slow Pointers when:
- ✅ Problem involves a **cycle** in a linked list or array
- ✅ Need to find the **middle** of a linked list (in one pass)
- ✅ Need to find if a linked list is a **palindrome**
- ✅ Need to find the **start of a cycle**
- ✅ Problem is about a **happy number** (digit cycle)
- ✅ Need the **k-th from end** element

### Magic Words:
> "cycle", "circular", "loop", "middle node", "palindrome linked list", "happy number"

---

## Template Code in Go

### Linked List Node Definition

```go
type ListNode struct {
    Val  int
    Next *ListNode
}
```

### Template 1: Detect Cycle

```go
func hasCycle(head *ListNode) bool {
    slow, fast := head, head

    for fast != nil && fast.Next != nil {
        slow = slow.Next       // Move 1 step
        fast = fast.Next.Next  // Move 2 steps

        if slow == fast {
            return true // They met → cycle exists
        }
    }

    return false // Fast reached end → no cycle
}
```

### Template 2: Find Cycle Start

```go
func detectCycle(head *ListNode) *ListNode {
    slow, fast := head, head

    // Phase 1: Detect cycle
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next

        if slow == fast {
            // Phase 2: Find cycle start
            // Reset one pointer to head, move both at speed 1
            slow = head
            for slow != fast {
                slow = slow.Next
                fast = fast.Next
            }
            return slow // Cycle start
        }
    }

    return nil // No cycle
}
```

### Template 3: Find Middle of Linked List

```go
func middleNode(head *ListNode) *ListNode {
    slow, fast := head, head

    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
    }

    return slow // When fast is at end, slow is at middle
}
// For even-length list: returns the SECOND middle node
// For [1,2,3,4], returns node 3
// For [1,2,3,4,5], returns node 3
```

---

## Solved Problems with Explanations

### Problem 1: Linked List Cycle (LeetCode 141)

```go
func hasCycle(head *ListNode) bool {
    if head == nil || head.Next == nil {
        return false
    }

    slow, fast := head, head

    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next

        if slow == fast {
            return true
        }
    }

    return false
}
// Time: O(n) | Space: O(1)
```

### Problem 2: Linked List Cycle II — Find Cycle Start (LeetCode 142)

```go
func detectCycle(head *ListNode) *ListNode {
    slow, fast := head, head
    hasCycle := false

    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next

        if slow == fast {
            hasCycle = true
            break
        }
    }

    if !hasCycle {
        return nil
    }

    // Reset slow to head, keep fast at meeting point
    // Both move at speed 1 → meet at cycle start
    slow = head
    for slow != fast {
        slow = slow.Next
        fast = fast.Next
    }

    return slow
}
// Time: O(n) | Space: O(1)
```

**Why Phase 2 works:**
Let's say the distance from head to cycle start is `a`, and the distance from cycle start to meeting point is `b`, and the cycle length is `c`.
- Slow traveled: `a + b`
- Fast traveled: `a + b + c` (one full cycle more)
- Since fast = 2 × slow: `a + b + c = 2(a + b)` → `c = a + b` → `a = c - b`
- So, distance from head to cycle start (`a`) = distance from meeting point to cycle start (`c - b`).

### Problem 3: Happy Number (LeetCode 202)
**A happy number:** Replace the number by the sum of squares of its digits; repeat. If it reaches 1, it's happy. If it loops forever, it's not.

```go
func isHappy(n int) bool {
    slow, fast := n, n

    for {
        slow = sumOfSquares(slow)           // Move 1 step
        fast = sumOfSquares(sumOfSquares(fast)) // Move 2 steps

        if fast == 1 {
            return true
        }
        if slow == fast {
            return false // Cycle detected, not happy
        }
    }
}

func sumOfSquares(n int) int {
    sum := 0
    for n > 0 {
        digit := n % 10
        sum += digit * digit
        n /= 10
    }
    return sum
}
// Time: O(log n) | Space: O(1)
```

**Key Insight:** This is not about a linked list! The sequence of numbers forms a "virtual linked list" — each number points to the next. If it cycles, it's not happy. Floyd's algorithm detects that cycle in O(1) space.

### Problem 4: Palindrome Linked List (LeetCode 234)

```go
func isPalindrome(head *ListNode) bool {
    if head == nil || head.Next == nil {
        return true
    }

    // Step 1: Find middle using fast & slow
    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
    }

    // Step 2: Reverse second half (slow is at middle)
    secondHalf := reverseList(slow)

    // Step 3: Compare first half and reversed second half
    first, second := head, secondHalf
    isPalin := true
    for second != nil {
        if first.Val != second.Val {
            isPalin = false
            break
        }
        first = first.Next
        second = second.Next
    }

    // Step 4: (Optional) Restore the list
    reverseList(secondHalf)

    return isPalin
}

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
// Time: O(n) | Space: O(1)
```

### Problem 5: Find the Duplicate Number (LeetCode 287)
**Given:** Array of n+1 integers where each integer is in [1, n]. One number is duplicated. Find it in O(1) space.

```go
func findDuplicate(nums []int) int {
    // Treat array as a linked list: index → value = next pointer
    // nums[0] → nums[nums[0]] → nums[nums[nums[0]]] → ...
    // A duplicate means two indices point to the same value → cycle!

    slow, fast := nums[0], nums[nums[0]]

    // Phase 1: Find meeting point
    for slow != fast {
        slow = nums[slow]
        fast = nums[nums[fast]]
    }

    // Phase 2: Find cycle entrance (the duplicate number)
    slow = 0
    for slow != fast {
        slow = nums[slow]
        fast = nums[fast]
    }

    return slow
}
// Time: O(n) | Space: O(1)
```

**Brilliant Insight:** The array `[1,3,4,2,2]` can be seen as a linked list:
- Index 0 → value 1, Index 1 → value 3, Index 3 → value 2, Index 2 → value 4, Index 4 → value 2 (back to index 2!)
- This creates a cycle, and the entrance to the cycle is the duplicate number.

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Linked List Cycle | Easy | fast & slow, check if they meet |
| 2 | Linked List Cycle II | Medium | Two phases: detect then find start |
| 3 | Middle of Linked List | Easy | Fast reaches end → slow at middle |
| 4 | Happy Number | Easy | Virtual linked list, detect cycle |
| 5 | Palindrome Linked List | Medium | Find middle + reverse second half |
| 6 | Find the Duplicate Number | Medium | Array as linked list, find cycle entrance |
| 7 | Circular Array Loop | Medium | Each element = step size, detect cycle |
| 8 | Reorder List | Medium | Find middle + reverse + merge |

---

## Hints

1. **Hint:** The loop condition is `fast != nil && fast.Next != nil` — this prevents nil pointer dereference when accessing `fast.Next.Next`.
2. **Hint:** For Finding Cycle Start, remember the math: reset ONE pointer to head and move BOTH at speed 1.
3. **Hint:** For Palindrome List, you need three skills: find middle (fast/slow), reverse list, compare.
4. **Hint:** For "Find Duplicate" — think of the array as a linked list where `nums[i]` is the "next" pointer.
5. **Hint:** Happy Number is NOT a linked list problem, but has the same cycle structure. Think abstract!

---

[← Sliding Window](02-sliding-window.md) | [Back to Index](../README.md) | [Next: Merge Intervals →](04-merge-intervals.md)
