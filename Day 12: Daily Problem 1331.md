# Day 12 Problem 1331: Rank Transformation of an Array

**Difficulty**: Easy

- [Leetcode Problem 1331](https://leetcode.com/problems/rank-transform-of-an-array/?envType=daily-question&envId=2026-07-12)

## Problem Statement
Given an array of integers `arr`, replace each element with its rank.\
The rank represents how large the element is. The rank has the following rules:
- Rank is an integer starting from `1`.
- The larger the element, the larger the rank. If two elements are equal, their rank must be the same.
- Rank should be as small as possible.

### Constraints
-> $0 \le arr.length \le 10^{5}$\
-> $-10^{9} \le arr[i] \le 10^{9}$

## Approach:

### Idea:
First, create a copy of the original array and sort it in ascending order.\
Iterate through the sorted array and assign a rank to each unique element using a hash map `(std::unordered_map)`. We start the rank at `1` and increment it only when we encounter a new, unique element.\
Loop through the original array and replace each element with its corresponding rank looked up from our hash map.

```cpp
#include <vector>
#include <algorithm>
#include <unordered_map>

class Solution {
public:
    std::vector<int> arrayRankTransform(std::vector<int>& arr) {
        std::vector<int> sortedArr = arr;
        std::sort(sortedArr.begin(), sortedArr.end());
        std::unordered_map<int, int> rankMap;
        int rank = 1;
        for (int num : sortedArr) {
            if (rankMap.find(num) == rankMap.end()) {
                rankMap[num] = rank;
                rank++;
            }
        }
        for (int i = 0; i < arr.size(); i++) {
            arr[i] = rankMap[arr[i]];
        }
        
        return arr;
    }
};
```

## Complexity Analysis:
**Time Complexity**: $O(NlogN)$\
**Space Complexity**: $O(N)$
