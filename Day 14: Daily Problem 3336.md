# Day 14 Problem 3336: Find the Number of Subsequences with Equal GCD

**Difficulty**:Hard

- [Leetcode Problem 3336](https://leetcode.com/problems/find-the-number-of-subsequences-with-equal-gcd/?envType=daily-question&envId=2026-07-14)

## Problem Statement
You are given an integer array `nums`.\
Your task is to find the number of pairs of non-empty subsequences `(seq1, seq2)` of `nums` that satisfy the following conditions:
- The subsequences `seq1` and `seq2` are disjoint, meaning no index of `nums` is common between them.
- The GCD of the elements of `seq1` is equal to the GCD of the elements of `seq2`.
- Return the total number of such pairs.

Since the answer may be very large, return it $modulo 10^{9} + 7$.

### Constraints
-> 1 <= nums.length <= 200\
-> 1 <= nums[i] <= 200

## Approach:

### Idea:
For this question we use a dynamic programming approach similar to the knapsack problem.\
We use a 2D table `dp[g1][g2]` to store the number of ways to form two disjoint subsequences from a prefix of `nums` where:
- The greatest common divisor (GCD) of the first subsequence is `g1`.
- The greatest common divisor (GCD) of the second subsequence is `g2`.

We define `0` as a placeholder representing an empty subsequence.\
Initially, before we process any elements, there is exactly one way to have both subsequences empty, `dp[0][0]=1`.\
For each element `x` in `nums`, we have three choices:
- The state `dp[g1][g2]` remains unchanged, if we don't include `x` in any subsequence.
- If we add `x` to the first subsequence: The new GCD of the first subsequence becomes `gcd(g1, x)`, transitioning to state `dp[gcd(g1, x)][g2]`.
- If we add `x` to the second subsequence: The new GCD of the second subsequence becomes `gcd(g2, x)`, transitioning to state `dp[g1][gcd(g2, x)]`.

The valid pairs require both subsequences to be non-empty (which means both $g_{1} > 0$ and $g_{2} > 0$) and have equal GCDs ($g_{1} = g_{2}$).\
Therefore, we sum `dp[g][g]` for all $1 \le g \le \max(nums)$.

```cpp
#include <vector>
#include <numeric>
#include <algorithm>

using namespace std;

class Solution {
private:
    const int MOD = 1e9 + 7;
    int get_gcd(int a, int b) {
        if (a == 0) return b;
        if (b == 0) return a;
        return std::gcd(a, b);
    }

public:
    int subsequencePairCount(vector<int>& nums) {
        int n = nums.size();
        int max_val = *max_element(nums.begin(), nums.end());
        vector<vector<int>> gcd_table(max_val + 1, vector<int>(max_val + 1, 0));
        for (int i = 0; i <= max_val; ++i) {
            for (int j = 0; j <= max_val; ++j) {
                gcd_table[i][j] = get_gcd(i, j);
            }
        }
        vector<vector<int>> dp(max_val + 1, vector<vector<int>::value_type>(max_val + 1, 0));
        dp[0][0] = 1; 
        for (int x : nums) {
            vector<vector<int>> next_dp = dp;
            
            for (int g1 = 0; g1 <= max_val; ++g1) {
                for (int g2 = 0; g2 <= max_val; ++g2) {
                    if (dp[g1][g2] == 0) continue;
                    
                    int val = dp[g1][g2];
                    int next_g1 = gcd_table[g1][x];
                    next_dp[next_g1][g2] = (next_dp[next_g1][g2] + val) % MOD;
                    int next_g2 = gcd_table[g2][x];
                    next_dp[g1][next_g2] = (next_dp[g1][next_g2] + val) % MOD;
                }
            }
            dp = move(next_dp);
        }
        long long result = 0;
        for (int g = 1; g <= max_val; ++g) {
            result = (result + dp[g][g]) % MOD;
        }

        return result;
    }
};
```

## Complexity Analysis:
Time Complexity: $O(N.M^{2})$\
Space Complexity: $O(M^{2})$\
Where N is $nums.length()$ and M is $max(nums)$.
