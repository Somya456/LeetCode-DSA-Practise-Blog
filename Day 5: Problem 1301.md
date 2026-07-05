# Day 5 Problem 1301: Number of Paths with Max Score

**Difficulty**:Hard

- [Leetcode Problem 1301](https://leetcode.com/problems/number-of-paths-with-max-score/?envType=daily-question&envId=2026-07-05)

## Problem Statement
You are given a square `board` of characters. You can move on the board starting at the bottom right square marked with the character `S`.\
You need to reach the top left square marked with the character   `E`. The rest of the squares are labeled either with a numeric character `1, 2, ..., 9` or with an obstacle `X`. In one move you can go up, left or up-left (diagonally) only if there is no obstacle there.\
Return a list of two integers: the first integer is the maximum sum of numeric characters you can collect, and the second is the number of such paths that you can take to get that maximum sum, taken `modulo 10^9 + 7`.\
In case there is no path, return `[0, 0]`.

### Constraints
-> 2 <= board.length == board[i].length <= 100

## Approach:

### Idea:
We use the approach of Dynamic Programming for this problem.\
We need to find two things:
- Max sum of numeric characters collected from the bottom-right `S` to the top-left `E`.
- The number of paths that achieve this max sum.

We can only move up, left or diagonally. We can also take it in reverse by starting from destination `E` at `(0,0)` and working up to `S` at `(n-1,n-1)`.\
Let's maintain two 2d grids of size `n*n` named: `max_score[i][j]` containing the maximum score achievable from `(i,j) to (0,0)` and `paths[i][j]` containing the number of unique paths from `(i,j) to (0,0)` that yield `max_score[i][j]`.\
Transitions for any cell `(i,j)` that is not an obstacle `X`, we can arrive from three possible next states, `Up:(i-1,j)`, `Left:(i,j-1)`, `Up-Left:(i-1, j-1)`.\
The maximum value among these cells tells us what the best incoming score is. If a neighbor is reachable `path count>0`, its score plus current's cell value becomes a candidate for `max_score[i][j]`.\
If we find a strictly larger maximum score from a neighbor, we update `max_score[i][j]` and reset `paths[i][j]` to that neightbor's path count.\
If both scores are equal we add that neighbor's path counts to `paths[i][j]`. Base Case at `(0,0) ('E')`, the score is 0 and there is 1 way to be there: `max_score[0][0]=0`, `paths[0][0]=1`.

```cpp
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class Solution {
public:
    vector<int> pathsWithMaxScore(vector<string>& board) {
        int n = board.size();
        int MOD = 1e9 + 7;
        vector<vector<pair<int, int>>> dp(n, vector<pair<int, int>>(n, {-1, 0}));
        dp[0][0] = {0, 1};
        int dirs[3][2] = {{-1, 0}, {0, -1}, {-1, -1}};
        
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if ((i == 0 && j == 0) || board[i][j] == 'X') continue;
                
                int current_val = (board[i][j] == 'S') ? 0 : (board[i][j] - '0');
                int best_score = -1;
                int path_count = 0;
                for (auto& d : dirs) {
                    int prev_i = i + d[0];
                    int prev_j = j + d[1];
                    if (prev_i >= 0 && prev_j >= 0 && dp[prev_i][prev_j].first != -1) {
                        int prev_score = dp[prev_i][prev_j].first;
                        int prev_paths = dp[prev_i][prev_j].second;
                        
                        if (prev_score > best_score) {
                            best_score = prev_score;
                            path_count = prev_paths;
                        } else if (prev_score == best_score) {
                            path_count = (path_count + prev_paths) % MOD;
                        }
                    }
                }
                if (best_score != -1) {
                    dp[i][j] = {best_score + current_val, path_count};
                }
            }
        }
        if (dp[n - 1][n - 1].first == -1) {
            return {0, 0};
        }
        return {dp[n - 1][n - 1].first, dp[n - 1][n - 1].second};
    }
};
```
## Complexity Analysis:
Time Complexity: O($n^{2}$)\
Space Complexity: O($n^{2}$)
