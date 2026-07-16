# Day 13 Problem 1291: Sequential Digits

**Difficulty**: Medium

- [Leetcode Problem 1291](https://leetcode.com/problems/sequential-digits/?envType=daily-question&envId=2026-07-13)

## Problem Statement
An integer has sequential digits if and only if each digit in the number is one more than the previous digit.\
Return a sorted list of all the integers in the range `[low, high]` inclusive that have sequential digits.

### Constraints
-> $10 \le low \le high \le 10^{9}$

## Approach:

### Idea:
Since the digits must be sequential, instead of checking every single number in the range `[low, high]` we generate all possible sequential digits upfront, filter out the ones that don't fall within the `[low, high]` range, and return them sorted.\
First, we define the base string using the master sequence, `123456789`. Now, The length of sequential digits can range from the length of `low` to the length of `high`.\
For a fixed length `len`, we iterate through all possible starting positions in the master string. Convert each valid substring to an integer.\
Check if the generated integer falls within `[low, high]`.

```cpp
#include <vector>
#include <string>

class Solution {
public:
    std::vector<int> sequentialDigits(int low, int high) {
        std::vector<int> result;
        std::string digits = "123456789";
        for (int len = 2; len <= 9; ++len) {
            for (int start = 0; start <= 9 - len; ++start) {
                std::string sub = digits.substr(start, len);
                int num = std::stoi(sub);
                
                if (num >= low && num <= high) {
                    result.push_back(num);
                }
            }
        }
        
        return result;
    }
};
```
## Complexity Analysis:
**Time Complexity**: $O(1)$\
**Space Complexity**: $O(1)$
