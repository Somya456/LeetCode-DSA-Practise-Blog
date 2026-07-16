# Day 10 Problem 3534: Path Existence Queries in a Graph - 2

**Difficulty**: Hard

- [Leetcode Problem 3534](https://leetcode.com/problems/path-existence-queries-in-a-graph-ii/?envType=daily-question&envId=2026-07-10)

## Problem Statement
You are given an integer `n` representing the number of nodes in a graph, labeled from `0` to `n - 1`.\
You are also given an integer array `nums` of length `n` and an integer `maxDiff`.\
An undirected edge exists between nodes `i` and `j` if the absolute difference between `nums[i]` and `nums[j]` is at most `maxDiff` (i.e., $|nums[i] - nums[j]| \le maxDiff$).\
You are also given a 2D integer array queries. For each $queries[i] = [u_{i}, v_{i}]$, find the minimum distance between nodes $u_{i}$ and $v_{i}$. If no path exists between the two nodes, return `-1` for that query.\
Return an array `answer`, where `answer[i]` is the result of the $i^{th}$ query.\
Note: The edges between the nodes are unweighted.

### Constraints
-> $1 \le n == nums.length \le 10^{5}$\
-> $0 \le nums[i] \le 10^{5}$\
-> $0 \le maxDiff \le 10^{5}$\
-> $1 \le queries.length \le 10^{5}$\
-> $queries[i] == [u_{i}, v_{i}]$\
-> $0 \le u_{i}, v_{i} < n$

## Approach:

### Idea:
The problem asks for the minimum distance between two nodes `u` and `v`. An undirected edge exists between any two nodes `i` and `j` if $|nums[i] - nums[j]| \le maxDiff$.\
Because we want to minimize the number of steps, we want to make the absolute progress as large as possible at each step. This greedy property is highly structured:
- Climbing Up ($nums[u] < nums[v]$): To reach a target value `nums[v]` starting from `nums[u]` in the minimum steps, the optimal strategy is always to jump to a node with the largest possible value that is still within $val + maxDiff$.
- Climbing Down ($nums[u] > nums[v]$): To reach a target value `nums[v]` starting from `nums[u]`, the optimal strategy is always to jump to a node with the smallest possible value that is still within $val - maxDiff$.

Because this greedy choice is deterministic, we can view this as moving along a functional directed graph. This allows us to use Binary Lifting (Binary Jumping) to simulate multiple steps in `O(log(n))` time per query.\
Instead of searching for the optimal target for each node using binary search, we can use a Two-Pointer (Sliding Window) approach on the sorted nodes to find the immediate optimal steps in `O(n)` time:
- Create a helper array of pairs containing `(value, original_index)` and sort it.
- For the Up jumps: Use a sliding window pointer `r` that advances as long as $sorted\_nodes[r].value \le val + maxDiff$. The node at `r - 1` represents the maximum reachable node value.
- For the Down jumps: Use a sliding window pointer `l` that advances as long as $sorted\_nodes[l].value < val - maxDiff$. The node at `l` represents the minimum reachable node value.

Now, we construct two sparse tables `Up[i][k]` and `Down[i][k]` of dimensions $n \times log_{2}(n)$:
- `Up[i][k]` represents the node we end up at if we take $2^k$ optimal greedy upward steps from node `i`.
- `Down[i][k]` represents the node we end up at if we take $2^k$ optimal greedy downward steps from node `i`.

The state transitions are defined as:
- $$\text{Up}[i][k] = \text{Up}[\text{Up}[i][k-1]][k-1]$$
- $$\text{Down}[i][k] = \text{Down}[\text{Down}[i][k-1]][k-1]$$

If our final landed node `curr` is within `maxDiff` of `v`, the accumulated steps is our shortest path distance.\
If not, it means `u` and `v` belong to disconnected components, so we return `-1`.

```cpp
#include <vector>
#include <cmath>
#include <algorithm>

using namespace std;

class Solution {
public:
    vector<int> pathExistenceQueries(int n, vector<int>& nums, int maxDiff, vector<vector<int>>& queries) {
        vector<pair<int, int>> sorted_nodes(n);
        for (int i = 0; i < n; ++i) {
            sorted_nodes[i] = {nums[i], i};
        }
        sort(sorted_nodes.begin(), sorted_nodes.end());

        int LOG = 18;
        vector<vector<int>> Up(n, vector<int>(LOG));
        vector<vector<int>> Down(n, vector<int>(LOG));
        int r = 0;
        for (int i = 0; i < n; ++i) {
            int orig_idx = sorted_nodes[i].second;
            int val = sorted_nodes[i].first;
            while (r < n && sorted_nodes[r].first <= val + maxDiff) {
                r++;
            }
            Up[orig_idx][0] = sorted_nodes[r - 1].second;
        }
        int l = 0;
        for (int i = 0; i < n; ++i) {
            int orig_idx = sorted_nodes[i].second;
            int val = sorted_nodes[i].first;
            while (l < n && sorted_nodes[l].first < val - maxDiff) {
                l++;
            }
            Down[orig_idx][0] = sorted_nodes[l].second;
        }
        for (int k = 1; k < LOG; ++k) {
            for (int i = 0; i < n; ++i) {
                Up[i][k] = Up[Up[i][k - 1]][k - 1];
                Down[i][k] = Down[Down[i][k - 1]][k - 1];
            }
        }

        vector<int> ans;
        ans.reserve(queries.size());

        for (const auto& q : queries) {
            int u = q[0], v = q[1];
            if (u == v) {
                ans.push_back(0);
                continue;
            }
            if (abs(nums[u] - nums[v]) <= maxDiff) {
                ans.push_back(1);
                continue;
            }

            int steps = 0;
            int curr = u;

            if (nums[u] < nums[v]) {
                for (int k = LOG - 1; k >= 0; --k) {
                    int nxt = Up[curr][k];
                    if (nums[v] - nums[nxt] > maxDiff) {
                        curr = nxt;
                        steps += (1 << k);
                    }
                }
                curr = Up[curr][0];
                steps++;
            } else {
                for (int k = LOG - 1; k >= 0; --k) {
                    int nxt = Down[curr][k];
                    if (nums[nxt] - nums[v] > maxDiff) {
                        curr = nxt;
                        steps += (1 << k);
                    }
                }
                curr = Down[curr][0];
                steps++;
            }

            if (abs(nums[curr] - nums[v]) <= maxDiff) {
                ans.push_back(steps + 1);
            } else {
                ans.push_back(-1);
            }
        }

        return ans;
    }
};
```

## Complexity Analysis:
**Time Complexity**: $O((n+q)\log(n))$\
**Space Complexity**: $O(n\log(n)+q)$\
where n is the number of nodes, q is the number of queries, 
