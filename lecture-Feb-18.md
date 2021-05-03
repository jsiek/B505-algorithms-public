# Selection Problem (Feb. 18)

The selection problem is to find the kth smallest number in an array.
In other words, the number that would appear at index k in the sorted
version of the array.

What number appears at index 3 of the sorted version of the following
array?

    8 15 6 2 3 10

sorted version:

    2 3 6 8 10 15
          ^
    0 1 2 3 4  5
    

So the straightforward algorithm is to sort the array and return the
kth element. This algorithm is O(n log n).

We can do better by adapting the Quick Sort algorithm.

In particular, the Partition algorithm tells us the index of the
pivot. If that index is greater than k, then we should continue
looking in the left side of the array. If the index is less than k,
then we should continue looking in the right side. Of course, if the
index is equal to k, then the pivot is the kth smallest element.


    8 15 6 2 3 10

partition around 6

    2 3 6 8 15 10
        ^
    0 1 2 3 4  5


So we can formulate the Select algorithm following the
divide-and-conquer recipe as follows.

Divide: 
  Choose a pivot using the Median of Median's algorithm
  Partition the array around the pivot.
  If the index of the pivot is k, return the pivot. 

Conquer: recursively apply Select to either the left or right side
depending on the index of the pivot (as described above).

Combine: (trivial) return the result from the recursion.

As with Quick Sort, there are several strategies for choosing
the pivot.

The Randomized Select algorithm chooses the pivot at random.
This algorithm has an expected running time of O(n).

As we have already discussed Randomized Quick Sort in detail, we'll
move on to study a method of choosing a good pivot in linear time,
which will produce a Select algorithm that has worst-case running time
of O(n).

## Median of Medians

The Median of Medians algorithms returns an element that is guaranteed
to be greater than 30% of the elements and less than 30% of the
elements. As we have seen for Quick Sort, that is more than enough to
ensure that the recursion depth of Select is logarithmic.

The Median of Medians algorithm is as follows: 

1. divide the elements into 5 groups and find the median of each group
   (sort them and take the middle one).

   4 7 32 9 11 | 2 7 22 91 33 | 44 21 46 77 92
   4 7 9 11 32 | 2 7 22 33 91 | 21 44 46 77 92

   medians: 9 22 46

2. compute the median of all the group's medians using a recursive
 call to Select.

   answer: 22

Let's see why this produces an element that is greater than 30% 
and less than 30%.

Consider the following diagram, in which each column is a group of 5
elements, and their are n/5 of them. Each column is sorted, from
largest to smallest going down the column. The L's and G's are the
medians of each group, and the big M is the median of the medians.
The L's are all smaller or equal to M and the G's are larger than M.

|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| | | | | | | |g|g|g|g|g|g|g|
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| | | | | | | |g|g|g|g|g|g|g| 
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|L|L|L|L|L|L|M|G|G|G|G|G|G|G| 
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|l|l|l|l|l|l| | | | | | | | | 
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|l|l|l|l|l|l| | | | | | | | |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|

    n/5 columns
    5 rows

time to merge sort a column:

  O(5 log 5) = O(1)
  
  
* time to sort all the columns: O(n)
* compute the median of the middle row: T(n/5)


There are about n/10 L's below M and there are 2 elements
per group below the L's. So all told that means there are
3n/10 elements below M.

Similarly, there are about n/10 G's above M and there are 2 elements
per group avove the G's. So all told that means there are 3n/10
elements above M.

## Time Complexity of Select (with Median of Medians)

The following recurrence relation captures the runtime of Select using
Median of Medians to choose the pivot, as explained below.

    T(n) ≤ T(n/5) + T(n 7/10) + O(n)

* The T(n/5) is for the recursive call to Select in computing the
  median of the medians.

* The T(n 7/10) is for the recursive call to Select on the left or right
  side of the array.

* The O(n) is for the call to Partition and for the sorting of
  all the columns.
  
  O(n) partition
  O(n) sort the columns
  O(n) + O(n) = O(n)

So the Select Algorithm is O(n). 

The intuition is that the amount of work per-level decreases at each
level. However, we can't apply the Master Theorem directly.

Instead we can use the substitution method to check the O(n)
complexity.

    Assume T(k) ≤ 10 k  for any k < n.
    We need to show that T(n) ≤ 10 n.
    T(n) ≤ T(n/5) + T(n 7/10) + n
         = 10n/5 + 10n 7/10 + n 10/10
         = n 20/10 + n 70/10 + n 10/10
         = n 100/10
         = 10 n


# Lower bound on comparison-based sorting (Feb. 18)

**Theorem** Any comparison-based sorting algorithm performs Ω(n log n)
comparisons in the worst case to sort n elements.

## Decision Tree

Any algorithm can be represented by a decision tree that records the
comparisons performed on elements of the array.

    [a1,a2,a3]
                       [a1 ≤ a2 ≤ a3]
                      /yes
              a2 < a3?      [a1 ≤ a3 ≤ a2]
             /     \no     /yes
            /yes    a1 < a3?
    a1 < a2?               \no
           \no              [a3 ≤ a1 ≤ a2]
            \
             \          [a2 ≤ a1 ≤ a3]
              \        /yes
               a1 < a3?         [a2 ≤ a3 ≤ a1]
                       \no     /yes
                        a2 < a3?
                               \no
                                [a3 ≤ a2 ≤ a1]

The number of leaves `l` must be at least n!, which is the total
number of permutations of the array.

The worst-case running time is the depth `d` of the tree. 

The decision tree is a binary tree, so the number of nodes can at-most
double from each level to the next. So the number of leaves is bounded
by 2^d.

    2^d ≥ l

Taking the log of both sides, we have a lower bound on the depth.

    d ≥ log l

Above we observed that the number of leaves is at least n!, so we have

    d ≥ log(n!)
    
So how big is log (n!)? 

    log(n!) = log(1 ⋅ 2 ⋅⋅⋅ n)
            = log 1 + log 2 + ... + log (n/2) + ... + log n
            >= log (n/2) + ... + log n     (throw away the first half)
            >= n/2 log (n/2)
            = n/2 (log n - log 2)
            = n/2 log n - n log 2 /2
            ∈ Ω(n log n)

So we've shown that the lower bound on the number of comparisons to
sort a sequence of element is Ω(n log n). This means that Merge Sort
is asympotically optimal. In other words, no other comparison-based
sorting algorithm can do better by more than a constant factor.
