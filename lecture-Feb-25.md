# Optimization Problems (Feb. 25)

An **optimization problem** is a problem in which the goal is to find
an optimal solution among a set of feasible solutions.

**Example** Suppose you are preparing to hike the Appalachian Trail.
Select food from a grocery store that will fit in your backpack while
maximizing the number of calories.  You have room for 5 pounds of food
in your backpack. (This is the 0-1 Knapsack Problem.)

In general, an optimization problem defines a
* predicate that says which solutions are **feasible** and a
* **metric** that maps solutions to a number.

In the above knapsack problem, the
* feasible solutions are food selections up to 5 pounds and the
* metric is the sum of the calories in the selection.


## Optimal Substructure property

Some optimizations problems can be solved using a divide-and-conquer
strategy because they have a property called optimal substructure.

**Definition** A problem has *optimal substructure* if an optimal
solution to the problem can be constructed from optimal solutions for
subproblems.

The 0-1 Knapsack Problem has optimal substructure. Consider an optimal
solution for the 5 pound limit that consists of n items. If you remove
any one of the items, say a burrito weighing 1 pound, you have an
optimal solution for the subproblem of maximizing calories for 4
pounds.


## Greedy-Choice Property

**Definition** A problem has the *greedy-choice* property is an
optimal solution can be constructed using locally optimal choices.

The 0-1 Knapsack Problem does not have the greedy-choice property.
However, a small change to the problem gives it this property.

The Fractional Knapsack Problem is like the 0-1 Knapsack Problem
except that you are allowed to select a fraction of an item from the
grocery store. This problem has the greedy-choice property because one
can iteratively select an item with the highest ratio of calories to
weight, and if neccessary, cut the item into a fraction to fit into
the backpack.

Small changes in problem statements can cause significant changes in
which techniques and algorithms can be used to solve them.
Because the Fractional Knapsack Problem has the greedy-choice
property, it can be solved using a Greedy Algorithm that is especially
efficient. The 0-1 Knapsack Problem is more difficult and requires the
use of Dynamic Programming.  We study Greedy Algorithms this week and
Dynamic Programming next week.


# Examples of Greedy Algorithms (Feb. 25)

## Activity Selection Problem

Fit the maximum number of weddings into a single wedding hall over a
period of 20 hours, given that each wedding has a particular requested
start and end time.

    0         5        10        15        20
    |         |         |         |         |
    |-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|   acitivity #
    .                                     0,0   0
      ------                              1,4   1     *
          ----                            3,5   2
    ------------                          0,6   3
              ----                        5,7   4     *
          ------------                    3,9   5
              --------                    5,9   6
                --------                  6,10  7
                    ------                8,11  8     *
                    --------              8,12  9
        ------------------------          2,14  10
                            --------      12,16 11    *

### Brute force algorithm

Try all valid combinations:
    
    for i = 0...n-1
      select activity i, remove all conflicting activities,
      recursively select from the rest of the activities
      remember if this is the best combination seen so far

### Greedy algorithm

Can we make an optimal choice without seeing all combinations?
    
* What if we work from left to right and

* pick the activity that finishes first thereby leaving the most time
  to other activities.

Proof that this greedy choice is optimal:
      
Suppose that we have an optimal solution A, that is, a maximal set
of activities that do not conflict with each other. Now the greedy
choice is the activity x that finishes first.  If activity x is in
A, then we are done.  On the other hand, suppose the activity x is
not in A.  Let x' be the first activity to finish amongst those in
A.  We can replace x' with x to get a new solution. That is, let
A' = (A - {x'}) ∪ {x}. The set A' is also an optimal solution
because a does not conflict with any of the other activities: its
finish time is less than that of x', and therefore, less than the
start time of any other activity in A.  Also, the size of A' is
the same as A.

### Implementation of Activity Selection

```java "Activity class"
class Activity {
   Activity(int s, int f) { start = s; finish = f; }
   int start;
   int finish;
}
```

The activities are already sorted by finish time.

activity array maps activity numbers to activity objects
k is the last activity that was selected.
n is the total number of activities.


```java "activity selection"
static LinkedList<Integer>
activity_selector(Activity[] activity, int k, int n) {
   LinkedList<Integer> A;
   int m = k + 1;
   // skip over activities that start too soon
   while (m < n && activity[m].start < activity[k].finish)
      m += 1;
   if (m < n) {
      A = activity_selector(activity, m, n);
      A.add(m);
      return A;
   } else {
      return new LinkedList<Integer>();
   }
}
```

Recurrence Formula

    T(n) = T(n-1) + O(n)
	
Time complexity:

    O(n^2)

Tighter upper bound, based on analyzing the accesses to the activity
array:

    O(n)


## Huffman Codes

Suppose we've got a long DNA sequence such as the following
      
    CTCT CTCT CTCT AGCT AGCC AGCC TGAA CATC CATC CTCT CATC ...

and want to store it using less space

Idea: use shorter binary codes for higher-frequency words.

	  CTCT: 4 occurrences
	  CATC: 3
	  AGCC: 2
	  AGCT: 1
	  TGAA: 1

Also, to avoid needing a separator, use a *prefix code*. That is, make
sure that no code is a prefix of another code.

The parsing algorithm is: read the first code, then the second, and so
on.

To parse an individual code, use a Finite-State Automata in the shape
of a tree.

Example:

A message uses letters A-H with the following number of occurences.

    A:10, B:1, C:1, D:2, E:6, F:2, G:1, H:1

* Fixed-length encoding uses 3 bits for each character
  A=000, B=001, C=010, D=011, etc.

                  _/\_
			    _/    \_
			  0/        \1
			  /          \
			 /\          /\
		   0/  \1      0/  \1
		   /    \      /    \
		 0/\1  0/\1  0/\1  0/\1
		 A  B  C  D  E  F  G  H
		 
    24 total characters in the message to be encoded.
	
    Total length is 72.

* Variable length encoding:
    A=0(1), B=11000(5), C=11001(5), D=1101(4), 
    E=10(2), F=1110(4), G=11110(5), H=11111(5).

		   0/\1
		   /  \
		  A  0/\1
			 /  \
			E  0/\1
			  _/  \_
			 /      \
		   0/\1    / \1
		   /  D  0/ 0/\1
		 0/\1    F  /  \
		 B  C      G    H

   Code-length multiplied times number of occurences yields the
   following for each letter:

    A=10, B=5, C=5, D=8, E=12, F=8, G=5, H=5
              
    Total length is 58.

In general, the total number of bits to encode a string s using tree T
is given by

    bits(s, T) = Σ(c∈C) freq(c,s) × depth(T,c)

where depth(T,c) is the depth of character c in the tree T and
freq(c,s) is the frequency of character c in s (i.e. number of
occurences).

### Greedy algorithm for building a variable-length Huffman code:
      
We work from back to front with respect to the encoding, choosing
which bits for which words. We want to pick the lowest frequency
words, because the longest codes go the farthest back.

Intuition: make the choice that uses the fewest bits for the final
encoded string.

1. Put the words (singleton trees) into a min priority queue
where the priority is the frequency of the word.

2. Pop two trees from the queue, create a subtree whose frequency
   is the sum of the two:

		2
		|- B[1]
		|- C[1]

3. Push the new subtree into the queue.
4. Go back to 2. as long as there is more than one item in the queue. 

		24
		|-A[10]
		|-14
		  |-E[6]
		  |-8
			|-4
			| |-2
			| | |- B[1]
			| | |- C[1]
			| |-D[2]
			|-4
			  |-F[2]
			  |-2
				|- G[1]
				|_ H[1]

### How to encode a string using a Huffman code:

* Build a dictionary mapping each word to its code.

	recursively walk through the tree, keeping track
	of the current path, and add to the dictionary
	when you get to a leaf.

* Iterate through the string and translate each word
  by using the dictionary.

### Student group work: 

Create the Huffman code (tree) for the following alphabet with
frequencies.

	   a  b  c  d  e f
	   ---------------
	   45 13 12 16 9 5

a solution:

				   0/\1
				   /  \  
				  55   a(45)
				0/  \1
				|     \
				30      25
			  0/  \1  0/  \1
			 14    d  b    c
		   0/  \1
		   f    e

Decode the following message, encoded using the above Huffman tree.

	 000010110001

solution:
    
    face
