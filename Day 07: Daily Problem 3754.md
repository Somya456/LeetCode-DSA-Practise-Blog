# Day 7 Problem 3754: Concatenate Non-Zero Digits and Multiply by Sum - 1

**Difficulty**:Easy

- [Leetcode Problem 3754](https://leetcode.com/problems/concatenate-non-zero-digits-and-multiply-by-sum-i/description/?envType=daily-question&envId=2026-07-07)

## Problem Statement
You are given an integer `n`.\
Form a new integer `x` by concatenating all the non-zero digits of `n` in their original order.\
If there are no non-zero digits, `x = 0`.\
Let `sum` be the sum of digits in `x`.\
Return an integer representing the value of `x * sum`.

### Constraints
-> 0 <= n <= $10^{9}$

## Approach:

### Idea:
The Problem describes processing an integer `n` by extracting its non-zero digits, forming a new number `x` from them in their original order then calculating the sum of digits of `x` and returning `x*sum`.\
First, we extract non-zero digits using `n%10` from right to left in reverse order, we can reverse these digits at the end or collect them in a string or array and then reverse it.\ 
Then we create a new integer `x` by putting the extracted non-zero digits in their correct positional weights and calculate the sum of digits.\
Finally, we return `x*sum`.

```cpp
class Solution {
public:
    long long sumAndMultiply(int n) {
        if (n == 0) return 0;
        long long x = 0;
        long long sum = 0;
        long long place = 1;
        while (n > 0) {
            int digit = n % 10;
            if (digit != 0) {
                x = x + digit * place;
                place *= 10;
                sum += digit;
            }
            n /= 10;
        }
        return x * sum;
    }
};
```
## Complexity Analysis:
Time Complexity: $O(log_{10} n)$ \
Space Complexity: O(1)
