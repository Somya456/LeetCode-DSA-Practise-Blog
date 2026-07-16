# Day 8 Problem 3756: Concatenate Non-Zero Digits and Multiply by Sum - 2

**Difficulty**: Medium

- [Leetcode Problem 3756](https://leetcode.com/problems/concatenate-non-zero-digits-and-multiply-by-sum-ii/?envType=daily-question&envId=2026-07-08)

## Problem Statement
You are given a string `s` of length `m` consisting of digits. You are also given a 2D integer array queries, where `queries[i] = [lᵢ, rᵢ]`.\
For each `queries[i]`, extract the substring `s[lᵢ...rᵢ]`. Then, perform the following:\
Form a new integer `x` by concatenating all the non-zero digits from the substring in their original order. If there are no non-zero digits, `x = 0`.\
Let `sum` be the sum of digits in `x`. The answer is `x * sum`.\
Return an array of integers `answer` where `answer[i]` is the answer to the `ith` query.\
Since the answers may be very large, return them modulo $10^{9} + 7$.

### Constraints
-> $1 \le m == s.length \le 10^{5}$\
-> $\text{s consists of digits only.}$\
-> $1 \le queries.length \le 10^{5}$\
-> $queries[i] = [l_{i}, r_{i}]$\
-> $0 \le l_{i} \le r_{i} < m$

## Approach:

### Idea:
We need to extract non-zero digits within a range `[l,r]`, then treat them as a seperate number `x`, find their sum and return `(x*sum) (mod 10⁹+7)`.\
Firstly, the sum of digits is order-independent and we don't have to worry about zeros. We precompute a standard prefix sum array for the digit values, `prefSum[i]` stores the sum of all digits from indices `0` to `i-1`.\
The sum of digits for any query `[l,r]` can be computed as:
- $Sum = prefSum[r+1] - prefSum[l]$

If we only look at non-zero digits, we can precompute an array `V` storing only the non-zero digits. A prefix numeric value array `P` where `P[i]` represents the number formed by the first `i` non-zero digits `modulo(10⁹+7)`:
- $P[i] = ( P[i-1]*10 + V[i-1]) (mod 10^{9}+7)$

If a query `[l,r]` maps to a contiguous subarray of non-zero digits from index $L_{nz}$ to $R_{nz}$ in our compressed list `V`, the number `x` formed by this range can be computed using the prefix array `P`:
- $x = (P[R_{nz}+1] - P[L_{nz}] * 10^{(R_{nz}-L_{nz}+1)}) (mod 10^{9}+7)$

To do this efficiently we precompute powers of $10 modulo 10^{9}+7$.
To find $L_{nz}$ and $R_{nz}$ in `O(1)` time we create:
- `nxt[i]` which stores the index in V of the first non-zero digit at or after index `i` in the original string. If none exists, set it to the size of `V`. 
- `last[i]` which stores the index in `V` of the last non-zero digit at or before index `i` in the original string. If none exists, set it to `-1`.

For a query `[l,r]`, the corresponding range in `V` is simply:
- $[L_{nz}, R_{nz}] = [nxt[l], last[r]]$

If $L_{nz}>R_{nz}$, it means there are no non-zero digits in that range so, `x=0`. 

```cpp
#include <vector>
#include <string>

using namespace std;

class Solution {
public:
    vector<int> sumAndMultiply(string s, vector<vector<int>>& queries) {
        int m = s.length();
        long long MOD = 1e9 + 7;
        vector<int> prefSum(m + 1, 0);
        for (int i = 0; i < m; ++i) {
            prefSum[i + 1] = prefSum[i] + (s[i] - '0');
        }
        vector<int> V;
        vector<int> nxt(m, 0), last(m, 0);
        int current_nz_idx = 0;
        for (int i = 0; i < m; ++i) {
            if (s[i] != '0') {
                V.push_back(s[i] - '0');
            }
            nxt[i] = V.size(); 
        }
        int sz = V.size();
        int last_found = sz;
        for (int i = m - 1; i >= 0; --i) {
            if (s[i] != '0') {
                last_found = nxt[i] - 1;
            }
            nxt[i] = last_found;
        }
        last_found = -1;
        for (int i = 0; i < m; ++i) {
            if (s[i] != '0') {
                last_found = nxt[i]; 
            }
            last[i] = last_found;
        }
        vector<long long> P(sz + 1, 0);
        vector<long long> pow10(sz + 1, 1);
        
        for (int i = 0; i < sz; ++i) {
            P[i + 1] = (P[i] * 10 + V[i]) % MOD;
            pow10[i + 1] = (pow10[i] * 10) % MOD;
        }
        vector<int> answer;
        answer.reserve(queries.size());

        for (const auto& q : queries) {
            int l = q[0];
            int r = q[1];

            int L_nz = nxt[l];
            int R_nz = last[r];

            if (L_nz > R_nz || L_nz == sz || R_nz == -1) {
                answer.push_back(0);
                continue;
            }

            long long len = R_nz - L_nz + 1;
            long long x = (P[R_nz + 1] - (P[L_nz] * pow10[len]) % MOD + MOD) % MOD;
            long long digit_sum = prefSum[r + 1] - prefSum[l];
            long long ans = (x * digit_sum) % MOD;
            answer.push_back(ans);
        }

        return answer;
    }
};
```
## Complexity Analysis:
**Time Complexity**: $O(m+Q)$\
**Space Complexity**: $O(m)$\
where $m$ is the length of the string and $Q$ is the number of queries.
