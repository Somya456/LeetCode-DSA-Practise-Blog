# Day 2 Problem 3286: Find a Safe Walk Through a Grid

**Difficulty**:Medium

- [Leetcode Problem 3286 ](https://leetcode.com/problems/find-a-safe-walk-through-a-grid/?envType=daily-question&envId=2026-07-02)

## Problem Statement
You are given an `m x n` binary matrix grid and an integer health.
You start on the upper-left corner `(0, 0)` and would like to get to the lower-right corner `(m - 1, n - 1)`.\
You can move up, down, left, or right from one cell to another adjacent cell as long as your health remains positive.
Cells `(i, j) with grid[i][j] = 1` are considered unsafe and reduce your health by 1.\
Return **true** if you can reach the final cell with a health value of 1 or more, and **false** otherwise.

## Constraints:
-> m == grid.length\
-> n == grid[i].length\
-> 1 <= m, n <= 50\
-> 2 <= m * n\
-> 1 <= health <= m + n\
-> grid[i][j] is either 0 or 1.

## Approach:

### Idea:
First, we track the minimum health damage we can take to reach each cell `(i,j)`. Then we use a 2D array initialised with a large number to store these damages. Using Dijkstra Algorithm, we start from `(0,0)` with initial damage `grid[0][0]`.\
Using a deque to explore nighbors, so when moving to an adjacent cell costs `0` we push it to the front of the deque, if it costs `1`, we push it to the back.\
If the minimum damage to reach `(m-1, n-1)` is strictly less than our health, that means we can reach the end with atleast `1` health point remaining, hence return true otherwise return false.

```cpp
#include <vector>
#include <deque>

class Solution {
public:
    bool findSafeWalk(std::vector<std::vector<int>>& grid, int health) {
        int m = grid.size();
        int n = grid[0].size();
        std::vector<std::vector<int>> dist(m, std::vector<int>(n, 1e9));
        std::deque<std::pair<int, int>> dq;
        dist[0][0] = grid[0][0];
        dq.push_back({0, 0});
        int dr[] = {-1, 1, 0, 0};
        int dc[] = {0, 0, -1, 1};
        
        while (!dq.empty()) {
            auto [r, c] = dq.front();
            dq.pop_front();
            if (r == m - 1 && c == n - 1) break;
            for (int i = 0; i < 4; ++i) {
                int nr = r + dr[i];
                int nc = c + dc[i];
                if (nr >= 0 && nr < m && nc >= 0 && nc < n) {
                    int weight = grid[nr][nc];
                    int next_dist = dist[r][c] + weight;
                    if (next_dist < dist[nr][nc]) {
                        dist[nr][nc] = next_dist;
                        if (weight == 0) {
                            dq.push_front({nr, nc});
                        } else {
                            dq.push_back({nr, nc});
                        }
                    }
                }
            }
        }
        return dist[m - 1][n - 1] < health;
    }
};
```

## Complexity Analysis:
Time Complexity : O(m * n log(m * n))\
Space Complexity : O($m*n$)
