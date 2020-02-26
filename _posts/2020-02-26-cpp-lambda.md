---
layout: post
title:  "C++中的 Lambda"
date:   2020-02-26
categories: programming
---


最近因为想要找工作的缘由开始刷一刷 LeetCode，自己比较熟悉 Python ，同时有些题目
可能也会用 `C++` 来写，一些地方用 lambda 比较方便，`C++` 中的 `lambda` 语句中以
前不是很熟悉的地方用的时候出了一点问题，这里面简单记录一下。

# 问题

做到一个最长不重复字符的问题的时候，需要用到 `lambda` ，随手去搜索了一下，自己常
用的语法是 `auto f = [&]() {}` ，但是随手一搜看到了另外一种语法，  `auto f =
[=]() {}` ，自己没有多想，就拿过来用了。

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int len = s.length();
        if (len <= 1) return len;

        unordered_map<char, int> cnt;
        // 这个地方一开始用的是传值的方式来捕获变量
        auto unique_char = [=]() {
            for (auto& rec: cnt) {
                if (rec.second > 1) return false;
            }
            return true;
        };

        int i = 0;
        int j = 1;

        vector<int> candi;
        cnt[s[0]] = 1;
        while (j < len) {
            while (!unique_char()) {
                cnt[s[i]] -=1;
                i++;
            }
            cout << i << "," << j << endl;

            candi.push_back(j-i);


            if (cnt.count(s[j]) > 0)
                cnt[s[j]] += 1;
            else
                cnt[s[j]] = 1;

            j++;

        }
        return *max_element(candi.begin(), candi.end());
    }
};
```

中间标记的地方一开始是用的传值的方式来捕获变量，这里用到的需要捕获的变量为 `cnt`

因为是传值的方式捕获就使得在执行到这一行的时候这个变量已经被拷贝了，之后的 `cnt`
的变化也就不会对这个 `lambda` 产生任何作用。

所以这里面的 `lambda` 捕获的时候需要按引用来捕获外部变量，这样才会在调用这个函数
的时候得到想要的结果。

