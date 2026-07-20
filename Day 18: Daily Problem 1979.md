# Day 18 Problem 1979: Find Greatest Common Divisor of Array

**Complexity**: Easy

- [Leetcode Problem 1979](https://leetcode.com/problems/find-greatest-common-divisor-of-array/?envType=daily-question&envId=2026-07-18)

## Problem Statement
Given an integer array `nums`, return the greatest common divisor of the smallest number and largest number in `nums`.

The greatest common divisor of two numbers is the largest positive integer that evenly divides both numbers.

### Constraints
-> $2 \le nums.length \le 1000$\
-> $1 \le nums[i] \le 1000$

## Approach:

### Idea:
You use `std::minmax_element` from the `algorithm` library. This function traverses the array in a single pass to locate the positions of both the minimum and maximum elements simultaneously. This avoids the overhead of sorting the array or making two separate passes.

Once the minimum `(min_it)` and maximum `(max_it)` numbers are retrieved, you use `std::gcd` from the `numeric` library. Under the hood, this function utilizes Euclid's algorithm, which efficiently computes the greatest common divisor by repeatedly replacing the larger number with its remainder when divided by the smaller number until the remainder is zero.

```cpp
#include <vector>
#include <algorithm>
#include <numeric>

class Solution {
public:
    int findGCD(std::vector<int>& nums) {
        auto [min_it, max_it] = std::minmax_element(nums.begin(), nums.end());
        return std::gcd(*min_it, *max_it);
    }
};
```
## Complexity Analysis:
**Time Complexity**: $O(N+log(min(min_val, max_val)))$\
**Space Complexity**: $O(1)$

 
