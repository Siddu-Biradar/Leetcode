# Pattern 27: Design Problems

## Theory

Design problems test your ability to **combine data structures** to meet specific time/space constraints. They're among the most common interview questions at top companies.

### Key Approach
1. **Identify required operations** and their time complexity constraints
2. **Choose the right combination** of data structures
3. **Handle edge cases** (empty, capacity, duplicates)

### Common Combinations

| Requirement | Data Structure Combo |
|------------|---------------------|
| O(1) get + O(1) put with eviction | HashMap + Doubly Linked List (LRU) |
| O(1) push/pop/top + O(1) getMin | Two stacks (or stack of pairs) |
| O(1) insert/remove/getRandom | HashMap + Array (swap-delete) |
| O(1) amortized queue operations | Two stacks as queue |
| O(log n) add + O(1) findMedian | Two heaps |
| Prefix matching + word storage | Trie |

---

## How to Identify

- "Design a data structure that supports..."
- "Implement [LRU Cache, LFU Cache, Min Stack]"
- "All operations must be O(1)" or "O(log n)"
- "Implement an iterator"
- Functional requirements with performance constraints

---

## Solved Problems

### Problem 1: LRU Cache (LC 146) ⭐ TOP INTERVIEW QUESTION

> Design a data structure with `get(key)` and `put(key, value)` in O(1). When capacity is exceeded, evict the **least recently used** item.

**Key Insight:** HashMap gives O(1) lookup. Doubly linked list gives O(1) removal and O(1) move-to-front.

```go
type LRUCache struct {
    capacity   int
    cache      map[int]*Node
    head, tail *Node // Dummy nodes
}

type Node struct {
    key, val   int
    prev, next *Node
}

func Constructor(capacity int) LRUCache {
    head := &Node{}
    tail := &Node{}
    head.next = tail
    tail.prev = head
    return LRUCache{
        capacity: capacity,
        cache:    make(map[int]*Node),
        head:     head,
        tail:     tail,
    }
}

func (lru *LRUCache) Get(key int) int {
    if node, ok := lru.cache[key]; ok {
        lru.moveToFront(node)
        return node.val
    }
    return -1
}

func (lru *LRUCache) Put(key int, value int) {
    if node, ok := lru.cache[key]; ok {
        node.val = value
        lru.moveToFront(node)
        return
    }

    node := &Node{key: key, val: value}
    lru.cache[key] = node
    lru.addToFront(node)

    if len(lru.cache) > lru.capacity {
        removed := lru.removeLast()
        delete(lru.cache, removed.key)
    }
}

func (lru *LRUCache) addToFront(node *Node) {
    node.prev = lru.head
    node.next = lru.head.next
    lru.head.next.prev = node
    lru.head.next = node
}

func (lru *LRUCache) removeNode(node *Node) {
    node.prev.next = node.next
    node.next.prev = node.prev
}

func (lru *LRUCache) moveToFront(node *Node) {
    lru.removeNode(node)
    lru.addToFront(node)
}

func (lru *LRUCache) removeLast() *Node {
    node := lru.tail.prev
    lru.removeNode(node)
    return node
}
```

**Complexity:** Get O(1), Put O(1), Space O(capacity)

---

### Problem 2: Min Stack (LC 155)

> Design a stack that supports push, pop, top, and retrieving the minimum element, all in O(1).

**Key Insight:** Maintain a parallel stack that tracks the minimum at each level.

```go
type MinStack struct {
    stack    []int
    minStack []int
}

func Constructor() MinStack {
    return MinStack{}
}

func (ms *MinStack) Push(val int) {
    ms.stack = append(ms.stack, val)
    if len(ms.minStack) == 0 || val <= ms.minStack[len(ms.minStack)-1] {
        ms.minStack = append(ms.minStack, val)
    }
}

func (ms *MinStack) Pop() {
    top := ms.stack[len(ms.stack)-1]
    ms.stack = ms.stack[:len(ms.stack)-1]
    if top == ms.minStack[len(ms.minStack)-1] {
        ms.minStack = ms.minStack[:len(ms.minStack)-1]
    }
}

func (ms *MinStack) Top() int {
    return ms.stack[len(ms.stack)-1]
}

func (ms *MinStack) GetMin() int {
    return ms.minStack[len(ms.minStack)-1]
}
```

**Complexity:** All operations O(1), Space O(n)

---

### Problem 3: Insert Delete GetRandom O(1) (LC 380)

> Design a set that supports `insert`, `remove`, and `getRandom` all in O(1).

**Key Insight:** Array for O(1) random access. HashMap maps value → index. On remove, swap with last element.

```go
type RandomizedSet struct {
    nums   []int
    valIdx map[int]int // value → index in nums
}

func Constructor() RandomizedSet {
    return RandomizedSet{
        nums:   []int{},
        valIdx: make(map[int]int),
    }
}

func (rs *RandomizedSet) Insert(val int) bool {
    if _, exists := rs.valIdx[val]; exists {
        return false
    }
    rs.valIdx[val] = len(rs.nums)
    rs.nums = append(rs.nums, val)
    return true
}

func (rs *RandomizedSet) Remove(val int) bool {
    idx, exists := rs.valIdx[val]
    if !exists {
        return false
    }
    // Swap with last element
    last := rs.nums[len(rs.nums)-1]
    rs.nums[idx] = last
    rs.valIdx[last] = idx
    // Remove last
    rs.nums = rs.nums[:len(rs.nums)-1]
    delete(rs.valIdx, val)
    return true
}

func (rs *RandomizedSet) GetRandom() int {
    return rs.nums[rand.Intn(len(rs.nums))]
}
```

**Swap-delete trick:** Instead of shifting O(n), swap the target with the last element and pop — O(1).

**Complexity:** All operations O(1), Space O(n)

---

### Problem 4: Implement Queue using Stacks (LC 232)

> Implement FIFO queue using only two stacks.

```go
type MyQueue struct {
    inStack  []int
    outStack []int
}

func Constructor() MyQueue {
    return MyQueue{}
}

func (q *MyQueue) Push(x int) {
    q.inStack = append(q.inStack, x)
}

func (q *MyQueue) transfer() {
    if len(q.outStack) == 0 {
        for len(q.inStack) > 0 {
            top := q.inStack[len(q.inStack)-1]
            q.inStack = q.inStack[:len(q.inStack)-1]
            q.outStack = append(q.outStack, top)
        }
    }
}

func (q *MyQueue) Pop() int {
    q.transfer()
    val := q.outStack[len(q.outStack)-1]
    q.outStack = q.outStack[:len(q.outStack)-1]
    return val
}

func (q *MyQueue) Peek() int {
    q.transfer()
    return q.outStack[len(q.outStack)-1]
}

func (q *MyQueue) Empty() bool {
    return len(q.inStack) == 0 && len(q.outStack) == 0
}
```

**Complexity:** Push O(1), Pop O(1) amortized, Space O(n)

---

### Problem 5: LFU Cache (LC 460)

> Design a cache with `get` and `put` in O(1). Evict least frequently used; ties broken by LRU.

**Key Insight:** HashMap for O(1) lookup. Frequency → doubly linked list for each frequency level. Track minimum frequency.

```go
type LFUCache struct {
    capacity int
    minFreq  int
    keyNode  map[int]*LFUNode
    freqList map[int]*DLList
}

type LFUNode struct {
    key, val, freq int
    prev, next     *LFUNode
}

type DLList struct {
    head, tail *LFUNode
    size       int
}

func newDLList() *DLList {
    head := &LFUNode{}
    tail := &LFUNode{}
    head.next = tail
    tail.prev = head
    return &DLList{head: head, tail: tail}
}

func (dl *DLList) addToFront(node *LFUNode) {
    node.prev = dl.head
    node.next = dl.head.next
    dl.head.next.prev = node
    dl.head.next = node
    dl.size++
}

func (dl *DLList) removeNode(node *LFUNode) {
    node.prev.next = node.next
    node.next.prev = node.prev
    dl.size--
}

func (dl *DLList) removeLast() *LFUNode {
    if dl.size == 0 {
        return nil
    }
    node := dl.tail.prev
    dl.removeNode(node)
    return node
}

func Constructor(capacity int) LFUCache {
    return LFUCache{
        capacity: capacity,
        keyNode:  make(map[int]*LFUNode),
        freqList: make(map[int]*DLList),
    }
}

func (lfu *LFUCache) Get(key int) int {
    if node, ok := lfu.keyNode[key]; ok {
        lfu.increaseFreq(node)
        return node.val
    }
    return -1
}

func (lfu *LFUCache) Put(key int, value int) {
    if lfu.capacity == 0 {
        return
    }

    if node, ok := lfu.keyNode[key]; ok {
        node.val = value
        lfu.increaseFreq(node)
        return
    }

    if len(lfu.keyNode) >= lfu.capacity {
        // Evict LFU (and LRU among ties)
        list := lfu.freqList[lfu.minFreq]
        removed := list.removeLast()
        delete(lfu.keyNode, removed.key)
    }

    node := &LFUNode{key: key, val: value, freq: 1}
    lfu.keyNode[key] = node
    if lfu.freqList[1] == nil {
        lfu.freqList[1] = newDLList()
    }
    lfu.freqList[1].addToFront(node)
    lfu.minFreq = 1
}

func (lfu *LFUCache) increaseFreq(node *LFUNode) {
    oldFreq := node.freq
    lfu.freqList[oldFreq].removeNode(node)

    if lfu.freqList[oldFreq].size == 0 && oldFreq == lfu.minFreq {
        lfu.minFreq++
    }

    node.freq++
    if lfu.freqList[node.freq] == nil {
        lfu.freqList[node.freq] = newDLList()
    }
    lfu.freqList[node.freq].addToFront(node)
}
```

**Complexity:** Get O(1), Put O(1), Space O(capacity)

---

### Problem 6: Design Twitter (LC 355)

> Design a simplified Twitter: postTweet, getNewsFeed (10 most recent), follow, unfollow.

**Pattern:** HashMap + Linked List + Merge K Sorted Lists (heap).

```go
type Twitter struct {
    time      int
    tweets    map[int][]Tweet      // userId → tweets
    following map[int]map[int]bool // userId → set of followees
}

type Tweet struct {
    id, time int
}

func Constructor() Twitter {
    return Twitter{
        tweets:    make(map[int][]Tweet),
        following: make(map[int]map[int]bool),
    }
}

func (t *Twitter) PostTweet(userId int, tweetId int) {
    t.time++
    t.tweets[userId] = append(t.tweets[userId], Tweet{tweetId, t.time})
}

func (t *Twitter) GetNewsFeed(userId int) []int {
    // Collect all tweets from user and followees
    candidates := []Tweet{}
    candidates = append(candidates, t.tweets[userId]...)
    for followee := range t.following[userId] {
        candidates = append(candidates, t.tweets[followee]...)
    }

    // Sort by time descending
    sort.Slice(candidates, func(i, j int) bool {
        return candidates[i].time > candidates[j].time
    })

    result := []int{}
    for i := 0; i < len(candidates) && i < 10; i++ {
        result = append(result, candidates[i].id)
    }
    return result
}

func (t *Twitter) Follow(followerId int, followeeId int) {
    if t.following[followerId] == nil {
        t.following[followerId] = make(map[int]bool)
    }
    t.following[followerId][followeeId] = true
}

func (t *Twitter) Unfollow(followerId int, followeeId int) {
    delete(t.following[followerId], followeeId)
}
```

**Complexity:** PostTweet O(1), GetNewsFeed O(N log N) where N = total tweets from followees, Follow/Unfollow O(1)

---

## Practice Problems

| Problem | Difficulty | Key Data Structures | LC # |
|---------|-----------|-------------------|------|
| Min Stack | Medium | Stack + Min Stack | 155 |
| LRU Cache | Medium | HashMap + DLL | 146 |
| Insert Delete GetRandom O(1) | Medium | HashMap + Array | 380 |
| Queue using Stacks | Easy | Two Stacks | 232 |
| LFU Cache | Hard | HashMap + Freq DLL | 460 |
| Design Twitter | Medium | HashMap + Heap | 355 |
| Design HashMap | Easy | Array of Linked Lists | 706 |
| Design HashSet | Easy | Array of Linked Lists | 705 |
| Serialize/Deserialize Binary Tree | Hard | BFS/DFS + String | 297 |
| Flatten Nested List Iterator | Medium | Stack | 341 |
| Design Circular Queue | Medium | Array + Pointers | 622 |
| Design Browser History | Medium | Stack or DLL | 1472 |

---

## Hints

1. **O(1) lookup + O(1) ordered operations** → HashMap + Linked List
2. **O(1) random access** → Must have an array somewhere
3. **O(1) delete from middle** → Doubly linked list (with node reference from HashMap)
4. **Swap-to-last trick** for O(1) array deletion
5. **Two-stack trick** for amortized O(1) queue
6. **Design problems are about combining the RIGHT data structures** — analyze each operation's complexity requirement independently
7. **Always handle edge cases:** empty state, capacity = 0, duplicate operations

---

[← String Matching](26-string-matching.md) | [Index](../README.md) | [Math & Number Theory →](28-math-number-theory.md)
