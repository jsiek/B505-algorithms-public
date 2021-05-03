# Review of Insertion Sort Student Solutions

## The swapping version of Insertion Sort

    static void sort(int A[]) {
         if (A.length == 0)
             return;
         for (int j = 1; j != A.length; ++j) {
             int i = j - 1;
             while (i >= 0 && A[i] > A[i+1]) {
                 swap(A, i, i + 1);
                 i = i - 1;
             }
         }
     }

The invariant of the outer loop is that [0,j) is sorted.

Consider an example execution of the inner loop.

Suppose we're part-way through the outer loop, and have the following
situation:

     1 3 5 7 4 2
             ^
             j

The inner loop initializes `i = j - 1` and decrements `i` with each
iteration. So we start here:

     1 3 5 7 4 2
           ^ ^
           i j

The inner loop moves the `4` down to the correct spot by swapping it
with the next element to the left. After the first iteration we have:

     1 3 5 7 7 2 (w/o swapping)
     1 3 5 4 7 2
         ^   ^
         i   j

Notice that the range [0,j) is no longer sorted!
Instead, the range [0,i+1) is sorted and so is [i+1,j+1).
This is the loop invariant for the inner loop.

After the second iteration we have:

     1 3 5 5 7 2 (w/o swapping)
     1 3 4 5 7 2
       ^     ^
       i     j

after the loop

     1 3 4 5 7 2 (w/o swapping)

And the loop terminates because A[i] <= A[i+1].
We know that [0,i+1) is sorted and so is [i+1,j+1).
Combined with A[i] <= A[i+1], we know the whole range [0,j+1) is sorted.

The last step of the outer loop is to increment j.

Thus, we've re-established the invariant of the outer loop:
the range [0,j) is sorted.

## The non-swapping version of Insertion Sort

    static void sort(int A[]) {
         if (A.length == 0)
             return;
         for (int j = 1; j != A.length; ++j) {
             int key = A[j];
             int i = j - 1;
             while (i >= 0 && A[i] > key) {
                 A[i + 1] = A[i];
                 i = i - 1;
             }
             A[i + 1] = key;
         }
     }

Again, the invariant of the outer loop is that [0,j) is sorted.

Consider an example execution of the inner loop.

Suppose we're part-way through the outer loop, and have the following
situation:

     1 3 5 7 4 2
             ^
             j

The inner loop initializes `i = j - 1` and decrements `i` with each
iteration. So we start here:

     key = 4

     1 3 5 7 4 2
           ^ ^
           i j

The inner loop compares the key to 7, moves 7 to the right, then
decrements i.

     1 3 5 7 7 2
         ^   ^
         i   j

Next, compare the key to 5, move 5 to the right, then decrement i.

     1 3 5 5 7 2
       ^     ^
       i     j

As we're half-way through the iterations of the inner loop, it's
a good place to stop and think about the loop invariant.
Here are some observations:

1. The range [0,i+1) is sorted. 
2. The contents of A[i+1] is garbage.
3. The range [i+2,j+1) is sorted.
4. The key is less than A[i+2].

Can the conjunction of 1,2, and 4 be the loop invariant?
Let's check whether they are true at the beginning, just before the
inner loop.

     1 3 5 7 4 2
           ^ ^
           i j

Unfortunately, part 4 is not true. A[i+2] is 2 and the key is 4.
So we have to massage part 4 a little bit:

4*. If i + 1 < j, then key < A[i+2].

Ok, so hopefully we have a strong enough loop invariant. Let's carry
on where we left off.

     1 3 5 5 7 2
       ^     ^
       i     j

Next, compare the key to 3, and terminate the loop. Put the key
into the `i + 1` position. 

     1 3 4 5 7 2
       ^     ^
       i     j

Let's check that the invariant for the inner loop is strong enough to
prove the invariant for the outer loop, which is [0,j) is sorted.
The invarient for the inner loop gives us:

    [0,i+1) is sorted
    [i+2,j+1) is sorted
    if i + 1 < j, then key < A[i+2]

and the fact that the inner loop terminated tells us:

    i < 0 or A[i] <= key

Let's consider both cases:

* Suppose i < 0. In fact, i = -1.
  Then i + 1 = 0, and j >= 1, so i + 1 < j. So key < A[i+2].
  The key is placed at location i+1 = 0.
  So A[0] < A[1]. Also, we have [1,j+1) is sorted.
  Thus, [0,j+1) is sorted.

* Suppose `A[i] <= key`.
  The key is placed at location i+1.
  We have [0,i+1) is sorted, and A[i] <= A[i+1].
  But what about the stuff to the right of A[i+1]?
  We consider two subcases:
  
    - Suppose i + 1 < j.
      We have [i+2,j+1) is sorted from the loop invariant. 
      We just need to know that A[i+1] <= A[i+2],
      which we also have from the loop invariant.

    - Suppose i + 1 >= j.
      In fact, i + 1 = j.
      Above we noted that [0,i+1) is sorted and A[i] <= A[i+1],
      so [0,i+2) is sorted, or in other words,
      [0,j+1) is sorted.

# Divide and Conquer

The Divide and Conquer recipe:

1. Divide the problem into smaller subproblems.

2. Conquer each subproblem recursively.

3. Combine the solutions for the subproblems to create a solution.


## Search

Is an item in the array?
Is 3 in [5,1,2,3,4]? Yes
Is 6 in [5,1,2,3,4]? No

Linear Search: traverse the array from left to right looking for a
match.
Time Complexity: O(n)

Can we do better? What if the array is already sorted?

## Binary Search

     [3,5,8,10,12,15,18,20,20,50,60]
     
Intuition: search as you would for a word in a dictionary.

Suppose we're checking if `12` is in the array.

* Look at the element in the midde.

         [3,5,8,10,12,15,18,20,20,50,60]
          0 1 2  3  4  5  6  7  8  9 10
         |             ^               |

  The **window** in which we're searching is currently the entire
  array, as shown the by the vertical bars.

* Compare it to the element we're looking for. If not equal,
  go right or left, again looking at the middle of that side.
  So we go left:

         [3,5,8,10,12,15,18,20,20,50,60]
          0 1 2  3  4  5  6  7  8  9 10
         |    ^         |

* Then we go right:

         [3,5,8,10,12,15,18,20,20,50,60]
          0 1 2  3  4  5  6  7  8  9 10
               |    ^   |

Notice that the window gets smaller as we search.

We'll create the algorithm using the divide and conquer recipe:

* Divide: compare the middle element to the item we're looking for
  and narrow the window to the right or left half.

* Conquer: recursively apply Binary Search to the window

* Combine: return the same result as from the subproblem

Time complexity of Binary Search:

Let's visualize how much work is performed by Binary Search.
This is called **recursion tree** method for analyzing time complexity.

* Each call to Binary Search internally does O(1) computation..

                   O(1)   (cost for divide and combine)
                  /
               O(1)
                 \
                  O(1)        len=4
                   \
                    ...       len=2
                    /
                   O(1)       len=1

* We need to determine the depth of the recursion. The following
  relates the wost-case depth `d` to the size `n` of the array:
  
          2^d = n

  We solve for `d` by taking the log of both sides.
  
          d = log n

  The depth of the recursion is O(log n). 

* We can compute an upper bound on total time by multiplying the depth
  by the amount of time for each level of the recursion.

* The time complexity of Binary Search is O(log n) * O(1) = O(log n)


## Merge Sort

Reference: pages 30-34 of CLRS textbook

* Divide: cut the array in half

* Conquer: recursively apply Merge Sort to both halves

* Combine: Merge the two halves

Merge takes two arrays of sorted elements and produces a sorted array.

* Take the front element of each input array.

* Put the smaller one into the output array and take the next element
  from it's input array.

* Repeat until we get to the end of one or both of the input arrays.
  If only one is at the end, copy the rest of the other to the output
  array.
        
Correctness of MergeSort(A, begin, end):
  pre-condition: nothing
     len = end - begin
     MergeSort(A, begin, begin + 1/2 len)
        sorted(A, begin, begin + 1/2 len) is true ! (induction hypothesis)
     MergeSort(A, begin + 1/2 len, end)
        sorted(A, begin + 1/2 len, end) is true ! (induction hypothesis)
     Merge(A, begin, begin + 1/2 len, end)
        sorted(A, begin, end)
  post-condition: sorted(A, begin, end)

Correctness of Merge(A, begin, mid, end):
   pre-condition: sorted(A, begin, begin + 1/2 len)
                  and sorted(A, begin + 1/2 len, end)
   post-condition: sorted(A, begin, end)


Let's visualize the time again. 

* Each call to Merge Sort internally does O(k) computation, where
  k is the size of the array at that point. That's because
  Merge is O(k).
  
* The total amount of work at each layer is O(n).

                      _ cn _                   = O(n)
                     /       \
                cn/2    +     cn/2             = cn in O(n)
               /    \        /     \
            cn/4  +  cn/4  + cn/4 + cn/4      = cn in O(n)
           /  \     /  \   /  \    /  \
                    ...
                    
* The depth of the recursion is O(log n)

* The time complexity of Merge Sort is O(log n) * O(n) = O(n log n)


## In-class exercise

The Closest Pair algorithm takes an array of length n of 2-dimensional
points and finds the pair points that are closest to each.

* Divide: Sort the array according to the x-coordinates of the
  points. Split the array of points in half with a vertical line.

* Conquer: recursively apply this algorithm to the left and right
  half. This yields a closest pair of points for the left half and
  likewise for the right half. Let δ be the distance between
  the closer of the two pairs.
  
* Combine: Check whether there is a pair of points that spans the
  vertical line whose distance is less than δ. Is so, that's the
  answer. Otherwise, the answer is the closer of the pairs from the
  left and right halves.

The divide step is O(n log n), e.g., using Merge Sort.

The combine step, surprisingly, is only O(n log n). It also involves sorting.

What is the big-O time complexity of Closest Pair?

