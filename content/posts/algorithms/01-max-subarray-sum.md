---
title: "Maximum Subarray / Kadane's Algorithm"
date: 2021-01-23T09:19:29-04:00
slug: "kadane-max-subarray-sum"
description: "Algorithm prep questions / posts index"
keywords: ["algorithms", "kadane's algorithm", "arrays", "traversal", "dp", "maximum subarray sum"]
draft: false
tags: ["algorithms", "sum", "arrays", "traversal", "sum", "medium-hard", "math", "dynamic-programming"]
math: false
toc: true
---

##### Spiciness: 🌶️🌶️🌶️ (halapeño)

## Problem Statement

Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return _its sum_.

```javascript
  /* Input: */ [-2, 1, -3, 4, -1, 2, 1, -5, 4]
  /* Output: */ 6
  /* Explanation: [4,-1,2,1] has the largest sum = 6. */
```

## tl;dr

* Track maximum sum encountered
* Track "current" highest sum
* Reset "current" sum if its value drops below `0`
* Think of it as a sliding window problem (two pointers)

## Solution

### Brief

Let's ignore the O(n³) solution that we sums up all the possible subarrays looking for the highest sum.

I'm not sure why this is considered an "easy" problem on LC, I find it quite unintuitive. It's obvious that some variable needs to hold the maximum sum, but when in the weeds of iterating through the array it gets confusing on what to keep track of and when.

### All Positive

If all numbers were positive...

```javascript
[1, 2, 5, 15, 3, 1, 5] // => 32
```

... there would be no reason not to just sum up all the elements. Easy enough. The caveat is the negative numbers: when does a negative "interrupt" the subsequence? In order to find the max sum, we need to keep track of the ongoing, "current" sum or "sum so far."

So when's is it appropriate to ignore an element? Let's see a subsequence with a negative number that "resets" the current sum and the one that does not.

### With / Without Reset

A simple example **with** a reset:

```javascript
        [1, 2, 5, -15, 3, 1, 5]
//                 ^ reset at -15!
// sum:  1  3  8  -7   3  4  9
// ---
// max:  1  3  8   8   8  8  9
```

We sum `[1, 2, 5]` but then `-15` drops the current sum from `8` to `-7`. New starting point becomes `3`. Max sum encountered at that point is `8`. Summing together the remaining `[3,1,5]`, we arrive at the answer which is the max of `9`.

Another example **without** a reset:

```javascript
        [1, 2, 5, -7, 99, 1, 1]
//                ^ no reset!
// sum:  1  3  8  1  100 101 102
// ---
// max:  1  3  8  8  100 101 102
```

Even though the current sum decreases, it never falls below zero. the very next element continues the contiguous chain. A decrease isn't the end of the chain if the value is still positive. Remember the all positives example!

### Two Candidates

```javascript
        [1, 2, 5, -7, -5, 99, 5]
//                    ^ reset at -5!
// sum:  1  3  8  1   -4  99 104
// ---
// max:  1  3  8  8    8  99 104
```
Note that there are two candidates here: `[1, 2, 5]` and `[99, 5]`.

Regardless of what follows `-5`, the current sum is now a negative number and as such, there is no way even adding a large number like `99` can make the chain _contiguous_. If that's still confusing, think of it this way:

### Final Example

```javascript
        [-1, -2, 0, -8, -3, 248, 164, 320]
```

**At every element in the array we have to decide if we'd be better off adding the next element to the tally or starting over**. If the previous "run" is negative, start over.

In the above example, once we reach `248`, the "prefix" (`[... -3,`) can be ignored because there is no way it would contribute to _increasing_ the sum. If there was a large enough number at the start that would compensate, then the "prefix" at the point of reaching `248` would look like this: `[... 999]`. In that case, we'd add it to the "tally": `999 + 248`. 

### Sliding Window

I find it helpful to think of this problem as a sliding window / two pointers sort of dealio:

```javascript
// Start
     [-2, 1, -3, 4, -1, 2, 1, -5, 4]
//   ^lp ^rp 
// Mid
     [-2, 1, -3, 4, -1, 2, 1, -5, 4]
//               ^lp       ^rp
//               <----sum---->
// End
     [-2, 1, -3, 4, -1, 2, 1, -5, 4]
//                                ^lp
```

If the current sum is below zero, move left pointer (start of sum). Shift right pointer (end of sum) to the right to increase the current sum. If the sum is below zero, move the left pointer to the new beginning of subarray; rinse and repeat. 

At the very start of the above example, the sum between the first two items would be `-1`, so the left pointer would move to `1` and right pointer would move to `-3`. That would make the current sum `-2`. Now the left pointer would move to `4`. Right pointer would continue to `-5` at which point the left pointer would end at `4`, exhausting the array.

While this is happening, simply keep an eye on the max sum seen so far. I think sliding window is a more intuitive way to look at this problem versus figuring out the distinction between "sum so far" and "max sum" and where to reset values.

### Solution

The reason why "sum so far" and "max sum" are handy though is that it makes for a very elegant solution, particularly in Python:

```javascript
class Solution
  def maxSubArray(self, nums: List[int]) -> int:
    maxSub = nums[0]
    curSum = 0

    for n in nums:
      if curSum < 0:
        curSum = 0
      curSum += n
      maxSub = max(maxSub, curSum)
    
    return maxSub
```

## Big O: Time & Space Complexity

Brute force solution of simply summing up all the possible subarrays runs in O(n³) because:

* nested `for` to generate subarrays runs in O(n²)
* loop to sum every subarray runs in O(n)

... giving us time complexity of O(n³) - ouch.

The above algorithm runs in one pass, using no extra memory - **O(n)** time, **O(1)** space.