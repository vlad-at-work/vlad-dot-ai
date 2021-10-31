---
title: "Inverting a binary tree: probably easier than you think"
date: 2020-09-09T09:19:29-04:00
slug: "invert-binary-tree"
description: "Algorithms & data structures interview prep: invert a binary tree"
keywords: ["algorithms", "binary tree", "inverting binary tree"]
draft: false
tags: ["algorithms", "tree", "binary-tree", "invert", "_medium-difficulty"]
math: false
toc: true
---

##### Spiciness: 🌶️🌶️ (poblano)

## Problem Statement

Write a function which takes in a binary tree, inverts it, and returns the inverted tree. 

An inverted version of the tree above would swap every left node in the tree for its corresponding right node.

```javascript
class BinaryTree {
  constructor(value) {
    this.value = value
    this.left = null
    this.right = null
  }
}
/*
        INPUT

          1
        /   \ 
       2     3      flip me!
     /  \   / \
    4    5 6   7
   / \
  8   9            
```

Look down at your palms - they're mirror images of each other with respect to an imaginary line between your pinkies:

```javascript
/*      INPUT      |       SOLUTION
                   |
          1        |          1         
        /   \      |        /   \ 
       2     3    flip     3     2
      / \   / \    ->     / \   / \
     4   5 6   7   |     7   6 5   4
    / \            |              / \
   8   9           |             9   8   
                   |
```

## tl;dr

* The solution is as simple as reassigning left & right pointers between nodes in one pass. 
* Traversing a tree like this requires a queue to go breadth first instead of depth first.
* Leaf nodes' children, for the purposes of the queue, can be `null`; .This is important for space complexity calculations later.

## Solution

### Brief

Linked lists, trees, BSTs are often easy problems masquerading as difficult ones.

You may feel an inkling to traverse the tree depth-first (1 -> 2 -> 4) and re-construct the tree backwards somehow or traverse in some nebulous "opposite" fashion. However, the final tree is mirrored with respect to Y axis, not X. That should be a hint you're going in the wrong direction (up/down vs left/right)

Turns out, all you'll need is queue to parse the tree left to right, swapping each node's left and right values along the way. 

A binary tree's node only has so much to offer. You have`node.value`, `node.left` and `node.right`:
```javascript
/*        1                  1         
        /   \               /   \ 
       2     3    flip     3     2
```

**Once you have that, you're done**. Well, almost: you need to figure out how to apply the same actions to node 2, node 3, their children, and so on.

### Traverse left-to-right (breadth-first)

First, let's look at how to output the value of every "level" of a tree, left-to-right. As neat as recursion is, I think iterative solutions are easier to read:

```javascript
function printBinaryTreeValues(tree) {
    // initialize the queue. Our first element is the root
    const queue = [tree]
  
     // we'll be updating this guy as we go in lieu of using a recursive function
	let currentNode

    // while there something in the queue...
	while (queue.length > 0) {
        // yeet first item from the queue
		currentNode = queue.shift()

        // do something with node, in this case, print
		console.log(currentNode.value)

        // update the queue to handle the children nodes
        if (currentNode.left) queue.unshift(currentNode.left)
        if (currentNode.right) queue.unshift(currentNode.right)

        // next iteration will handle the newly added children 
        // preserving the left-to-right direction
	}
  // here, we are out of queue items
}
```

Let's go back to our smallest node "unit"...

### Swapper helper function

```javascript
/*        1
        /   \
       2     3
```

Now you swap nodes with values 2 and 3. This logic will be applied to their children too, but for now, we're only looking at this one piece. Always be on the lookout as to what logic can be pawned off to its own function: don't clutter the meat of the solution or you may come to regret it. That regret comes when you're about 80% finished confidently explaining your way through a whiteboard problem.

Quickly swap values:

```javascript
// chad ES6 swap:
function swapNodeLeftAndRight(node) {
  [node.left, node.right] = [node.right, node.left]
}
// ugly, oldschool swap:
function swapNodeLeftAndRight(node) {
  const tempLeft = node.left
  node.left = node.right
  node.right = tempLeft
}
```

### Completed solution
Finally, here is our iterative solution: (parse, swap left & right, enqueue children)

```javascript
// This is the class of the input binary tree.
class BinaryTree {
  constructor(value) {
    this.value = value
    this.left = null
    this.right = null
  }
}

function swapLeftAndRight(node) {
	[node.left, node.right] = [node.right, node.left]
}

function invertBinaryTree(tree) {
  const queue = [tree]
  let currentNode
  while (queue.length > 0) {
      currentNode = queue.shift()
      if (currentNode) swapLeftAndRight(currentNode)
      if (currentNode.left) queue.unshift(currentNode.left)
      if (currentNode.right) queue.unshift(currentNode.right)
    }
  }
```

## Big O: Time & Space Complexity

Since this algorithm runs in one pass through all the tree's nodes (no nested loops, or other shenanigans) the time complexity is **O(n)**.

As for space complexity - there's a queue growing in memory with every iteration. There's a little nuance to why this is so (touched on in the tl;dr) but the answer is **O(n) as well**, not O(1)