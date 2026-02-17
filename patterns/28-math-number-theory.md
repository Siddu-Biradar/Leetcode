# Pattern 28: Math & Number Theory

## Theory

Math-based problems test fundamentals that can't be solved with standard data structure patterns. They require recognizing mathematical properties.

### Core Concepts

| Concept | Key Formula / Idea |
|---------|-------------------|
| **GCD** | `gcd(a, b) = gcd(b, a%b)`, base: `gcd(a, 0) = a` |
| **LCM** | `lcm(a, b) = a * b / gcd(a, b)` |
| **Modular Arithmetic** | `(a+b)%m = ((a%m)+(b%m))%m` |
| **Sieve of Eratosthenes** | Find all primes up to N in O(N log log N) |
| **Fast Exponentiation** | Compute `a^n % m` in O(log n) |
| **Combinatorics** | `C(n,k) = n! / (k! × (n-k)!)` |
| **Modular Inverse** | `a^(-1) mod p = a^(p-2) mod p` (Fermat's) |

---

## How to Identify

- "Find GCD/LCM", "fraction simplification"
- "Count primes", "prime factorization"
- "Power of 2/3/4", "perfect square"
- "Count number of ways" with large answer → "return answer mod 10^9+7"
- "Factorial", "permutations count", "combinations count"
- "Water jug problem" → GCD
- "Happy number", "digit manipulation"

---

## Template 1: GCD & LCM

```go
func gcd(a, b int) int {
    for b != 0 {
        a, b = b, a%b
    }
    return a
}

func lcm(a, b int) int {
    return a / gcd(a, b) * b // Divide first to avoid overflow
}

// GCD of array
func gcdArray(nums []int) int {
    result := nums[0]
    for _, n := range nums[1:] {
        result = gcd(result, n)
    }
    return result
}
```

---

## Template 2: Sieve of Eratosthenes

```go
func sieve(n int) []bool {
    isPrime := make([]bool, n+1)
    for i := 2; i <= n; i++ {
        isPrime[i] = true
    }

    for i := 2; i*i <= n; i++ {
        if isPrime[i] {
            for j := i * i; j <= n; j += i {
                isPrime[j] = false
            }
        }
    }
    return isPrime
}

// Collect primes into a list
func primesUpTo(n int) []int {
    ip := sieve(n)
    primes := []int{}
    for i := 2; i <= n; i++ {
        if ip[i] {
            primes = append(primes, i)
        }
    }
    return primes
}
```

---

## Template 3: Fast Exponentiation (Modular)

```go
func modPow(base, exp, mod int) int {
    result := 1
    base %= mod
    for exp > 0 {
        if exp%2 == 1 {
            result = result * base % mod
        }
        exp /= 2
        base = base * base % mod
    }
    return result
}
```

---

## Template 4: Combinatorics with Modular Inverse

```go
const MOD = 1_000_000_007

func nCr(n, r int) int {
    if r > n {
        return 0
    }
    // Precompute factorials
    fact := make([]int, n+1)
    fact[0] = 1
    for i := 1; i <= n; i++ {
        fact[i] = fact[i-1] * i % MOD
    }

    // C(n, r) = n! / (r! * (n-r)!) mod p
    // = n! * modInverse(r!) * modInverse((n-r)!) mod p
    return fact[n] * modPow(fact[r], MOD-2, MOD) % MOD * modPow(fact[n-r], MOD-2, MOD) % MOD
}
```

---

## Template 5: Prime Factorization

```go
func primeFactors(n int) map[int]int {
    factors := make(map[int]int)
    for d := 2; d*d <= n; d++ {
        for n%d == 0 {
            factors[d]++
            n /= d
        }
    }
    if n > 1 {
        factors[n]++ // n is a prime factor
    }
    return factors
}
```

---

## Solved Problems

### Problem 1: Count Primes (LC 204)

> Count primes less than `n`.

**Pattern:** Sieve of Eratosthenes.

```go
func countPrimes(n int) int {
    if n <= 2 {
        return 0
    }
    isPrime := make([]bool, n)
    for i := 2; i < n; i++ {
        isPrime[i] = true
    }
    for i := 2; i*i < n; i++ {
        if isPrime[i] {
            for j := i * i; j < n; j += i {
                isPrime[j] = false
            }
        }
    }
    count := 0
    for i := 2; i < n; i++ {
        if isPrime[i] {
            count++
        }
    }
    return count
}
```

**Complexity:** Time O(n log log n), Space O(n)

---

### Problem 2: Pow(x, n) (LC 50)

> Implement `pow(x, n)`.

**Pattern:** Fast exponentiation — square and multiply.

```go
func myPow(x float64, n int) float64 {
    if n < 0 {
        x = 1 / x
        n = -n
    }
    result := 1.0
    for n > 0 {
        if n%2 == 1 {
            result *= x
        }
        x *= x
        n /= 2
    }
    return result
}
```

**Why O(log n)?** We square `x` each step and halve `n`, so we only do log(n) multiplications.

**Complexity:** Time O(log n), Space O(1)

---

### Problem 3: GCD of Strings (LC 1071)

> Find the largest string `x` such that `x` divides both `str1` and `str2`.

**Pattern:** GCD — the answer length is `gcd(len(str1), len(str2))`.

```go
func gcdOfStrings(str1 string, str2 string) string {
    // If str1 + str2 != str2 + str1, no common divisor
    if str1+str2 != str2+str1 {
        return ""
    }
    g := gcd(len(str1), len(str2))
    return str1[:g]
}

func gcd(a, b int) int {
    for b != 0 {
        a, b = b, a%b
    }
    return a
}
```

**Complexity:** Time O(n + m), Space O(n + m)

---

### Problem 4: Unique Paths (LC 62) — Combinatorial Solution

> Count paths from top-left to bottom-right in m×n grid (only right/down).

**Pattern:** Combinatorics — we need exactly (m-1) downs and (n-1) rights = C(m+n-2, m-1).

```go
func uniquePaths(m int, n int) int {
    // C(m+n-2, m-1) = (m+n-2)! / ((m-1)! * (n-1)!)
    // Compute without overflow using incremental multiplication
    total := m + n - 2
    choose := m - 1
    if choose > total-choose {
        choose = total - choose
    }

    result := 1
    for i := 0; i < choose; i++ {
        result = result * (total - i) / (i + 1)
    }
    return result
}
```

**Complexity:** Time O(min(m, n)), Space O(1)

---

### Problem 5: Water and Jug Problem (LC 365)

> Can you measure exactly `target` liters using jugs of capacity `x` and `y`?

**Pattern:** Bézout's Identity — `target` is achievable iff `target % gcd(x, y) == 0` and `target <= x + y`.

```go
func canMeasureWater(x int, y int, target int) bool {
    if x+y < target {
        return false
    }
    if x == 0 || y == 0 {
        return target == 0 || x+y == target
    }
    return target%gcd(x, y) == 0
}
```

**Complexity:** Time O(log(min(x, y))), Space O(1)

---

### Problem 6: Fraction to Recurring Decimal (LC 166)

> Convert fraction `numerator/denominator` to string. Detect repeating part.

**Pattern:** Long division + HashMap to detect cycle.

```go
func fractionToDecimal(numerator int, denominator int) string {
    if numerator == 0 {
        return "0"
    }

    var sb strings.Builder

    // Handle sign
    if (numerator < 0) != (denominator < 0) {
        sb.WriteByte('-')
    }
    num := abs(numerator)
    den := abs(denominator)

    // Integer part
    sb.WriteString(strconv.Itoa(num / den))
    remainder := num % den
    if remainder == 0 {
        return sb.String()
    }

    sb.WriteByte('.')
    seen := make(map[int]int) // remainder → position in result

    for remainder != 0 {
        if pos, ok := seen[remainder]; ok {
            // Insert '(' at the position where this remainder first appeared
            result := sb.String()
            return result[:pos] + "(" + result[pos:] + ")"
        }
        seen[remainder] = sb.Len()
        remainder *= 10
        sb.WriteByte(byte('0' + remainder/den))
        remainder %= den
    }
    return sb.String()
}

func abs(a int) int {
    if a < 0 {
        return -a
    }
    return a
}
```

**Complexity:** Time O(denominator), Space O(denominator)

---

## Useful Math Tricks

```go
// Check if power of 2
func isPowerOfTwo(n int) bool {
    return n > 0 && n&(n-1) == 0
}

// Check if power of 3
func isPowerOfThree(n int) bool {
    // 3^19 = 1162261467 is largest power of 3 in int32
    return n > 0 && 1162261467%n == 0
}

// Check if perfect square
func isPerfectSquare(n int) bool {
    s := int(math.Sqrt(float64(n)))
    return s*s == n
}

// Integer square root (floor)
func isqrt(n int) int {
    return int(math.Sqrt(float64(n)))
}

// Sum of arithmetic series: 1+2+...+n
func sumN(n int) int {
    return n * (n + 1) / 2
}

// Digit sum
func digitSum(n int) int {
    sum := 0
    for n > 0 {
        sum += n % 10
        n /= 10
    }
    return sum
}

// Reverse a number
func reverseNum(n int) int {
    rev := 0
    for n > 0 {
        rev = rev*10 + n%10
        n /= 10
    }
    return rev
}
```

---

## Modular Arithmetic Rules

```
(a + b) % m = ((a % m) + (b % m)) % m
(a - b) % m = ((a % m) - (b % m) + m) % m
(a * b) % m = ((a % m) * (b % m)) % m
(a / b) % m = (a * modInverse(b, m)) % m    // Only when m is prime

// NEVER do: (a / b) % m = ((a % m) / (b % m)) % m   ← WRONG!
```

---

## Practice Problems

| Problem | Difficulty | Concept | LC # |
|---------|-----------|---------|------|
| Count Primes | Medium | Sieve | 204 |
| Pow(x, n) | Medium | Fast Exponentiation | 50 |
| GCD of Strings | Easy | GCD / Euclidean | 1071 |
| Water and Jug | Medium | Bézout's Identity | 365 |
| Unique Paths (math) | Medium | Combinatorics | 62 |
| Fraction to Recurring Decimal | Medium | Long Division | 166 |
| Excel Sheet Column Title/Number | Easy | Base conversion | 168/171 |
| Happy Number | Easy | Digit manipulation | 202 |
| Integer to Roman / Roman to Integer | Medium/Easy | Greedy | 12/13 |
| Palindrome Number | Easy | Reverse half | 9 |

---

## Hints

1. **"Return answer mod 10^9+7"** → Use modular arithmetic throughout, not just at the end
2. **GCD solves more problems than you think** — water jug, string divisibility, fraction simplification
3. **Sieve for range of primes**, trial division for single number primality
4. **Fast exponentiation** works for any associative operation (matrix exponentiation for Fibonacci in O(log n))
5. **Overflow danger:** multiply before dividing → use `a / gcd(a,b) * b` instead of `a * b / gcd(a,b)`
6. **Perfect square check:** compute sqrt and verify `s*s == n` (don't trust floating point directly)

---

[← Design Problems](27-design-problems.md) | [Index](../README.md) | [Divide & Conquer →](29-divide-and-conquer.md)
