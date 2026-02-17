# Pattern 04: Merge Intervals

## Theory

The **Merge Intervals** pattern deals with overlapping intervals/ranges. The core idea: **sort intervals by start time**, then iterate and merge overlaps. Two intervals `[a, b]` and `[c, d]` overlap if `a <= d && c <= b` (assuming `a <= c` after sorting).

### When Do Two Intervals Overlap?
After sorting by start:
```
Case 1: a.end >= b.start → OVERLAP → merge to [a.start, max(a.end, b.end)]
Case 2: a.end < b.start  → NO OVERLAP → start new interval
```

### Six Interval Relationships (a.start <= b.start after sorting):
```
1. a contains b:    a: |--------|     → merge: a
                    b:   |---|

2. b contains a:    a:   |---|        → merge: b
                    b: |--------|

3. a overlaps b:    a: |-----|        → merge: [a.start, b.end]
                    b:    |-----|

4. a == b:          a: |-----|        → merge: a (or b)
                    b: |-----|

5. a ends, b starts:a: |---|          → touching: depends on problem
                    b:     |---|

6. No overlap:      a: |---|          → separate
                    b:        |---|
```

---

## How to Identify This Pattern

### Use Merge Intervals when:
- ✅ Problem talks about **intervals, ranges, time periods, schedules**
- ✅ "Merge overlapping intervals"
- ✅ "Insert a new interval"
- ✅ "Find free time" or "find gaps"
- ✅ "Minimum number of meeting rooms"
- ✅ Anything involving **start time** and **end time**

### Magic Words:
> "intervals", "overlap", "merge", "meeting rooms", "schedule", "time range", "conflicting appointments"

---

## Template Code in Go

### Template: Merge Overlapping Intervals

```go
func mergeIntervals(intervals [][]int) [][]int {
    if len(intervals) <= 1 {
        return intervals
    }

    // Step 1: Sort by start time
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][0] < intervals[j][0]
    })

    // Step 2: Merge
    merged := [][]int{intervals[0]}

    for i := 1; i < len(intervals); i++ {
        last := merged[len(merged)-1]

        if intervals[i][0] <= last[1] { // Overlap
            // Merge: extend the end
            if intervals[i][1] > last[1] {
                last[1] = intervals[i][1]
            }
        } else { // No overlap
            merged = append(merged, intervals[i])
        }
    }

    return merged
}
```

---

## Solved Problems with Explanations

### Problem 1: Merge Intervals (LeetCode 56)

```go
func merge(intervals [][]int) [][]int {
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][0] < intervals[j][0]
    })

    merged := [][]int{intervals[0]}

    for i := 1; i < len(intervals); i++ {
        last := merged[len(merged)-1]
        curr := intervals[i]

        if curr[0] <= last[1] {
            // Overlapping — merge by extending end
            if curr[1] > last[1] {
                merged[len(merged)-1][1] = curr[1]
            }
        } else {
            merged = append(merged, curr)
        }
    }

    return merged
}
// Time: O(n log n) [sorting] | Space: O(n) [output]
```

### Problem 2: Insert Interval (LeetCode 57)

```go
func insert(intervals [][]int, newInterval []int) [][]int {
    result := [][]int{}
    i := 0
    n := len(intervals)

    // Step 1: Add all intervals that come before newInterval
    for i < n && intervals[i][1] < newInterval[0] {
        result = append(result, intervals[i])
        i++
    }

    // Step 2: Merge all overlapping intervals with newInterval
    for i < n && intervals[i][0] <= newInterval[1] {
        if intervals[i][0] < newInterval[0] {
            newInterval[0] = intervals[i][0]
        }
        if intervals[i][1] > newInterval[1] {
            newInterval[1] = intervals[i][1]
        }
        i++
    }
    result = append(result, newInterval)

    // Step 3: Add remaining intervals
    for i < n {
        result = append(result, intervals[i])
        i++
    }

    return result
}
// Time: O(n) | Space: O(n)
```

### Problem 3: Meeting Rooms II — Minimum Rooms Needed (LeetCode 253)

```go
func minMeetingRooms(intervals [][]int) int {
    if len(intervals) == 0 {
        return 0
    }

    // Approach: Use events (start = +1 room, end = -1 room)
    starts := make([]int, len(intervals))
    ends := make([]int, len(intervals))

    for i, interval := range intervals {
        starts[i] = interval[0]
        ends[i] = interval[1]
    }

    sort.Ints(starts)
    sort.Ints(ends)

    rooms, maxRooms := 0, 0
    s, e := 0, 0

    for s < len(starts) {
        if starts[s] < ends[e] {
            rooms++ // A meeting starts before another ends → need new room
            s++
        } else {
            rooms-- // A meeting ended → free a room
            e++
        }
        if rooms > maxRooms {
            maxRooms = rooms
        }
    }

    return maxRooms
}
// Time: O(n log n) | Space: O(n)
```

**Why it works:** By separating starts and ends and processing them in order, we track the "traffic" of meetings. When a start happens before an end, we need an extra room. When an end happens, we free a room.

### Problem 4: Non-overlapping Intervals — Min Removals (LeetCode 435)

```go
func eraseOverlapIntervals(intervals [][]int) int {
    if len(intervals) <= 1 {
        return 0
    }

    // Sort by END time (greedy: keep earliest-ending intervals)
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][1] < intervals[j][1]
    })

    count := 0
    prevEnd := intervals[0][1]

    for i := 1; i < len(intervals); i++ {
        if intervals[i][0] < prevEnd {
            // Overlap! Remove this one (increment count)
            count++
        } else {
            // No overlap, update prevEnd
            prevEnd = intervals[i][1]
        }
    }

    return count
}
// Time: O(n log n) | Space: O(1)
```

### Problem 5: Interval List Intersections (LeetCode 986)

```go
func intervalIntersection(firstList [][]int, secondList [][]int) [][]int {
    result := [][]int{}
    i, j := 0, 0

    for i < len(firstList) && j < len(secondList) {
        // Find intersection
        start := max(firstList[i][0], secondList[j][0])
        end := min(firstList[i][1], secondList[j][1])

        if start <= end {
            result = append(result, []int{start, end})
        }

        // Move the pointer with smaller end
        if firstList[i][1] < secondList[j][1] {
            i++
        } else {
            j++
        }
    }

    return result
}

func max(a, b int) int { if a > b { return a }; return b }
func min(a, b int) int { if a < b { return a }; return b }
// Time: O(m + n) | Space: O(1) excluding output
```

---

## Practice Problems

| # | Problem | Difficulty | Key Hint |
|---|---------|-----------|----------|
| 1 | Merge Intervals | Medium | Sort by start, extend end on overlap |
| 2 | Insert Interval | Medium | Three phases: before, overlap, after |
| 3 | Non-overlapping Intervals | Medium | Sort by **end**, greedy removal |
| 4 | Meeting Rooms | Easy | Sort, check any overlap |
| 5 | Meeting Rooms II | Medium | Sort starts and ends separately |
| 6 | Interval List Intersections | Medium | Two pointers, advance smaller end |
| 7 | Employee Free Time | Hard | Merge all busy times, gaps = free time |
| 8 | Minimum Number of Arrows | Medium | Sort by end, greedy |

---

## Hints

1. **Hint:** ALWAYS sort intervals first (usually by start time). Without sorting, you can't process them linearly.
2. **Hint:** For "minimum removals" — sort by END time (greedy: keep shortest intervals).
3. **Hint:** Meeting Rooms trick: separate starts and ends into two arrays, sort each, sweep through.
4. **Hint:** For intersections of two sorted lists — use two pointers, advance the one with smaller end.
5. **Hint:** If problem says "conflicting" → it means overlapping intervals.

---

[← Fast & Slow Pointers](03-fast-slow-pointers.md) | [Back to Index](../README.md) | [Next: Cyclic Sort →](05-cyclic-sort.md)
