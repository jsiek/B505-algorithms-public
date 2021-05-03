# Code Review: Edit Distance 

## Dynamic programming version

```java
public static int editDistance(String word1, String word2) {
  int n = word1.length();
  int m = word2.length();
  int [][] d = new int[n + 1][m + 1];
  for (int i = 0; i < n + 1; i++) {
	d[i][0] = i;
  }
  for (int j = 0; j < m + 1; j++) {
	d[0][j] = j;
  }
  for (int i = 1; i < n + 1; i++) {
	for (int j = 1; j < m + 1; j++) {
	  int left = d[i - 1][j] + 1;
	  int up = d[i][j - 1] + 1;
	  int left_up = d[i - 1][j - 1];
	  if (word1.charAt(i - 1) != word2.charAt(j - 1))
		left_up += 1;
	  d[i][j] = Math.min(left, Math.min(up, left_up));
	}
  }
  return d[n][m];
}
```

Time complexity of dynamic programming edit distance: O(n m).

## Recursive version with memoization:

```java
public static int editDistanceRecursive(int i, int j, 
							  int [][] d, String word1, String word2) {
  if (d[i][j] != Integer.MAX) {
	return d[i][j];
  } else if (j == 0) {
	d[i][0] = i;
	return d[i][j];
  } else if (i == 0) {
	d[0][j] = j;
	return d[i][j];
  } else {
	int left = editDistanceRecursive(i - 1, j, d, word1, word2) + 1;
	int up = editDistanceRecursive(i, j - 1, d, word1, word2) + 1;
	int left_up = editDistanceRecursive(i - 1, j - 1, d, word1, word2);
	if (word1.charAt(i - 1) != word2.charAt(j - 1))
	  left_up += 1;
	d[i][j] = Math.min(left, Math.min(up, left_up));
	return d[i][j];
  }
}
```

Time complexity for recursive, memoized: same as for dynamic programming, O(n m).

## Recursive version without memoization:

```java
public static int editDistanceRecursive(int i, int j, 
                                    String word1, String word2) {
  if (j == 0) {
	return i;
  } else if (i == 0) {
	return j;
  } else {
	int left = editDistanceRecursive(i - 1, j, word1, word2) + 1;
	int up = editDistanceRecursive(i, j - 1, word1, word2) + 1;
	int left_up = editDistanceRecursive(i - 1, j - 1, word1, word2);
	if (word1.charAt(i - 1) != word2.charAt(j - 1))
	  left_up += 1;
	return Math.min(left, Math.min(up, left_up));
  }
}
```

Time complexity for recursive, non-memoized:

T(n) = 3T(n-1)  in O(3^n)
     = 3 3 T(n-2)
	 = 3 3 3 T(n-3)
	 = 3 3 3 ... 1


# Heap Data Structure

Motivations:

1. Want a data structure that returns the maximum-keyed
   element of a set, that is, a data structure called a priority
   queue. The key's are the priorities.

2. Want to do in-place sorting in O(n log(n)).

3. Want to be able to change the key (priority) associated with an element.

**Definition.** A *max heap* is a tree in which the key of each node
is greater-than or equal to the keys of its children.

This is called the **heap property** or "max-heap property".  One can
instead create a min-heap by flipping this around.
	
Example:

                            __16__
                           /      \
                         14        10
                        /  \      /  \
                       /    \    /    \
                      8      7  9      3
                     / \    /
                    2   4  1
    
## Array Representation

Store elements in an array left-to-right, one level at a time, top-to-bottom.

                  0  1  2  3  4  5  6  7  8  9 
                [16,14,10, 8, 7, 9, 3, 2, 4, 1]
    
Def. A **heap** is an array viewed as a nearly complete binary tree.
Given an array A, the root of the tree is stored at A[0] and for a
node stored at index i, the left child is stored at index 2i + 1 and the
right child is stored at index 2(i + 1).

    left(i) = 2i + 1
    
    right(i) = 2(i + 1) = 2i + 2
    
    parent(i) = ⌊(i - 1)/2⌋
	
This kind of heap has nothing to do with the usage of "heap" as a
synonym for the main memory of a computer.
  
    
## Overview of the Heap operations

* `build_max_heap` creates a heap from an unordered array in O(n).
* `maximum` returns maximum element in O(1).
* `extract_max` removes the max in O(log(n)).
* `sortInPlace` sorts an array in-place in O(n  log(n)).
* `increase_key` updates the key of an element in the heap in O(log(n)).
* `insert` adds a new element to a heap in O(log(n)).

## Heap class

	public class Heap<E> {
			ArrayList<E> data;
			BiPredicate<E,E> lesseq;
			...
	}
    
* `max_heapify` auxilliary function

Many of the heap operations need to turn an array that is almost a max
heap, except for one element, into a max heap. The `max_heapify`
operation moves the element at position i into the correct position.

Suppose that the tree rooted at i is not a max heap, but the subtrees
left(i) and right(i) are max heaps.

Example: 4 is smaller than its left child.  To turn this
subtree into a heap, we swap it with the larger child and repeat.

                         ___16___
                        /        \
                      *4*         10
                     /  \        /  \
                    /    \      9    3
                   14     7
                  / \    /
                 2   8  1

becomes

                         ___16___
                        /        \
                      14          10
                     /  \        /  \
                    /    \      9    3
                  *4*     7
                  / \    /
                 2   8  1

becomes

                         ___16___
                        /        \
                      14          10
                     /  \        /  \
                    /    \      9    3
                   8      7
                  / \    /
                 2  *4* 1


What is the time complexity of max_heapify? O(log(n))

Another example:

                         ___16___
                        /        \
                      20          10
                     /  \        /  \
                    /    \      /    \
                   4      7    9      3
                  / \    /
                 2  14  1


## `build_max_heap` method

	void build_max_heap() {
		int last_parent = data.size() / 2 - 1;
		for (int i = last_parent; i != -1; --i) {
			max_heapify(i, data.size());
		}
	}
    
Why does this procedure work? What is the loop invariant?
Answer: the invariant is that the trees rooted at
  positions from i+1 to the end are max heaps.

What is the time complexity of `build_max_heap`?
Answer: O(n log(n)) is the easy answer, but not tight. 

The tight upper bound is O(n) which can be obtained by
observing that the time for `max_heapify` depends on the
height of the node, and `build_max_heap` calls `max_heapify`
many times on nodes with a low height.

Consider how many nodes there can be in an n-element heap at each
height. The worst cast is a complete tree. For example,

           n = 7
           
              _o_        height 2, 1 node
             /   \
            o     o      height 1, 2 nodes
           / \   / \
          o   o o   o    height 0, 4 nodes

Number of nodes at each height:

Height 0: n / 2     =  n / 2^(0+1)  =  n (1/2)^1
Height 1: n / 4     =  n / 2^(1+1)  =  n (1/2)^2
Height 2: n / 8     =  n / 2^(2+1)  =  n (1/2)^3

In general, there are at most n (1/2)^(h+1) nodes at a given height h.
    
So we can sum these up, from h=0 to log(n), times a O(h) cost 
to run `max_heapify` on each node:

    sum_(h=0)^log(n) n O(h) (1/2)^(h+1) =  O( n sum_(h=0)^log(n) h (1/2)^(h+1) )
    
      n ( (1/2)^1 + 1(1/2)^2 + 2(1/2)^3 + 3(1/2)^4 + ... + log(n) (1/2)^log(n) )
	< n ( 1(1/2)^1 + 2(1/2)^2 + 3(1/2)^3 + 4(1/2)^4 + ... )

recall formula A.8: 
    
       sum_(h=0)^∞ h x^h  =  x / (1 - x)², for |x| < 1
       
let x = 1/2
       
       sum_(h=0)^∞ h (1/2)^h  =  (1/2) / (1 - 1/2)²  =  (1/2) / (1/4) = 2
        
so we get
    
    sum_(h=0)^log(n) h (1/2)^h  <  2
    
Thus O( n sum_(h=0)^log(n) h (1/2)^h = O( 2 n ) = O(n).


What if 8 elements:

              _o_        height 3, 1 node
             /   \
            o     o      height 2, 2 nodes
           / \   / \
          o   o o   o    height 1, 4 nodes
         /
	    o                height 0, 1 nodes


## `maximum` method

	public E maximum() {
		return data.get(0);
	}
	
Time complexity: O(1)	
