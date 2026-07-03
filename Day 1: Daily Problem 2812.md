# Day 1 Problem 2812: Find the Safest Path in a Grid
 
**Difficulty**:Medium  

- [Leetcode Problem 2812](https://leetcode.com/problems/find-the-safest-path-in-a-grid/description/?envType=daily-question&envId=2026-07-01)
  
## Problem Statement
You are given a **0-indexed** square matrix `grid` of size **n × n**, where each cell is either an empty cell (`0`) or contains a thief (`1`). 
You start at the top-left cell `(0, 0)` and need to reach the bottom-right cell `(n - 1, n - 1)`. 
From any cell, you can move one step in any of the four directions—up, down, left, or right—as long as the destination cell lies within the grid. 
Moving through cells containing thieves is allowed.

The **safeness factor** of a path is defined as the minimum **Manhattan distance** between any cell on that path and the nearest thief. 
The Manhattan distance between two cells `(a, b)` and `(x, y)` is calculated as `|a - x| + |b - y|`. 
Your task is to find a path from the starting cell to the destination such that its safeness factor is as large as possible, and return this maximum safeness factor.

### Constraints:

-> 1 <= grid.length == n <= 400\
-> grid[i].length == n\
-> grid[i][j] is either 0 or 1.\
-> There is at least one thief in the grid.

## Approach:

### Idea:
Use a two-step graph traversal approach. We need to find a path from the top left corner `(0,0)` to the bottom right corner `(n-1, n-1)` keeping as far from the thieves as possible which are represented using 1s in the grid.\
Instead calculating distance from each thief every time, we will treat all thieves as a starting point in a Mutli Source BFS.\
First push all the thieves in a queue with distance `0`, all other points have a distance of `-1`. The BFS expands outwards and on coming to an empty cell it will update the distance.\
Now to find the shortest path, we use Dijkstra's algorithm, here we use a max-heap instead of min-heap to give more priority to the paths with highest possible safeness factor. 

```cpp
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

class Solution {
public:
    int maximumSafenessFactor(vector<vector<int>>& grid) {
        int n = grid.size();
        if (grid[0][0] == 1 || grid[n - 1][n - 1] == 1) {
            return 0;
        }
        vector<vector<int>> dist(n, vector<int>(n, -1));
        queue<pair<int, int>> q;
        for (int r = 0; r < n; ++r) {
            for (int c = 0; c < n; ++c) {
                if (grid[r][c] == 1) {
                    dist[r][c] = 0;
                    q.push({r, c});
                }
            }
        }

        int dr[] = {-1, 1, 0, 0};
        int dc[] = {0, 0, -1, 1};

        while (!q.empty()) {
            auto [r, c] = q.front();
            q.pop();

            for (int i = 0; i < 4; ++i) {
                int nr = r + dr[i];
                int nc = c + dc[i];

                if (nr >= 0 && nr < n && nc >= 0 && nc < n && dist[nr][nc] == -1) {
                    dist[nr][nc] = dist[r][c] + 1;
                    q.push({nr, nc});
                }
            }
        }
        priority_queue<pair<int, pair<int, int>>> maxHeap;
        vector<vector<int>> maxSafeness(n, vector<int>(n, -1));

        maxHeap.push({dist[0][0], {0, 0}});
        maxSafeness[0][0] = dist[0][0];

        while (!maxHeap.empty()) {
            auto [safeness, cell] = maxHeap.top();
            auto [r, c] = cell;
            maxHeap.pop();
            if (r == n - 1 && c == n - 1) {
                return safeness;
            }
            if (safeness < maxSafeness[r][c]) continue;

            for (int i = 0; i < 4; ++i) {
                int nr = r + dr[i];
                int nc = c + dc[i];

                if (nr >= 0 && nr < n && nc >= 0 && nc < n) {
                    int nextSafeness = min(safeness, dist[nr][nc]);

                    if (nextSafeness > maxSafeness[nr][nc]) {
                        maxSafeness[nr][nc] = nextSafeness;
                        maxHeap.push({nextSafeness, {nr, nc}});
                    }
                }
            }
        }

        return 0;
    }
};
```
## Complexity Analysis:
Time Complexity : O($n^{2}$ log n)\
Space Complexity : O($n^{2}$)
