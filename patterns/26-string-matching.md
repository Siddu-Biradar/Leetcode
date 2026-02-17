# Pattern 26: String Matching Algorithms

## Theory

String matching finds occurrences of a **pattern** `P` (length m) within a **text** `T` (length n).

| Algorithm | Time | Space | Key Idea |
|-----------|------|-------|----------|
| **Brute Force** | O(n × m) | O(1) | Check every position |
| **KMP** | O(n + m) | O(m) | Prefix function avoids re-scanning |
| **Rabin-Karp** | O(n + m) avg | O(1) | Rolling hash |
| **Z-Algorithm** | O(n + m) | O(n + m) | Z-array of longest matching prefix |

---

## How to Identify

- "Find pattern in string", "strStr()", "indexOf()"
- "Repeated substring pattern"
- "Shortest palindrome" (KMP on reversed string)
- "Count occurrences of pattern"
- Multiple pattern matching → Rabin-Karp or Aho-Corasick
- "Longest prefix which is also suffix" → KMP failure function directly

---

## Algorithm 1: KMP (Knuth-Morris-Pratt)

### Theory

KMP pre-processes the pattern to build a **failure function** (also called LPS — Longest Prefix Suffix). When a mismatch occurs, instead of going back to the start of the pattern, we jump to the position indicated by the failure function.

**LPS Array:** `lps[i]` = length of the longest proper prefix of `pattern[0..i]` which is also a suffix.

```
Pattern:  A B A B C
LPS:      0 0 1 2 0

Explanation:
  i=0: "A"       → no proper prefix = suffix → 0
  i=1: "AB"      → no match → 0
  i=2: "ABA"     → "A" is prefix and suffix → 1
  i=3: "ABAB"    → "AB" is prefix and suffix → 2
  i=4: "ABABC"   → no match → 0
```

### Template: Build LPS

```go
func buildLPS(pattern string) []int {
    m := len(pattern)
    lps := make([]int, m)
    length := 0 // Length of previous longest prefix suffix
    i := 1

    for i < m {
        if pattern[i] == pattern[length] {
            length++
            lps[i] = length
            i++
        } else {
            if length != 0 {
                length = lps[length-1] // Fall back
            } else {
                lps[i] = 0
                i++
            }
        }
    }
    return lps
}
```

### Template: KMP Search

```go
func kmpSearch(text, pattern string) []int {
    n, m := len(text), len(pattern)
    if m == 0 {
        return []int{0}
    }

    lps := buildLPS(pattern)
    matches := []int{}
    i, j := 0, 0 // i for text, j for pattern

    for i < n {
        if text[i] == pattern[j] {
            i++
            j++
        }
        if j == m {
            matches = append(matches, i-j) // Found match at index i-j
            j = lps[j-1]                    // Continue searching
        } else if i < n && text[i] != pattern[j] {
            if j != 0 {
                j = lps[j-1] // Use failure function
            } else {
                i++
            }
        }
    }
    return matches
}
```

---

## Algorithm 2: Rabin-Karp (Rolling Hash)

### Theory

Use a **hash function** to compare the pattern hash with rolling window hashes of the text. If hashes match, verify with string comparison (to handle collisions).

**Rolling Hash Formula:**
```
hash("abc") = a×p² + b×p¹ + c×p⁰   (mod M)

Rolling: remove leftmost char, add new rightmost char
hash("bcd") = (hash("abc") - a×p²) × p + d   (mod M)
```

### Template

```go
func rabinKarp(text, pattern string) []int {
    n, m := len(text), len(pattern)
    if m > n {
        return nil
    }

    const (
        base = 31
        mod  = 1_000_000_007
    )

    // Compute pattern hash and first window hash
    patHash, winHash := 0, 0
    power := 1 // base^(m-1)
    for i := 0; i < m; i++ {
        patHash = (patHash*base + int(pattern[i])) % mod
        winHash = (winHash*base + int(text[i])) % mod
        if i > 0 {
            power = (power * base) % mod
        }
    }

    matches := []int{}
    for i := 0; i <= n-m; i++ {
        if winHash == patHash && text[i:i+m] == pattern {
            matches = append(matches, i)
        }
        // Slide window
        if i < n-m {
            winHash = (winHash - int(text[i])*power%mod + mod) % mod
            winHash = (winHash*base + int(text[i+m])) % mod
        }
    }
    return matches
}
```

---

## Algorithm 3: Z-Algorithm

### Theory

The **Z-array** for string `s`: `z[i]` = length of the longest substring starting at `i` which is also a prefix of `s`.

**For pattern matching:** Concatenate `pattern + "$" + text`, compute Z-array. Wherever `z[i] == len(pattern)`, there's a match.

### Template

```go
func zFunction(s string) []int {
    n := len(s)
    z := make([]int, n)
    l, r := 0, 0

    for i := 1; i < n; i++ {
        if i < r {
            z[i] = min(r-i, z[i-l])
        }
        for i+z[i] < n && s[z[i]] == s[i+z[i]] {
            z[i]++
        }
        if i+z[i] > r {
            l, r = i, i+z[i]
        }
    }
    return z
}

func zSearch(text, pattern string) []int {
    combined := pattern + "$" + text
    z := zFunction(combined)
    m := len(pattern)
    matches := []int{}

    for i := m + 1; i < len(combined); i++ {
        if z[i] == m {
            matches = append(matches, i-m-1)
        }
    }
    return matches
}
```

---

## Solved Problems

### Problem 1: Find the Index of the First Occurrence (LC 28)

> Implement strStr(): return index of first occurrence of `needle` in `haystack`.

**Pattern:** KMP or built-in — but KMP is Interview-worthy.

```go
func strStr(haystack string, needle string) int {
    n, m := len(haystack), len(needle)
    if m == 0 {
        return 0
    }

    lps := buildLPS(needle)
    j := 0

    for i := 0; i < n; i++ {
        for j > 0 && haystack[i] != needle[j] {
            j = lps[j-1]
        }
        if haystack[i] == needle[j] {
            j++
        }
        if j == m {
            return i - m + 1
        }
    }
    return -1
}
```

**Complexity:** Time O(n + m), Space O(m)

---

### Problem 2: Repeated Substring Pattern (LC 459)

> Check if string `s` can be constructed by repeating a substring.

**Pattern:** KMP trick — if `s` has a period, `len(s) % (len(s) - lps[last])== 0`.

```go
func repeatedSubstringPattern(s string) bool {
    n := len(s)
    lps := buildLPS(s)
    length := lps[n-1] // Length of longest prefix-suffix

    // The repeating unit length is n - length
    // It must divide n and length must be > 0
    return length > 0 && n%(n-length) == 0
}
```

**Why it works:** If the LPS of the entire string has length `L`, then the string has a repeating unit of length `n - L`. If `n % (n - L) == 0`, the string is entirely made of this unit.

**Complexity:** Time O(n), Space O(n)

---

### Problem 3: Shortest Palindrome (LC 214)

> Find the shortest palindrome by adding characters to the **front** of `s`.

**Pattern:** KMP on `s + "#" + reverse(s)` — find longest palindromic prefix.

```go
func shortestPalindrome(s string) string {
    if len(s) <= 1 {
        return s
    }

    rev := reverse(s)
    combined := s + "#" + rev // '#' prevents overlap
    lps := buildLPS(combined)

    // lps[last] = length of longest prefix of s that is a palindrome
    palindromeLen := lps[len(combined)-1]

    // Add the non-palindrome suffix (reversed) to the front
    suffix := s[palindromeLen:]
    return reverse(suffix) + s
}

func reverse(s string) string {
    b := []byte(s)
    for i, j := 0, len(b)-1; i < j; i, j = i+1, j-1 {
        b[i], b[j] = b[j], b[i]
    }
    return string(b)
}
```

**Key Insight:** Concatenate `s + "#" + reverse(s)`. The LPS value at the end tells us the longest palindromic prefix. Everything after that needs to be reversed and prepended.

**Complexity:** Time O(n), Space O(n)

---

### Problem 4: Repeated String Match (LC 686)

> Given strings `a` and `b`, find the minimum number of times `a` must be repeated so that `b` is a substring.

**Pattern:** Rabin-Karp — repeat `a` enough times and search for `b`.

```go
func repeatedStringMatch(a string, b string) int {
    // Minimum repeats needed: ceil(len(b) / len(a))
    repeats := (len(b) + len(a) - 1) / len(a)

    // Build repeated string (try repeats and repeats+1)
    var sb strings.Builder
    for i := 0; i < repeats; i++ {
        sb.WriteString(a)
    }

    text := sb.String()
    if strings.Contains(text, b) {
        return repeats
    }

    text += a
    if strings.Contains(text, b) {
        return repeats + 1
    }

    return -1
}
```

**Why repeats+1?** The pattern might straddle the boundary of two copies.

**Complexity:** Time O(n + m), Space O(n)

---

## Practice Problems

| Problem | Difficulty | Algorithm | LC # |
|---------|-----------|-----------|------|
| Find First Occurrence (strStr) | Easy | KMP | 28 |
| Repeated Substring Pattern | Easy | KMP | 459 |
| Shortest Palindrome | Hard | KMP | 214 |
| Repeated String Match | Medium | Rabin-Karp | 686 |
| Longest Happy Prefix | Hard | KMP / Z-Algorithm | 1392 |
| Sum of Scores of Built Strings | Hard | Z-Algorithm | 2223 |

---

## Hints

1. **Single pattern matching** → KMP (most interview-friendly)
2. **Multiple pattern matching** → Rabin-Karp with multiple hashes
3. **"Longest prefix which is also suffix"** → KMP failure function directly
4. **"Repeated substring"** → KMP: check if `n % (n - lps[n-1]) == 0`
5. **"Shortest palindrome"** → KMP on `s + "#" + reverse(s)`
6. **Rabin-Karp** has worst-case O(nm) due to hash collisions — use two different hash functions to reduce collision probability
7. **Z-algorithm** is an alternative to KMP — sometimes simpler to implement and reason about

---

## Complexity Summary

| Algorithm | Preprocessing | Matching | Space |
|-----------|--------------|----------|-------|
| KMP | O(m) | O(n) | O(m) |
| Rabin-Karp | O(m) | O(n) avg, O(nm) worst | O(1) |
| Z-Algorithm | O(n + m) | Included | O(n + m) |

---

[← Segment Tree](25-segment-tree.md) | [Index](../README.md) | [Design Problems →](27-design-problems.md)
