---
layout: post
mathjax: true
published: true
title: "LeetCode Weekly Contest 159-Part I"
date: "2019-10-20"
category: [Programming]
---

* The contest consists of 4 problems: one easy, two medium and one hard problem. 


### [Problem 1: Check If It Is a Straight Line](https://leetcode.com/problems/check-if-it-is-a-straight-line/) 

* Description: You are given an array coordinates, coordinates[i] = [x, y], where [x, y] represents the coordinate of a point. Check if these points make a straight line in the XY plane.


* Needs aware of:
    * Vertical and horizontal lines.
    * Data type in number division (int / int => int, needs to cast to double in C++).


* My solution:
    * Runtime: 12ms
    * Memory: 10.2 MB


```cpp
bool checkStraightLine(vector<vector<int>>& coordinates) {

    int dx = (coordinates[1][0] - coordinates[0][0]);
    int dy = (coordinates[1][1] - coordinates[0][1]);
    bool vertical = false;
    bool horizontal = false;
    double slope = 0.0;

    if (dx == 0) {
        vertical = true;
    } else if (dy == 0) {
        horizontal = true;
    } else {
        slope = static_cast<double>(dy) / static_cast<double>(dx);
    }

    int test_dx = 0;
    int test_dy = 0;
    double test_slope = 0.0;

    for (int i = 2; i < coordinates.size(); i++) {
        test_dx = coordinates[i][0] - coordinates[0][0];
        test_dy = coordinates[i][1] - coordinates[0][1];
        if (vertical) {
            if (test_dx != 0) return false;
        } else if (horizontal) {
            if (test_dy != 0) return false;
        } else {
            test_slope = static_cast<double>(test_dy) / static_cast<double>(test_dx);
            if (test_slope != slope) return false;
        }
    }
    return true;
}
```

-------
### [Problem 2: Remove Sub-Folders from the Filesystem](https://leetcode.com/problems/remove-sub-folders-from-the-filesystem/) 


* Description: Given a list of folders, remove all sub-folders in those folders and return in any order the folders after removing.


* First try solution:
    * Construct the whole filesystem tree with node as fullpath, then depth-first-search to collect non-subfolders.
    * This use up a lot of memory, but have some kind of tree for other use?
    * Runtime: 880 ms
    * Memory: 235.9 MB


```cpp
struct Tree {
    string root = "";
    unordered_map<string, set<string> > adj;
};

vector<string> removeSubfolders(vector<string>& folder) {
    
    unordered_map<string, bool> real_name;
    //vector<Tree> trees;
    Tree tree;
    
    // parse every folder into vector of trees
    for (int i = 0; i < folder.size(); i++) {
        
        vector<string> sub_folders;
        
        size_t prev_slash = 0;
        size_t next_slash = folder[i].find("/", 1);
        string next_folder = folder[i].substr(0, next_slash);
        sub_folders.push_back(next_folder);
        
        while (next_slash != std::string::npos) {
            
            prev_slash = next_slash;
            next_slash = folder[i].find("/", prev_slash+1);
            next_folder = folder[i].substr(0, next_slash);
            
            sub_folders.push_back(next_folder);
        }
        //cout << "adding: " << next_folder << " to real_name\n";
        real_name[next_folder] = true;
        
        // construct into tree
        tree.adj[tree.root].insert(sub_folders[0]);
        
        for (int j = 0; j < sub_folders.size()-1; j++) {
            //cout << "inserting: " << sub_folders[j+1] << " to " << sub_folders[j] << "\n";
            tree.adj[sub_folders[j]].insert(sub_folders[j+1]);
        }
        tree.adj[sub_folders[sub_folders.size()-1]].insert("");
    }
    
    vector<string> results;
    vector<string> stack;
    for (auto it = tree.adj[tree.root].begin(); it != tree.adj[tree.root].end(); it++) {
        if ((*it).length() > 0) {
            //cout << "pushing: " << *it << " to stack\n";
            stack.push_back(*it);
        }
    }
    
    while (stack.size() > 0) {
        
        /*cout << "Stack: ";
        for (auto it = stack.begin(); it != stack.end(); it++) {
            cout << *it << " ";
        }
        cout << "\n";*/
        
        string now = stack.back();
        stack.pop_back();
        
        if (real_name[now]) {
            results.push_back(now);
        } else {
            // for all children of 'now'
            for (auto it = tree.adj[now].begin(); it != tree.adj[now].end(); it++) {
                if ((*it).length() > 0) {
                    //cout << "pushing: " << *it << " to stack\n";
                    stack.push_back(*it);
                }
            }
        }
    }
    
    //cout << "\n";
    return results;
}
```


* Needs aware of: (after looking at solutions from other.)
    * Folder paths can be sorted by string length, then binary search a target string will find the location of the target's parent (right before target).
    * A String-kind problem.


* Faster solution (from [discussion](https://leetcode.com/problems/remove-sub-folders-from-the-filesystem/discuss/408949/C%2B%2B-Short-sorting-O(nlogn)):    
    * Runtime: 244 ms
    * Memory: 35.7 MB


```cpp
vector<string> removeSubfolders(vector<string>& folder) {
    sort(folder.begin(), folder.end());
    vector<string> result;
    vector<string>::iterator it;
    for(string &s: folder) {
        it = lower_bound(result.begin(), result.end(), s);
        if(it != result.begin()) {
            --it;
        }
        if (result.empty() || it->compare(0,it->length(),s,0,it->length()) != 0 || s[it->length()] != '/') {
            result.push_back(s);
        }
    }
    return result;
}
```


* Final Notes:
    * Tree/Graph data structure needs to be practiced more.
    * Needs to review problem types more carefully.


### Part2 will cover the other two problems.
