# Pattern 18: Trie (Prefix Tree)

## Theory

A **Trie** is a tree data structure for efficiently storing and retrieving keys in a dataset of strings. Each node represents a character, and paths from root to nodes represent prefixes.

### Properties
- **Insert/Search/Delete:** O(m) where m = word length
- **Prefix Search:** O(p) where p = prefix length
- Space: O(ALPHABET_SIZE × m × n) where n = number of words

### Structure
```
Root → [a] → [p] → [p] → [l] → [e]*
                              → [s]*     (* = end of word)
     → [b] → [a] → [t]*
```

---

## How to Identify This Pattern

### Use Trie when:
- ✅ "Prefix matching" / autocomplete
- ✅ "Word search" in dictionary
- ✅ "Starts with" queries
- ✅ "Word break" problem
- ✅ XOR-based problems (binary trie)

---

## Template Code in Go

```go
type TrieNode struct {
    children [26]*TrieNode
    isEnd    bool
}

type Trie struct {
    root *TrieNode
}

func NewTrie() *Trie {
    return &Trie{root: &TrieNode{}}
}

func (t *Trie) Insert(word string) {
    node := t.root
    for _, ch := range word {
        idx := ch - 'a'
        if node.children[idx] == nil {
            node.children[idx] = &TrieNode{}
        }
        node = node.children[idx]
    }
    node.isEnd = true
}

func (t *Trie) Search(word string) bool {
    node := t.findNode(word)
    return node != nil && node.isEnd
}

func (t *Trie) StartsWith(prefix string) bool {
    return t.findNode(prefix) != nil
}

func (t *Trie) findNode(s string) *TrieNode {
    node := t.root
    for _, ch := range s {
        idx := ch - 'a'
        if node.children[idx] == nil {
            return nil
        }
        node = node.children[idx]
    }
    return node
}
```

---

## Solved Problems with Explanations

### Problem 1: Implement Trie (LeetCode 208)
(See template above — that IS the solution)

### Problem 2: Word Search II (LeetCode 212)
**Find all words from dictionary in a 2D grid.**

```go
type TrieNode struct {
    children [26]*TrieNode
    word     string // Store complete word at leaf
}

func findWords(board [][]byte, words []string) []string {
    // Build trie
    root := &TrieNode{}
    for _, word := range words {
        node := root
        for _, ch := range word {
            idx := ch - 'a'
            if node.children[idx] == nil {
                node.children[idx] = &TrieNode{}
            }
            node = node.children[idx]
        }
        node.word = word
    }

    rows, cols := len(board), len(board[0])
    result := []string{}

    var dfs func(r, c int, node *TrieNode)
    dfs = func(r, c int, node *TrieNode) {
        if r < 0 || r >= rows || c < 0 || c >= cols || board[r][c] == '#' {
            return
        }

        ch := board[r][c]
        next := node.children[ch-'a']
        if next == nil {
            return
        }

        if next.word != "" {
            result = append(result, next.word)
            next.word = "" // Avoid duplicates
        }

        board[r][c] = '#' // Mark visited
        dfs(r+1, c, next)
        dfs(r-1, c, next)
        dfs(r, c+1, next)
        dfs(r, c-1, next)
        board[r][c] = ch // Restore
    }

    for r := 0; r < rows; r++ {
        for c := 0; c < cols; c++ {
            dfs(r, c, root)
        }
    }

    return result
}
```

### Problem 3: Word Break (LeetCode 139)

```go
func wordBreak(s string, wordDict []string) bool {
    trie := NewTrie()
    for _, word := range wordDict {
        trie.Insert(word)
    }

    n := len(s)
    dp := make([]bool, n+1)
    dp[0] = true

    for i := 0; i < n; i++ {
        if !dp[i] {
            continue
        }
        node := trie.root
        for j := i; j < n; j++ {
            idx := s[j] - 'a'
            if node.children[idx] == nil {
                break
            }
            node = node.children[idx]
            if node.isEnd {
                dp[j+1] = true
            }
        }
    }

    return dp[n]
}
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Implement Trie | Medium | Array of 26 children per node |
| 2 | Word Search II | Hard | Trie + DFS on grid |
| 3 | Word Break | Medium | Trie + DP |
| 4 | Design Add and Search Words | Medium | DFS with '.' wildcard |
| 5 | Replace Words | Medium | Find shortest prefix in trie |
| 6 | Maximum XOR of Two Numbers | Medium | Binary trie (bit by bit) |

---

## Hints

1. **Hint:** Use `[26]*TrieNode` for lowercase English. Use `map[rune]*TrieNode` for arbitrary characters.
2. **Hint:** In Word Search II, Trie prunes the search — we abandon paths that don't match any word prefix.
3. **Hint:** Store the complete word at leaf nodes to avoid reconstructing it during search.
4. **Hint:** For wildcard search (`'.'`), do DFS through all children at that level.

---

[← Union Find](17-union-find.md) | [Back to Index](../README.md) | [Next: Bit Manipulation →](19-bit-manipulation.md)
