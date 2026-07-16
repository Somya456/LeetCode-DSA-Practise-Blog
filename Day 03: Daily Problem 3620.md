# Day 3 Problem 3620: Network Recovery Pathways

**Difficulty**: Hard

- [Leetcode Problem 3620](https://leetcode.com/problems/network-recovery-pathways/?envType=daily-question&envId=2026-07-03)

## Problem Statement 
You are given a directed acyclic graph of n nodes numbered from `0` to `n − 1`. This is represented by a 2D array edges of length `m`, where $edges[i] = [u_{i}, v_{i}, cost_{i}]$ indicates a one‑way communication from node $`u_{i}`$ to node $`v_{i}`$ with a recovery cost of $`cost_{i}`$.\
Some nodes may be offline. You are given a boolean array online where `online[i] = true` means node `i` is online. Nodes `0` and `n − 1` are always online.

A path from `0` to `n − 1` is valid if:
- All intermediate nodes on the path are online.
- The total recovery cost of all edges on the path does not exceed `k`.
- For each valid path, define its score as the minimum edge‑cost along that path.

Return the maximum path score (i.e., the largest minimum-edge cost) among all valid paths. If no valid path exists, return `-1`.

### Constraints

-> $n == online.length$\
-> $2 \le n \le 5 \times 10^{4}$\
-> $0 \le m == edges.length \le min(10^{5}, \frac {n \times (n - 1)}{2})$\
-> $edges[i] = [u_{i}, v_{i}, cost_{i}]$\
-> $0 \le u_{i}, v_{i} < n$\
-> $u_{i} != v_{i}$\
-> $0 \le cost_{i} \le 10^{9}$\
-> $0 \le k \le 5 \times 10^{13}$\
-> $\text{online[i] is either true or false, and both online[0] and online[n − 1] are true.}$\
-> $\text{The given graph is a directed acyclic graph.}$

## Approach:

### Idea:
To find a valid path from `0` to `n-1` that maximises minimum edge cost along the path while keeping the `total cost<=k` and only visiting online nodes, we can use binary search for the maximum possible score.\
The score can range from 0 to the maximum edge cost present in the graph, for a score `mid` we filter the graph to only includes costs where `cost>=mid`.\
From these filtered edges and online nodes, we find a path from `0 to n-1` that minimises the total recovery cost. Since it a Directed Acyclic Graph we can compute the shortest path using topological sort and dynamic programming.\
If the minimum total cost to reach `n-1` is `<=k`, then `mid` is possible so look for a larger score, otherwise try a smaller score.

```cpp
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;
class Solution {
public:
    int findMaxPathScore(vector<vector<int>>& edges, vector<bool>& online, long long k) {
        int n = online.size();
        vector<vector<pair<int, int>>> adj(n);
        vector<int> inDegree(n, 0);
        for (const auto& edge : edges) {
            int u = edge[0];
            int v = edge[1];
            int cost = edge[2];
            adj[u].push_back({v, cost});
            inDegree[v]++;
        }
        vector<int> topoOrder;
        queue<int> q;
        for (int i = 0; i < n; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            topoOrder.push_back(u);
            for (const auto& neighbor : adj[u]) {
                int v = neighbor.first;
                if (--inDegree[v] == 0) {
                    q.push(v);
                }
            }
        }
        int low = 0, high = 0;
        for (const auto& edge : edges) {
            high = max(high, edge[2]);
        }
        int ans = -1;
        long long INF = 1e18; 
        auto check = [&](int mid) -> bool {
            vector<long long> dp(n, INF);
            dp[0] = 0;
            for (int u : topoOrder) {
                if (dp[u] == INF) continue;
                if (u != 0 && u != n - 1 && !online[u]) continue;
                for (const auto& neighbor : adj[u]) {
                    int v = neighbor.first;
                    int cost = neighbor.second;
                    if (cost >= mid) {
                        if (dp[u] + cost < dp[v]) {
                            dp[v] = dp[u] + cost;
                        }
                    }
                }
            }
            return dp[n - 1] <= k;
        };
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (check(mid)) {
                ans = mid;      
                low = mid + 1;
            } else {
                high = mid - 1; 
            }
        }
        return ans;
    }
};
```
## Complexity Analysis:
**Time Complexity**: $O((n+m)logc)$\
**Space Complexity**: $O(n+m)$




