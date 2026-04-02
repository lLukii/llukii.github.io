---
layout:     post
title:      "Gulag Sort"
subtitle:   "Less brutal Stalin Sort"
date:       2026-4-01 12:00:00
author:     "lLukii"
header-img: "img/dark-mode-otherworld-wallpaper-v0-utqp151a9e7b1.png"
catalog: true
mathjax: true
tags: 
    - Computer Science
---
The other day, I was scrolling through Instagram and found this thing called Stalin Sort. Essentially, given an array of numbers $a$, Stalin Sort iterates from left to right, and eliminates any number less than the previously largest number found. The joke is that Stalin Sort purges any element that doesn't form an increasing sequence with the previously added numbers.


Consider this list of integers:
$$2,5,4,7,9,3,10$$


When Stalin Sort reaches the 3rd element, the largest previous element it found was $5$. However, since the 3rd element is $4$, which is less than $5$, it is erased from the list of numbers. However, it will include the 4th element $7$ since it is greater than $5$. In the figure below, numbers colored blue are included in the final sequence, while the ones in red are purged.


$$\blue{2},\blue{5},\red{4},\blue{7},\blue{9},\red{3},\blue{10}$$


This algorithm runs in $\mathcal{O}(n)$ time, and a basic C++ implementation would look something like this:
```cpp
#include <bits/stdc++.h>
using namespace std;


int main(){
   int n; cin >> n;
   vector<int> workers(n);
   for(int i = 0; i < n; i++){
       cin >> workers[i];
   }
   vector<int> not_purged;
   int prev_max = INT32_MIN;
   for(int num: workers){
       if(num > prev_max){
           not_purged.push_back(num);
           prev_max = num;
       }
   }
   for(int num: not_purged) cout << num << " ";
}
```


This got me thinking. The problem with Stalin Sort is that it is quite *corrupt*. If you're familiar with DSA, you might recognize Stalin Sort as greedily forming an increasing subsequence. However, this approach isn't always optimal, as it's not guaranteed to produce the *longest* increasing subsequence, the lineup that rightfully includes the most numbers (or workers ig :p)


We can use these numbers: $8, 1, 2, 3, 4, 5$ as a simple counterexample. Applying Stalin Sort would only keep $8$ and purge $1,2,3,4,5$. However, $1,2,3,4,5$ also forms an increasing subsequence, so it's probably more ideal to keep these and purge $8$. So yeah, Stalin sort is... brutal. Perhaps a little too brutal. What if we purge workers, but as little as possible? Gulag Sort, anyone?


This actually translates to a well-known CS problem called longest increasing subsequence (LIS). A relatively straightforward approach of tackling this question involves Dynamic Programming, which solves problems by merging overlapping subproblems. Let's define $dp[i]$ as the length of the LIS of $a[1\dots i]$ (the first i elements of the array $a$) including $a[i]$. Notice that the LIS of $a[1\dots i]$ can be formed by simply joining $a_i$ with the LIS of some prefix $a[1\dots k]$ where $1\le k< i$ and $a_k<a_i$, which is simply $dp[k]$. Thus, maximizing $dp[k]$ maximizes $dp[i]$. To generalize $dp$, we have


$$dp[i]=\begin{cases}
1 \text{ if } i=1\\
\max_{k<i,a[k]<a[i]}(dp[i], dp[k]+1) \text{ if } i>1
\end{cases}$$


The LIS length is simply the maximum element in $dp$.


However, $dp$ only returns the length of the LIS, not the actual subsequence itself. To fix this issue, we create another array $p$, where $p[i]$ corresponds to the index $k$ that maximizes $dp[i]$. To determine the subsequence, we propagate through $p$ from the index that maximizes $dp$ until we reach the start of the array and append the corresponding elements in $a$ while doing so.


Phew! That was a lot of work for more worker representation huh? Here's a C++ implementation to guide you:


```cpp
#include <bits/stdc++.h>
using namespace std;


int main(){
   int n; cin >> n;
   vector<int> workers(n);
   for(int i = 0; i < n; i++){
       cin >> workers[i];
   }
   vector<int> dp(n, 1), p(n, -1);
   for(int i = 1; i < n; i++){
       for(int k = 0; k < i; k++){
           if(dp[k] + 1 > dp[i] &&
                   workers[k] < workers[i]){
               dp[i] = dp[k] + 1;
               p[i] = k;
           }
       }
   }
   int idx = 0, lis = 0;
   for(int i = 0; i < n; i++){
       if(dp[i] > lis){
           lis = dp[i];
           idx = i;
       }
   }
   vector<int> not_purged;
   while(idx != -1){
       not_purged.push_back(workers[idx]);
       idx = p[idx];
   }
   reverse(not_purged.begin(), not_purged.end());
   for(int num: not_purged) cout << num << " ";
}
```
Unfortunately, this approach is actually *quite* slow—it runs in $\mathcal{O}(n^2)$ time, meaning that this process is very inefficient for larger arrays (or worker groups). The Soviets obviously cannot invest this much time into purging people, so we need a better workaround without going back to Stalin Sort.


Let's reconsider how we formulate $dp$: let $dp[l]$ represent the smallest element that ends an increasing sequence of length $l$. Initially, $dp[0]=-\infty$ and $dp[l]=\infty$ for $l>0$.


When we try to assign $a[i]$ to $dp[l]$, we need some way to "extend" the LIS of a subsequence with length $l-1$. Since it's ideal to minimize $dp[l]$, as this creates more room for future elements, we can extend $dp[l-1]$ with $a[i]$ if $dp[l-1]<a[i]<dp[l]$. With this setup, we can make two key observations: 1. $dp$ is sorted in increasing order, and 2. each $a[i]$ corresponds to at most one $dp[l]$ (If two elements corresponded to the same $dp[l]$, then the former condition wouldn't hold).


With these conditions, it's possible to use binary search to find the value $l$ where $dp[l-1]<a[i]<dp[l]$ without worrying about conflicts. This approach runs in $\mathcal{O}(n\log n)$ time, which shouldn't make the Soviets too mad :p


To restore the subsequence, we can use the same $p$ array as before and also maintain a separate array $idx$ where $idx[l]$ tells us the index of the element corresponding to $dp[l]$.


```cpp
#include <bits/stdc++.h>
using namespace std;


int main(){
   int n; cin >> n;
   vector<int> workers(n);
   for(int i = 0; i < n; i++){
       cin >> workers[i];
   }
   vector<int> dp(n+1, INT32_MAX), p(n, -1), idx(n+1, -1);
   dp[0] = INT32_MIN;
   for(int i = 1; i < n; i++){
       int worker = workers[i];
       int l = upper_bound(dp.begin(), dp.end(), worker) - dp.begin();
       if(dp[l-1] < worker && worker < dp[l]){
           dp[l] = worker;
           idx[l] = i;
           p[i] = idx[l-1];
       }
   }
   int ans = 0, start_idx = 0;
   for(int i = 1; i <= n; i++){
       if(dp[i] != INT32_MAX){
           ans = i;
           start_idx = idx[i];
       }
   }
   vector<int> not_purged;
   while(start_idx != -1){
       not_purged.push_back(workers[start_idx]);
       start_idx = p[start_idx];
   }
   reverse(not_purged.begin(), not_purged.end());
   for(int num: not_purged) cout << num << " ";
}
```


Actually, another more intuitive way of doing $\mathcal{O}(n\log n)$ LIS (colloquially Gulag Sort) is using the original $dp$ formulation, but optimizing the inner loop with a Point-Update-Range-Query data structure like a Binary Indexed Tree. However, this approach is also much harder to implement (and has difficulty adjusting to larger values), so I won't cover it as thoroughly.