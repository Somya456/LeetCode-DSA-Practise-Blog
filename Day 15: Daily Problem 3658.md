# Day 15 Problem 3658: GCD of Odd and Even Sums

**Difficulty**: Easy

- [Leetcode Problem 3658](https://leetcode.com/problems/gcd-of-odd-and-even-sums/?envType=daily-question&envId=2026-07-15)

## Problem Statement
You are given an integer `n`. Your task is to compute the GCD (greatest common divisor) of two values:
- `sumOdd`: the sum of the smallest `n` positive odd numbers.
- `sumEven`: the sum of the smallest `n` positive even numbers.

Return the GCD of `sumOdd` and `sumEven`.

### Constraints
-> $1 \le n \le 1000$

## Approach:

### Idea:
Sum of the first odd `n` numbers: $1+3+5+.....+(2n-1) = n^{2}$\
Sum of the first even `n` numbers: $2+4+6+.....+2n = n(n+1)$

We need to find `GCD(sumOdd, sumEven)` which is, $GCD(n^{2}, n(n+1))$.\
Using the properties of GCD we can factor out `n`:
- $$GCD(n \cdot n, n \cdot (n + 1)) = n \cdot GCD(n, n + 1)$$

Since `n` and `n+1` are two consecutive integers, they are always coprime. Therefore, $\text{GCD}(n, n + 1) = 1$.\
Substituting this back into the equation:
- $GCD(n^{2}, n(n+1)) = n.1 = n$

Thus, the answer is always simply `n`.

```cpp
class Solution {
public:
    int gcdOfOddEvenSums(int n) {
        return n;
    }
};
```
## Complexity Analysis:
**Time Complexity**: $O(1)$\
**Space Complexity**: $O(1)$


