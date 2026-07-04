# Day 4 Problem 2492: Minimum Score of a Path Between Two Cities

**Difficulty**:Medium

-

## Problem Statement
You are given a positive integer `n` representing `n` cities numbered from `1 to n`. You are also given a 2D array roads where `roads[i] = [ai, bi, distancei]` indicates that there is a bidirectional road between cities `ai` and `bi` with a distance equal to `distancei`. The cities graph is not necessarily connected.

The score of a path between two cities is defined as the minimum distance of a road in this path.

Return the minimum possible score of a path between cities `1` and `n`.

Note:

- A path is a sequence of roads between two cities.
- It is allowed for a path to contain the same road multiple times, and you can visit cities `1` and `n` multiple times along the path.
- The test cases are generated such that there is at least one path between `1` and `n`.

### Constraints:
-> 2 <= n <= 105\
-> 1 <= roads.length <= 105\
-> roads[i].length == 3\
-> 1 <= ai, bi <= n\
-> ai != bi\
-> 1 <= distancei <= 104\
-> There are no repeated edges.\
-> There is at least one path between 1 and n.

## Approach:

### Idea:
The way to solve this problem is to realize that we can visit any city and traverse any road multiple times, the minimum score of a path from city 1 to city n is the minimum weight of any edge in the connected component containing city 1.
First, we convert the given list of roads into an adjacency list `adj` where each node points to a list of pairs `{neighbor, road_distance}`.
Then we start a standard BFS from city 1. 
As we explore the neighbors of each node we constantly update `min_score` with the weigh of the roads we encounter. We do this for all edges connected to any reached node, even if the neighboring node has already been visited. This helps ensure that every single edge in the connected component is considered.
Once the queue is empty, and we have explored the entire connected component containing city 1 and city n, and `min_score` holds our answer.

```cpp
class Solution {
public:
    int minScore(int n, vector<vector<int>>& roads) {
        vector<vector<pair<int, int>>> adj(n + 1);
        for (const auto& road : roads) {
            int u = road[0];
            int v = road[1];
            int dist = road[2];
            adj[u].push_back({v, dist});
            adj[v].push_back({u, dist});
        }
        int min_score = INT_MAX;
        vector<bool> visited(n + 1, false);
        queue<int> q;
        q.push(1);
        visited[1] = true;
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            for (const auto& neighbor : adj[node]) {
                int next_node = neighbor.first;
                int weight = neighbor.second;
                min_score = min(min_score, weight);
                if (!visited[next_node]) {
                    visited[next_node] = true;
                    q.push(next_node);
                }
            }
        }
        return min_score;
    }
};
```
## Complexity Analysis:
Time Complexity : O(V+E)\
Space Complexity : O(V+E)

Where V is the number of cities(n) and E is the number of roads(roads.length)



