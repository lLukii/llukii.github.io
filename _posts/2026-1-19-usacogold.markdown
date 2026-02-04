---
layout:     post
title:      "Trying USACO again :skull:"
subtitle:   "Yeah I'm cooked"
date:       2026-1-19 12:00:00
author:     "lLukii"
header-img: "img/dark-mode-otherworld-wallpaper-v0-utqp151a9e7b1.png"
catalog: true
mathjax: true
tags: 
    - Computer Science
---

After (barely) promoting to Gold a year ago, I decided to try my hands at USACO again. Idk, I've gotten decently far, and I kind of miss the dopamine hit after getting an AC on codeforces or the green boxes on USACO. Anyways, here are some recent solves: 

**USACO Gold December 2016, P3 - Lasers and Mirrors**
We are trying to get from $(x_L,y_L)$ to $(x_B,y_B)$ through some series of intermediate points, which suggests that we can treat this as a graph problem. Construct a graph where mirrors sharing a common y-coordinate while travelling horizontally or a common x-coordinate while travelling vertically are adjacent to our current position. It suffices to run BFS, alternating our search direction each time, and finding the shortest path to any point containing $(x,y)$ where $x=x_b$ or $y=y_b$.

This question is a little annoying implementation-wise, since the coordinates can go up to $10^9$, which warrants the need for a hashmap or smth.

Time Complexity: $O(N)$ 

**USACO Gold January 2019, P1 - Cow Poetry**
Perhaps my favorite Gold question so far. A little involved, but super rewarding to solve. We can tackle this problem in two parts: how to create $K$ syllables with some rhyme, and associating each $e_i$ with some rhyme class.

For the first part, notice how we can choose each word as many times as needed, and the order that we pick two words matters. We can compute this easily using Knapsack DP. Let $dp[k]$ denote the number of ways to create a poem with $k$ total syllables. The transition is as follows:
$$dp[k]=\sum_{i=1}^{N}dp[k-{s_i}]$$
To find the number of ways that a poem ends in a specific rhyme class $c$, we fix the last word to have that rhyme. We can simply sum over all values of $dp[k-s]$, which we can call $f(c)$, where the word corresponding to $s$ has rhyme $c$. 

Part 2 is a little tricky, but very interesting. First, it's worth noting that every letter in the input can correspond to any rhyme class. Thus, the answer to this problem is essentially the sum of all possible letter combinations that the rhyme classes can take on. For the sake of brevity, consider a toy example with letters $A,B$ that appear $c(A),c(B)$ times, and rhyme classes $1$ and $2$. The number of arrangements is thus
$$f(1)^{c(A)}f(2)^{c(B)}+f(1)^{c(B)}f(2)^{c(A)}+$$
$$f(2)^{c(B)}f(2)^{c(B)}+f(1)^{c(A)}f(1)^{c(A)}$$
This expression doesn't scale nicely for large values of $M$, so we can factor
$$(f(1)^{c(A)}+f(2)^{c(A)})(f(1)^{c(A)}+f(2)^{c(B)})$$

The number of terms to compute now is much more reasonable. We can compute the exponents quickly using binary exponentiation, and take the answer modulo $10^9+7$.

Time Complexity: $O(NK+NM\log M)$


I'll probably post something related to life in general next time. More problems coming soon!
