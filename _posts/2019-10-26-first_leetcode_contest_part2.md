---
layout: post
mathjax: true
published: true
title: "LeetCode Weekly Contest 159-Part II"
date: "2019-10-26"
category: [Programming]
---

* The contest consists of 4 problems: one easy, two medium and one hard problem. 

* This is part 2, one medium and one hard problems.

* I was able to finish this problems after read through other's solution...QAQ.

### [Problem 3: Replace the Substring for Balanced String](https://leetcode.com/problems/replace-the-substring-for-balanced-string/)

* How to solve:
    1. First initialize with: character count - (length / 4).
    2. Maintain two pointers to indicate interval of substring that needs to be replaced.
    3. Increment right pointer and decrease count of pointed char until there are no char count > 0.
    4. Increment left pointer and add back count of pointed char until there are no char count <= 0.
    5. Repeat 3&4. Terminate when right pointer reach the end of string.


* My solution after seeing the discussion:
    * Runtime: 32ms
    * Memory: 10 MB


{% raw %}
```cpp
class Solution {
public:
    int balancedString(string s) {

        int target = s.size() / 4;
        vector<int> count (4, -target);
        unordered_map<char, int> index ({{'Q',0}, {'W',1}, {'E',2}, {'R',3}});
        int result = s.size();

        for (int i = 0; i < s.size(); i++) {
            count[index[s[i]]]++;
        }
        if (count[0] == 0 && count[1] == 0 && count[2] == 0 && count[3] == 0) return 0;

        int start = 0;
        int end = 0;
        while (end < s.size()) {
            while (end < s.size() && (count[0] > 0 || count[1] > 0 || count[2] > 0 || count[3] > 0)) {
                count[index[s[end]]]--;
                end++;
            }

            while (start <= end && count[0] <= 0 && count[1] <= 0 && count[2] <= 0 && count[3] <= 0) {
                count[index[s[start]]]++;
                start++;
            }

            if (end - start + 1 < result) {
                result = end - start + 1;
            }
        }

        return result;
    }
};
```
{% endraw %}


* What I learn:
    * Since we need to find consecutive interval(substring) to replace, we need to think of a sliding window.
    * Since we can replace any char in the window, we need to focus on the char count outside the window.
    

### [Problem 4: Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/)

* First think of dynamic programming, and I know we need a container to record max profit up to that time.
* But I cannot construct the formula all by myself.
* I first try using vector and vector[i] is the max profit upto ith smallest endTime.

* How to solve: use map\<endTime, max profit\> to record
    * Sort jobs by endTime.
    * For each job, find the first record whose endTime > job startTime.
        * Get the previous compatible profit + current job profit as **P**.
        * Compare **P** with current max profit with latest endTime.
        * If **P** is larger, record new \<endTime, max profit\> to the map.


* My solution:
    * Runtime: 216ms
    * Memory: 32.1 MB

{% raw %}
```cpp
class Solution {
public:
    int jobScheduling(vector<int>& startTime, vector<int>& endTime, vector<int>& profit) {

        map<int, int> max_profit {{0,0}}; // endTime, profit
        vector<vector<int> > jobs;
        int n = startTime.size();

        for (int i = 0; i < n; i++) {
            jobs.push_back({endTime[i], startTime[i], profit[i]});
        }
        sort(jobs.begin(), jobs.end());

        vector<int> job;
        for (int i = 0; i < n; i++) {

            job = jobs[i];
            /* 1. max_profit.upper_bound(job[1]): find the first endTime that > job startTime
               2. prev: get previous iterator of 1.
               3. ->second: get its profit
            ===> Previous compatible profit + job[2] (new task profit)
            */
            int cur = prev(max_profit.upper_bound(job[1]))->second + job[2];

            // compare current profit to current maximum profit
            if (cur > max_profit.rbegin()->second) {

                // New job has larger endTime, only add to map if profit more.
                max_profit[job[0]] = cur;
            }
        }
        return max_profit.rbegin()->second;
    }
};
```
{% endraw %}

* What I learn:
    * From the discussion, this is similar to the knapsack problem.
    * Needs to utilize STL binary search function to get upper/lower bound of some value.
        * Use upper_bound to find previous compatible tasks. (if all tasks are compatible, then return the last.)
    * map if the OrderedDict in python, its keys are ordered as they are inserted.


* Wrapping up:
    * The goal for participating the weekly contest is to motivate me to improve my data structure and algorithm skills.
    * Data structure and algorithms are essential for programmers.
    * Hope I can learn more and improve more from these contests!!

