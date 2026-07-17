# Day 17 Problem 3312: Sorted GCD Pair Queries

**Difficulty**: Hard

- [Leetcode Problem 3312](https://leetcode.com/problems/sorted-gcd-pair-queries/?envType=daily-question&envId=2026-07-17)

## Problem Statement
You are given an integer array `nums` of length `n` and an integer array `queries`.\
Let `gcdPairs` denote an array obtained by calculating the GCD of all possible pairs `(nums[i], nums[j])`, where $0 \le i < j < n$, and then sorting these values in ascending order.\
For each query `queries[i]`, you need to find the element at index `queries[i]` in `gcdPairs`.\
Return an integer array `answer`, where `answer[i]` is the value at `gcdPairs[queries[i]]` for each query.\
The term `gcd(a, b)` denotes the greatest common divisor of `a` and `b`.

### Constraints
-> $2 \le n == nums.length \le 10^{5}$\
-> $1 \le nums[i] \le 5 \times 10^{4}$\
-> $1 \le queries.length \le 10^{5}$\
-> $0 \le queries[i] < n \times \frac{ (n - 1)}{2}$

## Approach:

### Idea:
Let `M` be the maximum value present in nums ($M \le 5 \times 10^{4}$).\
For any integer `i` from `1` to `M`, we can easily count how many numbers in `nums` are multiples of `i`. Let this count be `C[i]`.\
The total number of pairs whose GCD is a multiple of `i` is given by the combination formula: $F[i]=\frac{C[i] \times C[i]-1}{2}$

`F[i]` includes pairs whose exact GCD is `i`, but it also includes pairs whose GCD is a multiple of `i`.\
To find the exact number of pairs with a GCD of exactly `i` (let's call this $G[i]$), we can iterate backwards from `M` down to `1` and subtract the counts of its multiples: $$G[i] = F[i] - \sum_{j=2}^{\lfloor M/i \rfloor} G[j \times i]$$

Once we have the array `G` where `G[i]` represents the number of pairs with an exact GCD of `i`, we can construct a prefix sum array. The prefix sum at index `i` tells us the cumulative count of pairs with a GCD less than or equal to `i`.
Since the problem asks for the elements of the sorted gcdPairs array at specific query indices, we can use `std::upper_bound` on the prefix sum array to determine which GCD value corresponds to each query index.

```cpp
#include <vector>
#include <algorithm>
#include <cmath>

using namespace std;

class Solution {
public:
    vector<int> gcdValues(vector<int>& nums, vector<long long>& queries) {
        int max_val = *max_element(nums.begin(), nums.end());
        vector<int> count(max_val + 1, 0);
        for (int num : nums) {
            count[num]++;
        }
        vector<long long> G(max_val + 1, 0);
        for (int i = max_val; i >= 1; --i) {
            long long multiples_count = 0;
            for (int j = i; j <= max_val; j += i) {
                multiples_count += count[j];
            }
            long long total_pairs = (multiples_count * (multiples_count - 1)) / 2;
            for (int j = 2 * i; j <= max_val; j += i) {
                total_pairs -= G[j];
            }
            
            G[i] = total_pairs;
        }
        vector<long long> prefix_sum(max_val + 1, 0);
        for (int i = 1; i <= max_val; ++i) {
            prefix_sum[i] = prefix_sum[i - 1] + G[i];
        }
        vector<int> ans;
        ans.reserve(queries.size());
        for (long long q : queries) {
            auto it = upper_bound(prefix_sum.begin(), prefix_sum.end(), q);
            ans.push_back(distance(prefix_sum.begin(), it));
        }
        
        return ans;
    }
};
```

## Complexity Analysis:
**Time Complexity**: $O(n+M \log M+q \log M)$\
**Space Complexity**: $O(M)$\
Where $M=max(nums) \le 5 \times 10^{4}$.

