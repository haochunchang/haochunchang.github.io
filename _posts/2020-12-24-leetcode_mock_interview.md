---
layout: post
mathjax: true
published: true
title: "LeetCode Mock Interview - onSite interview"
date: '2020-12-24'
category: [Programming]
---

### [Power of Four](https://leetcode.com/problems/power-of-four/)

* Description: Given an integer ```n```, return ```true``` if it is a power of four.

An integer ```n``` is a power of four, if there exists an integer ```x``` such that ```n == 4^x```

* My solution:
* First version: using loop to keep dividing by 4 until n is not divisible by 4 or equals 1.

```python
def isPowerOfFour(self, n: int) -> bool:
    if n == 0:
        return False

    while n % 4 == 0:
        n /= 4

    if n == 1:
        return True
    return False
```

* Second version: use 3 mathematical properties to determine n.
* Good explanation from [this discussion post](https://leetcode.com/problems/power-of-four/discuss/80460/1-line-C%2B%2B-solution-without-confusing-bit-manipulations)
    * [Explanation of ```(num - 1) % 3 == 0```](https://leetcode.com/problems/power-of-four/discuss/80460/1-line-C++-solution-without-confusing-bit-manipulations/119156)


```c++
bool isPowerOfFour(int num) {
    // bool positive = num > 0;
    // bool isPowerOfTwo = (num & (num - 1)) == 0;
    // bool divisibleByThree = (num - 1) % 3 == 0; // By binomial theorem
    return num > 0 && (num & (num - 1)) == 0 && (num - 1) % 3 == 0;
}
```

* Iteration is intuitive.
* Needs some sense of mathematics to come up with the second version.

---

### [Exclusive Time of Functions](https://leetcode.com/problems/exclusive-time-of-functions/)

* Initially, the direction was correct but I did not store the previous timestamp. 
Therefore, the difference between two starting functions cannot be computed.
That's the last step to the solution.

* The solution:
    * I used deque as stack to store the function id.
    * Timestamp from previous function was saved to compute the time difference between current call and prevTimeious call.

```python
def exclusiveTime(self, n: int, logs: List[str]) -> List[int]:
    # times[i] is the exclusive time for function i
    times = [0 for i in range(n)]

    # Stack for storing IDs of unfinished functions
    funcIDs = deque([
        int(logs[0].split(":")[0])
    ])
    prevTime = int(logs[0].split(":")[2])
    for log in logs[1:]:

        idx, mode, time = log.split(":")
        idx = int(idx)
        time = int(time)

        if mode == "start":
            # If there were started functions,
            # we added time for prevTimeious function.
            if len(funcIDs) > 0:
                old_id = funcIDs[-1]
                times[old_id] += time - prevTime
            funcIDs.append(idx)
            prevTime = time

        elif mode == "end":
            # The last function has finished
            old_id = funcIDs.pop()
            times[old_id] += time - prevTime + 1
            prevTime = time + 1
    return times
```

---

### [Text Justification](https://leetcode.com/problems/text-justification/)

* The solution:

```python
def fullJustify(self, words: List[str], maxWidth: int) -> List[str]:
    res, cur, num_of_letters = [], [], 0

    for w in words:

        # If the current word cannot fit into this line.
        if num_of_letters + len(w) + len(cur) > maxWidth:
            # Evenly distribute extra spaces to each word.
            for i in range(maxWidth - num_of_letters):
                cur[i % (len(cur) - 1 or 1)] += ' '

            res.append(''.join(cur))
            cur, num_of_letters = [], 0

        cur += [w]
        num_of_letters += len(w)

    return res + [' '.join(cur).ljust(maxWidth)]
```

The key is to evenly distribute extra spaces with:
```python
cur[i % (len(cur) - 1 or 1)] += ' '
```
This statement appends space to each member of ```cur``` in rotation.
