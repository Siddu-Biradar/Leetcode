# DSA Conquest Path: Easy → Hard Interview Roadmap

> **Goal:** Go from zero to interview-ready in a structured, progressive way.  
> **Time estimate:** 10–14 weeks (2–3 hours/day) or 6–8 weeks (4–5 hours/day)

---

## The 3 Phases

```
Phase 1: FOUNDATIONS (Weeks 1–4)         → Build muscle memory
Phase 2: PATTERN MASTERY (Weeks 5–8)     → Recognize & apply patterns
Phase 3: INTERVIEW SIMULATION (Weeks 9+) → Speed, communication, edge cases
```

---

## Phase 1: Foundations (Easy → Easy-Medium)

> **Goal:** Get comfortable with Go, basic data structures, and simple patterns.  
> **Solve:** 3–5 problems/day | **Difficulty:** Easy only first week, then Easy + some Medium

### Week 1: Arrays, Strings & HashMap Basics

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | Array basics | Two Sum (1), Best Time to Buy Stock (121) | Easy |
| 2 | Strings | Valid Anagram (242), Valid Palindrome (125) | Easy |
| 3 | HashMap | Two Sum (1), Ransom Note (383), Isomorphic Strings (205) | Easy |
| 4 | Two Pointers intro | Valid Palindrome (125), Reverse String (344), Squares of Sorted Array (977) | Easy |
| 5 | Prefix Sum intro | Running Sum (1480), Range Sum Query (303) | Easy |
| 6 | Review + re-solve any you struggled with | — | — |
| 7 | Rest or catch up | — | — |

**Pattern files:** [01-Two Pointers](patterns/01-two-pointers.md), [21-Prefix Sum](patterns/21-prefix-sum.md)

---

### Week 2: Sorting, Binary Search & Sliding Window

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | Sorting basics | Merge Sorted Array (88), Sort Colors (75) | Easy/Med |
| 2 | Binary Search basics | Binary Search (704), Search Insert Position (35) | Easy |
| 3 | Binary Search continued | First Bad Version (278), Guess Number (374) | Easy |
| 4 | Sliding Window (fixed) | Max Avg Subarray I (643), Contains Duplicate II (219) | Easy |
| 5 | Sliding Window (variable) | Longest Substring Without Repeating (3) | Medium |
| 6 | Two Pointers medium | Container With Most Water (11), Two Sum II (167) | Medium |
| 7 | Review + re-solve | — | — |

**Pattern files:** [11-Binary Search](patterns/11-binary-search.md), [02-Sliding Window](patterns/02-sliding-window.md)

---

### Week 3: Linked Lists, Stacks & Queues

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | Linked List basics | Reverse Linked List (206), Merge Two Sorted Lists (21) | Easy |
| 2 | Fast & Slow Pointers | Linked List Cycle (141), Middle of Linked List (876) | Easy |
| 3 | Linked List medium | Remove Nth From End (19), Linked List Cycle II (142) | Medium |
| 4 | Stack basics | Valid Parentheses (20), Min Stack (155) | Easy/Med |
| 5 | Stack continued | Daily Temperatures (739), Evaluate RPN (150) | Medium |
| 6 | Queue | Implement Queue using Stacks (232), Moving Average (346) | Easy |
| 7 | Review | — | — |

**Pattern files:** [03-Fast-Slow](patterns/03-fast-slow-pointers.md), [06-Linked List Reversal](patterns/06-linked-list-reversal.md), [16-Monotonic Stack](patterns/16-monotonic-stack.md)

---

### Week 4: Trees & Basic Recursion

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | Binary Tree DFS | Max Depth (104), Invert Tree (226) | Easy |
| 2 | Tree DFS continued | Same Tree (100), Symmetric Tree (101), Path Sum (112) | Easy |
| 3 | Tree BFS | Level Order Traversal (102), Min Depth (111) | Medium/Easy |
| 4 | BST basics | Validate BST (98), Search in BST (700), LCA of BST (235) | Easy/Med |
| 5 | Recursion | Power of Three (326), Fibonacci (509), Climbing Stairs (70) | Easy |
| 6 | Tree medium | Diameter of Binary Tree (543), Right Side View (199) | Medium |
| 7 | Review all 4 weeks | — | — |

**Pattern files:** [07-Tree BFS](patterns/07-tree-bfs.md), [08-Tree DFS](patterns/08-tree-dfs.md)

---

## Phase 1 Checkpoint ✅

By now you should be able to:
- [ ] Solve most Easy problems in < 20 minutes
- [ ] Recognize Two Pointers, Sliding Window, Binary Search, BFS/DFS patterns
- [ ] Write clean Go code with proper edge case handling
- [ ] Explain your time/space complexity

---

## Phase 2: Pattern Mastery (Medium → Medium-Hard)

> **Goal:** Master every major pattern. This is the core of interview prep.  
> **Solve:** 3–4 problems/day | **Difficulty:** Mostly Medium

### Week 5: Intervals, Cyclic Sort & Advanced Two Pointers

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | Merge Intervals | Merge Intervals (56), Insert Interval (57) | Medium |
| 2 | Intervals continued | Meeting Rooms II (253), Non-overlapping Intervals (435) | Medium |
| 3 | Cyclic Sort | Missing Number (268), Find All Disappeared (448) | Easy |
| 4 | Cyclic Sort hard | First Missing Positive (41), Find Duplicate (287) | Hard/Med |
| 5 | Three Pointers | Three Sum (15), Three Sum Closest (16) | Medium |
| 6 | Advanced Sliding Window | Minimum Window Substring (76), Longest Repeating Char Replacement (424) | Medium/Hard |
| 7 | Review | — | — |

**Pattern files:** [04-Merge Intervals](patterns/04-merge-intervals.md), [05-Cyclic Sort](patterns/05-cyclic-sort.md)

---

### Week 6: Backtracking, Heaps & Greedy

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | Subsets & Permutations | Subsets (78), Permutations (46) | Medium |
| 2 | Backtracking with constraints | Combination Sum (39), Subsets II (90) | Medium |
| 3 | Backtracking hard | N-Queens (51), Word Search (79) | Hard/Med |
| 4 | Heap / Top K | Kth Largest Element (215), Top K Frequent (347) | Medium |
| 5 | Two Heaps | Find Median from Data Stream (295) | Hard |
| 6 | Greedy | Jump Game (55), Jump Game II (45), Gas Station (134) | Medium |
| 7 | Greedy continued | Task Scheduler (621), Partition Labels (763) | Medium |

**Pattern files:** [10-Backtracking](patterns/10-subsets-backtracking.md), [12-Top K](patterns/12-top-k-elements.md), [09-Two Heaps](patterns/09-two-heaps.md), [22-Greedy](patterns/22-greedy.md)

---

### Week 7: Graphs

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | Graph BFS | Number of Islands (200), Flood Fill (733) | Medium/Easy |
| 2 | Graph DFS | Clone Graph (133), Pacific Atlantic (417) | Medium |
| 3 | BFS Shortest Path | Shortest Path Binary Matrix (1091), Rotting Oranges (994) | Medium |
| 4 | Topological Sort | Course Schedule (207), Course Schedule II (210) | Medium |
| 5 | Union Find | Number of Provinces (547), Redundant Connection (684) | Medium |
| 6 | Dijkstra | Network Delay Time (743), Path With Min Effort (1631) | Medium |
| 7 | Review graphs | — | — |

**Pattern files:** [20-Graph BFS/DFS](patterns/20-graph-bfs-dfs.md), [14-Topological Sort](patterns/14-topological-sort.md), [17-Union Find](patterns/17-union-find.md), [23-Shortest Path](patterns/23-shortest-path.md)

---

### Week 8: Dynamic Programming (The Big Boss)

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | 1D DP basics | Climbing Stairs (70), House Robber (198) | Easy/Med |
| 2 | 1D DP continued | House Robber II (213), Decode Ways (91) | Medium |
| 3 | 0/1 Knapsack | Partition Equal Subset Sum (416), Target Sum (494) | Medium |
| 4 | Unbounded Knapsack | Coin Change (322), Coin Change II (518) | Medium |
| 5 | String DP | Longest Common Subsequence (1143), Edit Distance (72) | Medium/Hard |
| 6 | LIS + Grid DP | LIS (300), Unique Paths (62), Min Path Sum (64) | Medium |
| 7 | State Machine DP | Best Time Buy/Sell Stock with Cooldown (309), Stock III (123) | Medium/Hard |

**Pattern file:** [15-Dynamic Programming](patterns/15-dynamic-programming.md)

---

## Phase 2 Checkpoint ✅

By now you should be able to:
- [ ] Solve most Medium problems in 20–30 minutes
- [ ] Identify which pattern to use within 2–3 minutes of reading a problem
- [ ] Write optimized solutions (not just brute force)
- [ ] Explain trade-offs between approaches

---

## Phase 3: Interview Simulation (Medium-Hard → Hard)

> **Goal:** Speed, hard problems, system design thinking, communication.  
> **Solve:** 2–3 problems/day + mock interviews

### Week 9: Advanced Data Structures

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | Trie | Implement Trie (208), Word Search II (212) | Med/Hard |
| 2 | Monotonic Queue | Sliding Window Maximum (239) | Hard |
| 3 | Segment Tree / BIT | Range Sum Query Mutable (307) | Medium |
| 4 | Design: LRU Cache | LRU Cache (146) | Medium |
| 5 | Design: more | LFU Cache (460), Insert Delete GetRandom O(1) (380) | Hard/Med |
| 6 | String Matching | Implement strStr - KMP (28), Repeated Substring (459) | Easy/Med |
| 7 | Review | — | — |

**Pattern files:** [18-Trie](patterns/18-trie.md), [24-Monotonic Queue](patterns/24-monotonic-queue.md), [25-Segment Tree](patterns/25-segment-tree.md), [27-Design](patterns/27-design-problems.md), [26-String Matching](patterns/26-string-matching.md)

---

### Week 10: Hard Problems & Mixed Practice

| Day | Topic | Problems (LC #) | Difficulty |
|-----|-------|-----------------|------------|
| 1 | Hard DP | Word Break II (140), Burst Balloons (312) | Hard |
| 2 | Hard Graph | Word Ladder (127), Alien Dictionary (269) | Hard |
| 3 | Hard Backtracking | Sudoku Solver (37), Palindrome Partitioning (131) | Hard/Med |
| 4 | Hard Tree | Serialize/Deserialize Tree (297), Binary Tree Max Path Sum (124) | Hard |
| 5 | Hard Array | Trapping Rain Water (42), Largest Rectangle Histogram (84) | Hard |
| 6 | Divide & Conquer | Median of Two Sorted Arrays (4), Count Smaller After Self (315) | Hard |
| 7 | Math/Bit | Reverse Bits (190), Power of Two (231), Fraction to Decimal (166) | Med |

**Pattern files:** [29-Divide & Conquer](patterns/29-divide-and-conquer.md), [19-Bit Manipulation](patterns/19-bit-manipulation.md), [28-Math](patterns/28-math-number-theory.md)

---

### Weeks 11–14: Mock Interviews & Spaced Review

| Activity | Frequency | How |
|----------|-----------|-----|
| **Mock interviews** | 3–4 per week | Use Pramp, Interviewing.io, or practice with a friend |
| **Random LeetCode** | 2 per day | Use "Pick Random" or company-tagged problems |
| **Revisit weak areas** | Daily | Re-solve problems you struggled with (no peeking!) |
| **Timed practice** | Daily | Set 25-min timer per problem (simulate interview) |
| **Explain solutions aloud** | Every problem | Practice talking through your approach |

---

## The Daily Routine (During Active Prep)

```
Morning (1 hour):
  → Solve 1 new problem (timer: 25 min attempt, then study solution)
  → Review 2 previously solved problems from memory

Evening (1–1.5 hours):
  → Solve 1–2 problems from current week's topic
  → Write brief notes: what pattern, what tripped you up

Weekend (2–3 hours):
  → 1 mock interview (45 min)
  → Review all problems from the week
  → Update your "weak spots" list
```

---

## Problem Solving Framework (Use This Every Time)

```
1. READ (2 min)
   → Understand inputs, outputs, constraints
   → Ask clarifying questions (in interview: ask interviewer)

2. IDENTIFY PATTERN (2 min)
   → What data structure? What's the input type?
   → What keywords signal which pattern?
   → Check: problem-identification-guide.md

3. PLAN (3 min)
   → State brute force approach + complexity
   → Propose optimized approach using the pattern
   → Walk through with a small example

4. CODE (15 min)
   → Write clean, readable code
   → Use meaningful variable names
   → Handle edge cases as you go

5. TEST (3 min)
   → Trace through your code with the given example
   → Test edge cases: empty, single element, duplicates, max/min

6. ANALYZE (1 min)
   → State time and space complexity
   → Can it be improved? (mention even if you don't implement)
```

---

## Company-Specific Focus

### FAANG / Top Tech

| Company | Focus Areas | Must-Know Problems |
|---------|------------|-------------------|
| **Google** | Graph, DP, Design, Math | Word Ladder, LRU Cache, Median of Two Arrays |
| **Amazon** | BFS/DFS, Design, Greedy | Number of Islands, LRU Cache, Meeting Rooms II |
| **Meta** | Arrays, Strings, Trees, Graphs | Valid Palindrome III, Binary Tree Paths, Subarray Sum |
| **Apple** | Arrays, Trees, Linked Lists | Merge Intervals, LCA, Reverse Linked List |
| **Microsoft** | DP, Trees, Design | Serialize Tree, Coin Change, Min Stack |
| **Startups** | Practical DS, Design, System | LRU Cache, Rate Limiter, Top K |

---

## Spaced Repetition Schedule

Don't just solve once and forget. Review using this schedule:

```
Day 1:   Solve problem
Day 2:   Re-solve from memory (no hints)
Day 7:   Re-solve again
Day 30:  Final review
```

**Track your problems** in a spreadsheet:

| Date | Problem | Pattern | Difficulty | Solved Clean? | Review Date |
|------|---------|---------|-----------|--------------|-------------|
| Feb 19 | Two Sum | HashMap | Easy | Yes | Feb 20 |
| Feb 19 | Three Sum | Two Pointers | Medium | Needed hint | Feb 20, Feb 26 |

---

## Red Flags: You're NOT Ready If...

- You can't solve Easy problems in < 15 minutes
- You can't identify the pattern within 3 minutes
- You're memorizing solutions instead of understanding patterns
- You can't explain your approach while coding
- You skip edge cases
- You can't analyze time/space complexity of your solution

---

## Green Flags: You're Ready When...

- You solve most Mediums in < 25 minutes
- You can identify the pattern instantly for 80%+ of problems
- You can explain WHY a pattern works, not just HOW
- You handle edge cases naturally
- You can discuss trade-offs between solutions
- You've done 5+ mock interviews and gotten positive feedback

---

## Total Problems Target

| Phase | Easy | Medium | Hard | Total |
|-------|------|--------|------|-------|
| Phase 1 (Weeks 1–4) | 40 | 20 | 0 | 60 |
| Phase 2 (Weeks 5–8) | 5 | 50 | 10 | 65 |
| Phase 3 (Weeks 9+) | 0 | 30 | 25 | 55 |
| **Total** | **45** | **100** | **35** | **180** |

> 180 well-understood problems > 500 problems you half-remember.

---

## Quick Links to All Pattern Files

| Beginner Patterns | Intermediate Patterns | Advanced Patterns |
|------|------|------|
| [01-Two Pointers](patterns/01-two-pointers.md) | [04-Merge Intervals](patterns/04-merge-intervals.md) | [09-Two Heaps](patterns/09-two-heaps.md) |
| [02-Sliding Window](patterns/02-sliding-window.md) | [05-Cyclic Sort](patterns/05-cyclic-sort.md) | [13-K-Way Merge](patterns/13-k-way-merge.md) |
| [03-Fast-Slow Pointers](patterns/03-fast-slow-pointers.md) | [10-Backtracking](patterns/10-subsets-backtracking.md) | [14-Topological Sort](patterns/14-topological-sort.md) |
| [06-Linked List Reversal](patterns/06-linked-list-reversal.md) | [11-Binary Search](patterns/11-binary-search.md) | [15-Dynamic Programming](patterns/15-dynamic-programming.md) |
| [07-Tree BFS](patterns/07-tree-bfs.md) | [12-Top K Elements](patterns/12-top-k-elements.md) | [17-Union Find](patterns/17-union-find.md) |
| [08-Tree DFS](patterns/08-tree-dfs.md) | [16-Monotonic Stack](patterns/16-monotonic-stack.md) | [18-Trie](patterns/18-trie.md) |
| [19-Bit Manipulation](patterns/19-bit-manipulation.md) | [20-Graph BFS/DFS](patterns/20-graph-bfs-dfs.md) | [23-Shortest Path](patterns/23-shortest-path.md) |
| [21-Prefix Sum](patterns/21-prefix-sum.md) | [22-Greedy](patterns/22-greedy.md) | [24-Monotonic Queue](patterns/24-monotonic-queue.md) |
| [28-Math](patterns/28-math-number-theory.md) | [29-Divide & Conquer](patterns/29-divide-and-conquer.md) | [25-Segment Tree](patterns/25-segment-tree.md) |
| | | [26-String Matching](patterns/26-string-matching.md) |
| | | [27-Design Problems](patterns/27-design-problems.md) |

**Guides:** [Problem Identification](problem-identification-guide.md) | [Complexity Cheatsheet](complexity-cheatsheet.md) | [Go DSA Tips](go-dsa-tips.md)

---

## During the Interview: Communication Scripts

### Opening (First 2 Minutes)

```
"Thanks for the problem. Let me make sure I understand it correctly.
So we're given [restate input], and we need to return [restate output].

A few clarifying questions:
 - Can the input be empty?
 - Are there duplicates?
 - What's the expected input size? (helps determine target complexity)
 - Should I optimize for time or space?"
```

### Proposing Your Approach (Minutes 2–5)

```
"My first thought is a brute force approach using [X], which would be O(n²).

But I notice [signal/keyword], which suggests we can use [pattern].
The idea is: [1-2 sentence explanation].

This would give us O(n) time and O(n) space. Shall I code this up?"
```

### While Coding (Minutes 5–20)

```
"I'm initializing [data structure] because..."
"Here I'm handling the edge case where..."
"This loop [does X] — let me walk through it with the example..."
```

### When You're Stuck

```
DON'T: Go silent for 2+ minutes.

DO say one of these:
 - "Let me think about this for a moment... I'm considering [X] vs [Y]"
 - "I'm stuck on [specific part]. Let me try a simpler version first."
 - "Could I get a small hint on [specific aspect]?"
 - "Let me draw out a small example to see the pattern."
```

### After Coding (Last 5 Minutes)

```
"Let me trace through with the example: [walk through step by step]
Edge cases I'd test: [empty input, single element, all same, etc.]
Time complexity: O(n), Space: O(n).
An alternative approach would be [X] with trade-off [Y]."
```

---

## 45-Minute Interview Breakdown

```
┌────────────────────────────────────────────────────┐
│ 0-2 min   │ Read + clarify questions               │
│ 2-5 min   │ Discuss approach + get buy-in           │
│ 5-25 min  │ Code the solution                       │
│ 25-30 min │ Test + debug                            │
│ 30-35 min │ Optimize or handle follow-ups           │
│ 35-40 min │ Complexity analysis                     │
│ 40-45 min │ Your questions to interviewer           │
└────────────────────────────────────────────────────┘
```

---

## Common Interview Follow-Ups (Be Ready For These)

| After You Solve... | They'll Often Ask... |
|--------------------|---------------------|
| O(n) space solution | "Can you do it in O(1) space?" |
| Recursive solution | "Can you do it iteratively?" |
| Single-threaded | "What if this needs to be thread-safe?" |
| Working solution | "What if input doesn't fit in memory?" |
| HashMap solution | "What if we can't use extra space?" |
| Array problem | "What if the array is a stream?" |
| Any problem | "What's the time/space complexity?" |
| Any problem | "What test cases would you write?" |

---

## Common Mistakes That Fail Interviews

### Coding Mistakes
| Mistake | Fix |
|---------|-----|
| Off-by-one errors | Always verify loop bounds with smallest example |
| Not handling empty/nil input | Add guard clause at the top of every function |
| Integer overflow | Use `int64` for products/sums, check constraints |
| Modifying input while iterating | Use a copy or iterate in reverse |
| Forgetting to return | Double-check all code paths return a value |
| Using wrong comparison (`<` vs `<=`) | Trace through boundary cases |

### Behavioral Mistakes
| Mistake | Fix |
|---------|-----|
| Jumping to code immediately | ALWAYS discuss approach first |
| Going silent when stuck | Think aloud — interviewers want to see your thought process |
| Arguing with interviewer's hints | Say "That's a great point, let me think about that" |
| Not testing your code | Always trace through at least one example |
| Saying "I've seen this before" | Instead say "This reminds me of [pattern], because [reason]" |
| Giving up too quickly | Try at least 2 different approaches before asking for help |

---

## Behavioral Interview Questions (STAR Method)

Most tech interviews include 1-2 behavioral rounds. Prepare 5–6 stories using **STAR**:

```
S - Situation: What was the context?
T - Task: What was your responsibility?
A - Action: What did YOU specifically do?
R - Result: What was the outcome? (Use numbers!)
```

### Must-Have Stories

| Category | Example Question | Your Story Should Show |
|----------|-----------------|----------------------|
| **Leadership** | "Tell me about a time you led a project" | Initiative, delegation, outcome |
| **Conflict** | "Describe a disagreement with a teammate" | Empathy, compromise, resolution |
| **Failure** | "Tell me about a time you failed" | Ownership, learning, improvement |
| **Technical Challenge** | "Hardest bug you've debugged" | Systematic approach, persistence |
| **Tight Deadline** | "Time you delivered under pressure" | Prioritization, focus, delivery |
| **Innovation** | "Time you improved a process" | Curiosity, impact, initiative |

---

## Pre-Interview Checklist

### Week Before
- [ ] Review all pattern templates (don't memorize — understand)
- [ ] Do 2–3 mock interviews (Pramp, Interviewing.io, or with a friend)
- [ ] Research the company (products, engineering blog, tech stack)
- [ ] Prepare 5–6 behavioral stories using STAR
- [ ] Prepare 3–4 questions to ask the interviewer
- [ ] Review your resume — be ready to discuss every bullet point

### Day Before
- [ ] Light review only — don't cram new problems
- [ ] Re-solve 3–5 of your best problems for confidence
- [ ] Prepare your environment (quiet room, stable internet, IDE ready)
- [ ] Set up screen sharing if remote
- [ ] Get enough sleep (8 hours)

### Day Of
- [ ] Eat well, stay hydrated
- [ ] Have water, pen & paper nearby
- [ ] Open IDE / coding environment 15 min early
- [ ] Take a few deep breaths — you've prepared, trust the process

---

## Questions to Ask Your Interviewer

**About the Team:**
- What does a typical day look like for engineers on your team?
- How is the team structured? How many engineers?
- What's the code review process like?

**About the Role:**
- What would my first project be?
- What's the biggest technical challenge the team is facing?
- How do you measure success for this role in the first 6 months?

**About Growth:**
- How does the promotion process work?
- Are there opportunities for mentorship?
- How does the team stay up to date with new technologies?

**About Culture:**
- What's one thing you'd change about working here?
- How does the team handle on-call rotations?
- What do you enjoy most about working here?

---

## Post-Interview Reflection Template

After every interview, fill this out (helps you improve):

```
Date:
Company:
Round: (Phone Screen / Onsite Round 1 / etc.)

Problem(s):
  1.
  2.

What went well:
  -
  -

What went wrong:
  -
  -

Pattern I should have recognized faster:

Things I should review:

Behavioral questions asked:
  1.
  2.

Overall feeling (1-5):

Follow-up needed: (thank you email, etc.)
```

---

## Useful Resources

### Practice Platforms
| Platform | Best For | Link |
|----------|---------|------|
| **LeetCode** | Problem practice, company tags | leetcode.com |
| **NeetCode** | Curated problem lists + video explanations | neetcode.io |
| **Blind 75** | The essential 75 problems | neetcode.io/practice |
| **Grind 75** | Customizable study plan | grind75.com |
| **Pramp** | Free mock interviews with peers | pramp.com |
| **Interviewing.io** | Anonymous mock interviews | interviewing.io |

### Learning
| Resource | Best For |
|----------|---------|
| **NeetCode YouTube** | Visual pattern explanations |
| **Abdul Bari YouTube** | Algorithm theory fundamentals |
| **Tech Interview Handbook** | End-to-end interview guide |
| **Go by Example** | Go syntax reference |
| **Effective Go** | Go best practices |

### Books
| Book | Focus |
|------|-------|
| *Cracking the Coding Interview* | Classic interview prep |
| *Elements of Programming Interviews* | Hard problem practice |
| *Grokking the Coding Interview* | Pattern-based learning |
| *Algorithm Design Manual* (Skiena) | Deep algorithm understanding |

---

## Salary Negotiation Basics

After you get the offer:

1. **Never accept immediately** — "Thank you! I'm very excited. Can I have a few days to review the details?"
2. **Always negotiate** — Companies expect it. You won't lose the offer.
3. **Use competing offers** — "I have another offer at $X. Can you match/beat?"
4. **Negotiate total comp** — Base, bonus, RSUs, signing bonus, WFH days
5. **Know your market rate** — Use levels.fyi, Glassdoor, Blind

```
Script: "I'm really excited about this role. Based on my research and 
competing offers, I was hoping we could get closer to [target]. 
Is there flexibility on [base/equity/signing bonus]?"
```

---

[Back to Index](README.md)
