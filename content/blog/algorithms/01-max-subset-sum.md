---
title: "Kadane's Algorithm Variation: Max Subset Sum"
date: 2020-08-23T09:19:29-04:00
slug: "kadane-max-subset-sum"
description: "Algorithm prep questions / posts index"
keywords: ["algorithms", "kadane's algorithm", "arrays", "traversal", "dp", "maximum subset sum"]
draft: true
tags: ["algorithms", "arrays", "traversal", "sum", "medium-hard", "math", "dynamic-programming"]
math: false
toc: true
---

## Problem

Write a function that takes in an array of positive integers and returns the maximum sum of non-adjacent elements in the array. Return `0` if sum can't be generated.

```javascript
  // input
  const array = [75, 105, 120, 75, 90, 135]
  maxSubsetSumNoAdjacent(array) // returns 330 because 
                                // 75 + 120 + 135 = 330
```

## What's the catch?

This is a variation of Kadane's algorithm. The catch here is that, unlike Kadane's algorithm, the elements making up the sum can't be adjacent. In the problem array, possible `maxSum` candidates can be `[75, 120, 135]` or `[105, 75, 135]` but never `[75, 105, 120]`.

As the input array grows, so does the possibility we may need to skip over elements. 

## At first glance

Since we are tasked with finding a sum, it's fair to 

## Takeaway