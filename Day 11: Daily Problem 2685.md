# Day 11 Problem 2685: Count the Number of Complete Components

**Difficulty**:Medium

- [Leetcode Problem 2685](https://leetcode.com/problems/count-the-number-of-complete-components/?envType=daily-question&envId=2026-07-11)

## Problem Statement
You are given an integer `n`. There is an undirected graph with `n` vertices, numbered from `0` to `n - 1`. You are given a 2D integer array `edges` where $edges[i] = [a_{i}, b_{i}]$ denotes that there exists an undirected edge connecting vertices $a_{i}$ and $b_{i}$.\
Return the number of complete connected components of the graph.\
A connected component is a subgraph of a graph in which there exists a path between any two vertices, and no vertex of the subgraph shares an edge with a vertex outside of the subgraph.\
A connected component is said to be complete if there exists an edge between every pair of its vertices.

### Constraints
-> 1 <= n <= 50\
-> 0 <= edges.length <= n * (n - 1) / 2\
-> edges[i].length == 2\
-> 0 <= $a_{i}, b_{i}$ <= n - 1\
-> $a_{i} != b_{i}$\
-> There are no repeated edges.

## Approach:

### Idea:
Here we use a Depth-First Search (DFS) traversal strategy to identify and validate each connected component in an undirected graph.\
A connected component with `V` vertices is considered complete if there is an edge between every single pair of its vertices. Mathematically, the total number of edges in a complete graph of size `V` is given by:
- $$\frac{V \times (V - 1)}{2}$$

First, represent the graph using an adjacency list `adj` from the given `edges` array. Then, iterate through all vertices from `0` to `n - 1`. If a vertex hasn't been visited yet, it marks the start of a new connected component.\
For each unvisited component, initialize `vertexCount = 0` and `edgeCount = 0`. Run a DFS traversal:
- Increment `vertexCount` by 1 for each unique node visited.
- Add the size of the current node's adjacency list `(adj[node].size())` to `edgeCount`.

After completing the DFS for a component, check if `edgeCount == vertexCount * (vertexCount - 1)`. If it matches, increment your complete components counter.\

```cpp
class Solution {
public:
    void dfs(int node, const vector<vector<int>>& adj, vector<bool>& visited, int& vertexCount, int& edgeCount) {
        visited[node] = true;
        vertexCount++;
        edgeCount += adj[node].size(); 
        
        for (int neighbor : adj[node]) {
            if (!visited[neighbor]) {
                dfs(neighbor, adj, visited, vertexCount, edgeCount);
            }
        }
    }

    int countCompleteComponents(int n, vector<vector<int>>& edges) {
        vector<vector<int>> adj(n);
        for (const auto& edge : edges) {
            adj[edge[0]].push_back(edge[1]);
            adj[edge[1]].push_back(edge[0]);
        }
        
        vector<bool> visited(n, false);
        int completeComponents = 0;
        
        for (int i = 0; i < n; ++i) {
            if (!visited[i]) {
                int vertexCount = 0;
                int edgeCount = 0;
                
                dfs(i, adj, visited, vertexCount, edgeCount);
                int actualEdges = edgeCount / 2;
                if (actualEdges == (vertexCount * (vertexCount - 1)) / 2) {
                    completeComponents++;
                }
            }
        }
        
        return completeComponents;
    }
};
```

## Complexity Analysis:
Time Complexity: $O(V+E)$\
Space Complexity: $O(V+E)$
