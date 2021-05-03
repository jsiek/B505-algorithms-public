# Midterm Review

## Asymptotic Time Complexity (Big-O)

### Big-O: Upper Bound

**Definition** Suppose g is a function. O(g) is the set of all
functions that grow at the same rate or slower than g.
To be precise, for some function f, we have
f ∈ O(g)
iff
there exists numbers n₀ and c such that
for all n ≥ n₀, 0 ≤ f(n) ≤ c g(n).

We say that g is an *asympototic upper bound* for f.
(Or just "upper bound" for short.)

We write f ≾ g to mean f ∈ O(g).

### Omega: Lower Bound

Ω(g) is the set of all functions that grow at the same rate or greater
than g.

f ∈ Ω(g)  means g ≾ f

### Theta: Tight Bound

Θ(g) is the set of all functions that grow at the same rate as g.

f ∈ Θ(g)  means f ≍ g

### Example

2 log n + 10 ∈ O(n)

n  | log n | 2 log n + 10
---|-------|-------------
2  | 1     | 12
4  | 2     | 14
8  | 3     | 16
16 | 4     | 18
32 | 5     | 20
64 | 6     | 22

Choose c = 1, n₀ = 32

### Example

10 n + 5 ∈ O(n)

n | 10 n + 5| 11n
--|---------|----
1 | 15      | 11
2 | 25      | 22
3 | 35      | 33
4 | 45      | 44
5 | 55      | 55
6 | 65      | 66

Choose c = 11, n₀ = 5

### Ordering of Complexity Classes

    1  ≾  log n  ≾  √n  ≾  n  ≾  n log n  ≾  n²  ≾  n³  ≾  2ⁿ

n log 4 ∈ O(n)
n log 5 ∈ O(n)
4 log n ∈ O(log n)
5 log n ∈ O(log n)

4^n  <  5^n

4^n = 4 * ... * 4

5^n = 5 * ... * 5

4^n * (5/4)^n = 5^n


5 n^2 ∈ O(n^2)

3 n^2 ∈ O(n^2)

5 n^2 ≍ 3 n^2

log(log(n)) ≾ log(n) ≾ log^2 n

log^(2)n = log(log(n))
log^(3)n = log(log(log(n)))

log^2 n = (log(n))^2
log^3 n = (log(n))^3

### Arithmetic with big-O

O(f) + O(g) = O(max(f,g))

O(f) * O(g) = O(f * g)


## Loop Invariants

Example: 

Recall the definition of fibonacci numbers:

    F(0) = 0
    F(1) = 1
    F(n) = F(n-1) + F(n-2)       if n > 1

Here's an array-based implementation

    static int fib_array(int n) {
        int A[] = new int[n + 2];
        A[0] = 0;
        A[1] = 1;
        int i = 2;
        while (i != n + 1) {
            A[i] = A[i-1] + A[i-2];
            i = i + 1;
        }
        return A[n];
    }

What's the loop invariant of the while loop?

1. start with the goal, that fib_array returns the correct value:

         A[n] == F(n)

2. generalize that to talk about the loop when it is part-way done

         for every j in [0,i)  A[j] == F(j)

3. Initialization: true before the loop starts

         i == 2
         for every j in [0,2)  A[j] == F(j)
		 A[0] == 0 == F(0), A[1] == 1 == F(1)

4. Maintenance: becomes true again at end of loop body

         // for every j in [0,i)  A[j] == F(j)
         A[i] = A[i-1] + A[i-2];
		 // for every j in [0,i+1)  A[j] == F(j)
         i = i + 1;
		 // for every j in [0,i)  A[j] == F(j)

5. Termination: loop invariant + negated condition implies goal

         i == n + 1
         for every j in [0,i)  A[j] == F(j)
		 implies
         for every j in [0,n+1)  A[j] == F(j)
		 implies
		 A[n] == F(n)

Binary Search (loop version)

	(result == -1
	 && !contains(A, key, 0, low)
	 && !contains(A, key, high, A.length))
	|| A[result] == key

Goal: 
   suppose result = BinarySearch(A, 0, A.length, key)

   (result == -1 && !contains(A, key, 0, A.length))
   ||
   (A[result] == key)



## Divide and Conquer

1. Divide the problem into smaller subproblems.

2. Conquer each subproblem recursively.

3. Combine the solutions for the subproblems to create a solution.

examples: Binary Search, Merge Sort, Polynomial Multiplication, Matrix Multiplication

### Merge Sort

1. Divide: cut the array in half

2. Conquer: recursively apply Merge Sort to both halves

3. Combine: Merge the two halves

```java "MergeSort"
static void MergeSort(int[] A, int begin, int end) {
	if (begin + 1 < end) {               // O(1)
		int middle = (begin + end) / 2;  // O(1)
		MergeSort(A, begin, middle);
		MergeSort(A, middle, end);
		Merge(A, begin, middle, end);    // O(n)
	}
}
```

## Recurrence Formulas

For Merge Sort:

    T(n) = 2 T(n/2) + n

### Binary Search with slicing

    T(n) = 1 T(n/2) + n

```java
public class BinarySearch {
    static int[] slice(int A[], int begin, int end) {
        int[] S = new int[end - begin];
        for (int i = begin; i != end; ++i) {
            S[i - begin] = A[i];
        }
        return S;
    }

    static int search_slice(int A[], int key) {
        if (A.length == 0)
            return -1;
        int mid = A.length / 2;
        if (key < A[mid]) {
		    left = slice(A,0, mid);              // O(n)
            return search_slice(left, key);
        } else if (key > A[mid]) {
		    right = slice(A, mid + 1, A.length); // O(n)
            int result = search_slice(right, key);
            if (result < 0) return result;
            else return mid + 1 + result;
        } else if (A[mid] == key)
            return mid;
        else
            return -1;
    }
}
```


## Master Theorem

**Theorem** (Master Theorem)
Suppose

    T(n) = a T(n/b) + O(nⁱ)
    
and a > 0, b > 1, i >= 0.
There are three cases:

1. Root wins: If i > log_b(a), then T(n) ∈ O(nⁱ).

2. Equal levels: If i = log_b(a), then T(n) ∈ O(nⁱ log n).

3. Leaves win: If i < log_b(a), then T(n) ∈ O(n^(log_b(a))).

### Example (Merge Sort)

    a = 2, b = 2, i = 1
    log_2(2) = 1 = i     (equal levels)
	T(n) ∈ O(n log n).

## Master theorem doesn't apply

1. guess and then do the substitution check/proof/method
2. analyze the recurrence tree manually
3. unfold the recurrence a few times

T(n) = T(n-2) + n^2
     = (T(n-4) + (n-2)^2) + n^2
     = (T(n-6) + (n-4)^2) + (n-2)^2 + n^2
	 = ... + (n-4)^2 + (n-2)^2 + n^2
       (n/2 many terms)
	 < n^2 + ... + n^2
	   (n/2 many terms)
     = n^3
T ∈ O(n^3)


## Sorting

### Insertion Sort

### Quick Sort

median of medians

### Counting Sort

## Greedy Algorithms

### Knapsack (0-1 versus fractional)


0-1 Knapsack
   Maximize calories fit into 5lb
   choose a food item, or not

   option 1:  follow greedy choice but not optimal
   4lb of burrito 500 calories

   option 2:  doesn't follow greedy choice and optimal
   2lb of pancakes 300 calories
   3lb of raisins  300 calories
   

Fractional Knapsack
   Maximize calories fit into 5lb
   can choose a fraction of a food item

Greedy choice:
  choose up to 5lb of food item with max calories/lb

### Activity Selection

### Huffman Codes

