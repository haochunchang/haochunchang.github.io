---
layout: post
mathjax: true
published: true
title: "LeetCode Weekly Contest 160-Part I"
date: "2019-10-27"
category: [Programming]
---

* The contest consists of 4 problems: one easy, two medium and one hard problem. 

* This is part 1, one easy and one medium problems.

### [Problem 1: Find Positive Integer Solution for a Given Equation](https://leetcode.com/problems/find-positive-integer-solution-for-a-given-equation/)

* Thoughts:
    * Since we only know that equation is constantly increasing, we just need to search x,y up to given z.


* My solution after seeing the discussion:
    * Runtime: 12ms
    * Memory: 8.7 MB


{% raw %}
```cpp
class Solution {
public:
    vector<vector<int>> findSolution(CustomFunction& customfunction, int z) {
        vector<vector<int> > solutions;
        
        for (int i = 1; i < z+1; i++) {
            for (int j = 1; j < z+1; j++) {
                vector<int> sol;
                if (customfunction.f(i, j) == z) {
                    sol.push_back(i);
                    sol.push_back(j);
                    
                }
                if (sol.size() > 0) {
                    solutions.push_back(sol);
                }
            }
        } 
        
        return solutions;
    }
};
```
{% endraw %}
 

### [Problem 2: Circular Permutation in Binary Representation](https://leetcode.com/problems/circular-permutation-in-binary-representation/)

* First thought:
    * Depth-First search the possible binary tree.
    * Each level of tree is one-digit away from parent.
    * Too slow for this problems, but can be applied to other data types.


* First solution:
    * Runtime: 736 ms
    * Memory: 74.5 MB

{% raw %}
```cpp
class Solution {
public:
    vector<int> circularPermutation(int n, int start) {

        vector<int> stack = {start};

        vector<int> base (n, 0);
        int tmp = 1;
        for (int i = 0; i < n; i++) {
            base[i] = tmp;
            tmp *= 2;
        }

        int cur = 0;
        int nw = 0;
        int target_depth = pow(2, n);
        int current_depth = 0;
        vector<int> path;
        set<int> found;

        while (stack.size() != 0) {

            cur = stack.back();
            if (found.find(cur) != found.end()) {
                stack.pop_back();
                continue;
            }
            path.push_back(cur);

            found.insert(cur);
            stack.pop_back();

            for (int i = 0; i < n; i++) {
                nw = cur^base[i];
                stack.push_back(nw);
            }

            if (current_depth == target_depth) {

                for (int i = 0; i < n; i++) {
                    nw = (*path.rbegin()) ^ base[i];
                    if (nw == start) break;
                }

                path.clear();
                path.push_back(start);
                found.clear();
                current_depth = 1;

            }
            current_depth++;
        }


        return path;
    }
};
```
{% endraw %}


* How to solve:
    * Use XOR operator and right shift operator to get the "gray-code".


* Solution:
    * Runtime: 176 ms
    * Memory: 14.3 MB


{% raw %}
```cpp
class Solution {
public:
    vector<int> circularPermutation(int n, int start) {
        // from https://leetcode.com/problems/circular-permutation-in-binary-representation/discuss/414203/JavaC%2B%2BPython-4-line-Gray-Code
        
        vector<int> res ((1<<n), 0);
        for (int i = 0; i < (1 << n); ++i) {
            res[i] = start ^ i ^ (i >> 1); // first right shift and then XOR
        }
        return res;
    }
};
```
{% endraw %}


* What I learn:
    * Right shift operator is executed first and then XOR.
    * (1 << n) means 2^n.
    * Gray code of n bits can be constructed from Gray code of n-1 bits by copying two times in different orders and prepend with '0' to the first half, '1' to the second half.
    * Why using (XOR i) and (XOR i>>1) can produce the gray code?

