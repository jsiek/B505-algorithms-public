# Dynamic Programming (March 2)

Overview
* recursive solutions to problems with optimial substructure
* memoization for problems with overlapping subproblems
* bottom-up memoization, which is called **dynamic programming**

The term **dynamic programming** was coined by Richard Bellman in 1950
as a way to describe his work to a US senator. Bellman was solving
optimization problems at RAND Comporation for the Air Force and
came up with an efficient technique based on bottom-up memoization.
The term "programming" was meant in the sense of creating a plan
(e.g. the "program" for an opera). The term "dynamic" was chosen
partialy because it sounded cool and partly because of its connection
to time-varying behaviour in physics.

## The rod-cutting problem

Given 
1) a rod of length n and
2) a price function that maps rod-lengths (integers) to dollars, 

How should you cut up the rod to maximize the amount of money?

Example table:

	length: 1  2  3  4  5  6  7  8  9 10
	price:  1  5  8  9 10 17 17 20 24 30

Let p_i be the price for a continuous rod of length i
(the second row in the above table).

Let r_n be the optimal total price for a rod of length n.

n | r_n
--|----------------
0 | 0
1 | 1   (no cuts)
2 | 5   (no cuts)
3 | 8   (no cuts)
4 | 10 = p_2 + p_2
5 | 13 = p_2 + p_3
6 | 17  (no cuts)
7 | 18 = p_1 + p_6
8 | 22 = p_2 + p_6


## Recursive solution to rod cutting problem

For each possible first cut, recursively solve the rest, then take the
max of the possibilities.
	  
    r_n = max_{1≤i≤n} (p_i + r_{n-i})

We define a class named `CutResult` to store the solution to
a subproblem. In this case, the location of the cut, the
cost, and a pointer to the solutions for the sub-subproblems.

```java "CutResult class"
class CutResult {
   CutResult(int c, int amt, CutResult r) { cut = c; cost = amt; rest = r; }
   int cut;
   int cost;
   CutResult rest;
}
```

The recursive function `cut_rod` implements this algorithm.

```java "cut rod"
static CutResult cut_rod(int[] P, int n) {
   if (n == 0) { // base case
	  return new CutResult(0, 0, null);
   } else { // recursive
	  CutResult best_yet = null;
	  for (int i = 1; i != n+1; ++i) {
		 CutResult rest = cut_rod(P, n - i);
		 int cost = P[i] + rest.cost;
		 if (best_yet == null || best_yet.cost < cost) {
			best_yet = new CutResult(i, cost, rest);
		 }
	  }
	  return best_yet;
   }
}
```

## Optimal Substructure Property

Recall that a problem has optimal substructure if an optimal solution
to the problem is based on optimal solutions to subproblems.

1. every solution involves making a choice that leaves
   one or more subproblems to be solved. e.g., the first-cut choice.
2. assume that the choice has been made
3. characterize the space of subproblems
4. show that using optimal solutions to the subproblems
  always gives you a better solution for the overall problem
  then using non-optimal solutions to the subproblems.

Real-life counter example: choosing an outfit to wear in the morning.

# Pattern for recursive solution to optimal substructure

1. figure out how to identify sub-problems
   e.g., length of remaining rod to cut
2. base case handles smallest sub-problems
   e.g., rod of length 0
3. recursive case
	a) try all possible choices of decomposing current problem into subproblems
	b) recursively solve the subproblems
	c) compute the solution based on each alternative
	d) choose the best one

## Overlapping Subproblems

        cut_rod(5)
        |- cut_rod(4)
        |  |- cut_rod(3)
        |  |- cut_rod(2)
        |  |  |- cut_rod(1)
        |  |  |- cut_rod(0)
        |  |- cut_rod(1)
        |  |  |- cut_rod(0)
        |  |- cut_rod(0)
        |- cut_rod(3)
        |  |- cut_rod(2)
        |  |  |- cut_rod(1)
        |  |  |- cut_rod(0)
        |  |- cut_rod(1)
        |  |  |- cut_rod(0)
        |  |- cut_rod(0)
        |- cut_rod(2)
        |  |- cut_rod(1)
        |  |  |- cut_rod(0)
        |  |- cut_rod(0)
        |- cut_rod(1)
        |  |- cut_rod(0)
        |- cut_rod(0)

## Memoization (Cache)

1. Record each solution that you find.

2. Before computing a solution, first check to see if it has already
been solved, and if so, just lookup the solution.


## Student exercise: implement a memoized version of cut_rod.

Solution:

```java "cut rod (memoized)"
static int cut_rod_memo(int[] P, int n) {
   CutResult[] R = new CutResult[n+1];
   return cut_rod_memo_aux(P, n, R).cost;
}

static CutResult cut_rod_memo_aux(int[] P, int n, CutResult[] R) {
   if (R[n] != null) {
	  return R[n];
   } else if (n == 0) {
	  R[n] = new CutResult(0, 0, null);
	  return R[n];
   } else {
	  CutResult best = null;
	  for (int i = 1; i != n+1; ++i) {
		 CutResult rest = cut_rod(P, n - i);
		 int cost = P[i] + rest.cost;
		 if (best == null || best.cost < cost) {
			best = new CutResult(i, cost, rest);
		 }
	  }
	  R[n] = best;
	  return best;
   }
}
```
