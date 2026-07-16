# Day 16 Problem 3867: Sum of GCD of Formed Pairs

**Difficulty**: Medium

- [Leetcode Problem 3867](https://leetcode.com/problems/sum-of-gcd-of-formed-pairs/description/?envType=daily-question&envId=2026-07-16)

## Problem Statement
You are given an integer array `nums` of length `n`.\
Construct an array `prefixGcd` where for each index `i`:
- Let $mx_{i} = max(nums[0], nums[1], ..., nums[i])$.
- $prefixGcd[i] = gcd(nums[i], mx_{i})$.

After constructing `prefixGcd`:
- Sort `prefixGcd` in non-decreasing order.
- Form pairs by taking the smallest unpaired element and the largest unpaired element.
- Repeat this process until no more pairs can be formed.
- For each formed pair, compute the gcd of the two elements.
- If `n` is odd, the middle element in the `prefixGcd` array remains unpaired and should be ignored.

Return an integer denoting the sum of the GCD values of all formed pairs.\
The term `gcd(a, b)` denotes the greatest common divisor of `a` and `b`.

### Constraints:
-> $1 \le n==nums.length \le 10^{5}$\
-> $1 \le nums[i] \le 10^{9}$

## Approach:

### Idea:
First, we need to keep track of the maximum value seen so far while iterating through the array. For each element, we compute the Greatest Common Divisor (GCD) between the element itself and this running maximum $mx_{i}$.\
Now, after populating `prefixGcd`, we sort it in non-decreasing order. To form pairs of the smallest and largest unpaired elements, we use a Two-Pointer technique. Place one pointer (left) at the beginning and another (right) at the end of the sorted array.\
While `left < right`, we calculate `std::gcd(prefixGcd[left], prefixGcd[right])`, add it to our running `total sum`, and move the pointers closer together `(left++, right--)`. If `n` is odd, the pointers will meet at the middle element `(left == right)`, which skips it as instructed.

```cpp
#include <vector>
#include <numeric>
#include <algorithm>

class Solution {
public:
    long long gcdSum(std::vector<int>& nums) {
        int n = nums.size();
        std::vector<int> prefixGcd(n);
        
        int currentMax = 0;
        for (int i = 0; i < n; ++i) {
            currentMax = std::max(currentMax, nums[i]);
            prefixGcd[i] = std::gcd(nums[i], currentMax);
        }
        

        std::sort(prefixGcd.begin(), prefixGcd.end());
        
        long long totalSum = 0;
        int left = 0;
        int right = n - 1;

        while (left < right) {
            totalSum += std::gcd(prefixGcd[left], prefixGcd[right]);
            left++;
            right--;
        }
        
        return totalSum;
    }
};
```

## Complexity Analysis:
**Time Complexity**: $\mathcal{O}(n \log n + n \log(\text{maxVal}))$\
**Space Complexity**: $O(n)$
