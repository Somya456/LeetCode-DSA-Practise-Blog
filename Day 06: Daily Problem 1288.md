# Day 6 Problem 1288: Remove Covered Intervals

**Difficulty**: Medium

- [Leetcode Problem 1288](https://leetcode.com/problems/remove-covered-intervals/description/?envType=daily-question&envId=2026-07-06)

## Problem Statement
Given an array intervals where $intervals[i] = [l_{i}, r_{i}]$ represent the interval $[l_{i}, r_{i})$, remove all intervals that are covered by another interval in the list.\
The interval `[a, b)` is covered by the interval `[c, d)` if and only if `c <= a` and `b <= d`.\
Return the number of remaining intervals.

### Constraints
-> $1 \le intervals.length \le 1000$\
-> $intervals[i].length == 2$\
-> $0 \le l_{i} < r_{i} \le 10^{5}$\
-> $\text{All the given intervals are unique.}$

## Approach:

### Idea:
For this problem we use the approach of sorting and greedy scan.\
An interval `[a,b)` is covered by `[c,d)` if $c \le a$ and $b \le d$. If we sort the intervals primarily by their start points in ascending order, we can ensure that its start point will always be greater than or equal to the previous interval's start point.\
First, sort in ascending order for start points, then sort end points in descending order.\
We maintain the maximum end point `max_end` we have so far from the valid intervals. As we move through the sorted intervals, if the current interval's end point is less than or equal to `max_end`, it means this interval is completely swallowed or covered by a previous interval, so we count it as covered.\
If the current interval's end point is greater than `max_end`, it cannot be covered by covered by previous intervals. So, we update max_end to this new end point and keep it.

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int removeCoveredIntervals(std::vector<std::vector<int>>& intervals) {
        std::sort(intervals.begin(), intervals.end(), [](const std::vector<int>& a, const std::vector<int>& b) {
            if (a[0] == b[0]) {
                return a[1] > b[1];
            }
            return a[0] < b[0];
        });
        
        int count = 0;
        int max_end = 0;
        
        for (const auto& interval : intervals) {
            if (interval[1] <= max_end) {
                count++;
            } else {
                max_end = interval[1];
            }
        }
        return intervals.size() - count;
    }
};
```
## Complexity Analysis:
**Time Complexity**: $O(N\log(N))$\
**Space Complexity**: $O(1)$ or $O(N)$\
where, N is the number of intervals.
