# Day 9 Problem 3532: Path Existence Queries in a Graph - 1

**Difficulty**:Medium

- [Leetcode Problem 3532](https://leetcode.com/problems/path-existence-queries-in-a-graph-i/?envType=daily-question&envId=2026-07-09)

## Problem Statement
You are given an integer `n` representing the number of nodes in a graph, labeled from `0` to `n - 1`.\
You are also given an integer array `nums` of length `n` sorted in non-decreasing order, and an integer `maxDiff`.\
An undirected edge exists between nodes `i` and `j` if the absolute difference between `nums[i]` and `nums[j]` is at most `maxDiff` $(i.e., |nums[i] - nums[j]| <= maxDiff)$.\
You are also given a 2D integer array `queries`. For each $queries[i] = [u_{i}, v_{i}]$, determine whether there exists a path between nodes $u_{i}$ and $v_{i}$.\
Return a boolean array `answer`, where `answer[i]` is `true` if there exists a path between $u_{i}$ and $v_{i}$ in the $i^{th}$ query and `false` otherwise.

### Constraints
-> 1 <= n == nums.length <= $10^{5}$\
-> 0 <= nums[i] <= $10^{5}$\
-> nums is sorted in non-decreasing order.\
-> 0 <= maxDiff <= $10^{5}$\
-> 1 <= queries.length <= $10^{5}$\
-> queries[i] == $[u_{i}, v_{i}]$\
-> 0 <= $u_{i}, v_{i}$ < n

## Approach:

### Idea:
The problem asks whether a path exists between two nodes $u_i$ and $v_i$ under the condition that an edge exists between any two nodes `i` and `j` if $|nums[i] - nums[j]| \le maxDiff$. We are also given that the `nums` array is sorted in non-decreasing order.\
Because `nums` is sorted, any connected component in this graph must form a contiguous segment of indices.\
If $nums[k+1]-nums[k] \le maxDiff$, then node `k` and `k+1` are directly connected. If there is a gap where $nums[k+1]-nums[k]>maxDiff$, it is impossible for any node $\le k$ to reach any node index $\ge k+1$ as the values are monotonically increasing, so the difference between any element before `k` will be even larger than `maxDiff`.\
Hence, we can preprocess the array to assign a component ID to each node. If two adjacent elements have a difference greater than `maxDiff`, a new component begins.\
For each query `(u,v)`, a path exists if and only if `u` and `v` belong to the same component.

```cpp
#include <vector>
#include <cmath>

class Solution {
public:
    std::vector<bool> pathExistenceQueries(int n, std::vector<int>& nums, int maxDiff, std::vector<std::vector<int>>& queries) {
        std::vector<int> comp(n, 0);
        int current_component = 0;
        for (int i = 1; i < n; ++i) {
            if (nums[i] - nums[i - 1] > maxDiff) {
                current_component++;
            }
            comp[i] = current_component;
        }
        std::vector<bool> answer;
        answer.reserve(queries.size());
        for (const auto& query : queries) {
            int u = query[0];
            int v = query[1];
            answer.push_back(comp[u] == comp[v]);
        }
        return answer;
    }
};
```
## Complexity Analysis:
Time Complexity: $O(n+q)$\
Space Complexity: $O(n)$\
where, $q=queries.length()$ and $n=nums.length()$.
