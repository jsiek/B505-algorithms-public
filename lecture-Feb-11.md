# Quicksort (Feb. 11)

Another divide and conquer algorithm for sorting.

    6 4 8 2 9 3 9 4 7 1 6
    
* Divide: Partition the array around one of the elements, called the
   **pivot**.  For now we'll choose the last element, which is `6`. So
   the elements ≤ 6 are moved to the left and those > 6 are moved to
   the right.

        1 4 2 3 4 6 6 9 7 8 9
           ≤ 6     | |  > 6

   Note that the left and right sides are not yet sorted.

* Conquer: Recursively apply Quicksort to the left and right sides.

        1 2 3 4 4 6 6 7 8 9 9
                   | |

* Combine: Nothing to do here, we're done!


## Partitioning

Goal: rearrange the array and return an index `i` such that

* region [0,i) contains elements ≤ the pivot,
* A[i] contains the pivot, and
* region [i+1,n) contains elements > the pivot.

During the algorithm, we'll maintain three regions within the array:

* Less-than: the region [0,i) contains elements that are ≤ the pivot.
* Greater-than: the region [i+1,j) contains elements that are > the pivot.
* To-do: the region [j+1,n-1) has not yet been processed.

Initially, the Less-than and Greater-than regions are empty, with
    
    i = j = 0

      6  4  8  2  9  3  9  4  7  1  6
    ||                             |
    ij

We iteratively apply the following two steps:

1. If A[j] ≤ pivot, swap A[i] with A[j] then increment i.

2. Increment j.

Because 6 ≤ 6, we swap A[0] with A[0] and then increment i and j.

    i = 1
    j = 1

      6  4  8  2  9  3  9  4  7  1  6
       ||                          |
       ij

The swapping didn't do anything here because i = j. Later we'll see a
situation where the swapping is needed.

Next iteration, 4 ≤ 6, we swap A[1] with A[1] and increment i and j.

    i = 2
    j = 2
    
      6  4  8  2  9  3  9  4  7  1  6
          ||                       |
          ij

This time 8 > 6, so we increment j.
    
    i = 2
    j = 3
    
      6  4  8  2  9  3  9  4  7  1  6
          |   |                    |
          i   j

Next iteration, 2 ≤ 6, we swap A[2] and A[3] then increment i and j.
    
    i = 3
    j = 4
    
      6  4  2  8  9  3  9  4  7  1  6
             |   |                 |
             i   j

This time we can see the reason for swapping. We swapped 2 with 8 to
move 2 into the Less-than region.

Next A[j] = 9 > 6, so we increment j.

    i = 3
    j = 5
    
      6  4  2  8  9  3  9  4  7  1  6
             |     |               |
             i     j

Now A[j] = 3 ≤ 6, so we swap 3 and 8 and then increment i and j.

    i = 4
    j = 6
    
      6  4  2  3  9  8  9  4  7  1  6
                 |     |           |
                 i     j

Another 9 > 6. Increment j.

    i = 4
    j = 7
    
      6  4  2  3  9  8  9  4  7  1  6
                 |       |         |
                 i       j

Then 4 ≤ 6. We swap 4 and 9, then increment i and j.

    i = 5
    j = 8
    
      6  4  2  3  4  8  9  9  7  1  6
                   |        |      |
                   i        j

Next, 7 > 6. Increment j.

    i = 5
    j = 9
    
      6  4  2  3  4  8  9  9  7  1  6
                   |           |   |
                   i           j

Last step, 1 ≤ 6. Swap 1 and 8, then increment i and j.

    i = 6
    j = 10
    
      6  4  2  3  4  1  9  9  7  8  6
                       |          ||
                       i          j

The to-do region is empty, so the iteration stops.

The final step of Partition is to move the pivot into the middle, by
swapping the pivot, A[n-1], with the first element of the Greater-than
region, A[i].

      6  4  2  3  4  1  6  9  7  8  9
                       |
                       i

The Partition algorithm returns the location of the pivot, index i.

Time complexity of Partition: Θ(n)

## In-class exercise

Partition this array, using the last element as the pivot

    2 8 7 1 3 5 6 4

My result:

    ||2 8 7 1 3 5 6|4
    2||8 7 1 3 5 6|4
    2|8|7 1 3 5 6|4
    2|8 7|1 3 5 6|4
    2 1|7 8|3 5 6|4
    2 1 3|8 7|5 6|4
    2 1 3|8 7 5|6|4
    2 1 3|8 7 5 6|4
    2 1 3|4|7 5 6 8

## Run Time Analysis of Quicksort

### Worst-case scenario

The pivot is either the minimum or maximum value in the subarray every
time. Partitioning results in an empty left or right side, and the
non-empty side only decreases by 1! So we get the following
recurrence.

    T(n) = T(n-1) + n = T(n-2) + (n-1) + n = ... = n(n+1)/2 ∈ Θ(n²)

Not so quick!

### Best-case scenario

The pivot is the median element every time, so Partition results in
left and right sides that are essentially equal in size.

    T(n) = 2 T(n/2) + O(n)

    Master method:
    a = 2, b = 2, i = 1, log_b(a) = 1
    T(n) ∈ Θ(n log n)

### Summary

The run time of Quicksort depends on the balance of the partitions,
which depends on the data.

Not good! A hacker could choose worst-case input, such as an already
sorted array, to mount a denial-of-service attack.


## Randomized Quicksort

Select the pivot element randomly. Swap it with the last element of
the array and run Quicksort as described above.

How does this affect the run time analysis?

For the sake of argument, let's say that anything equal to or better
than a 1/10 and 9/10 split is a **balanced partition**. 

If we achieve balanced partitions, we get the following recurrence.

    T(n) = T(n/10) + T(9n/10) + n
    
    
    T1(n) = T(n/10) + n
    T2(n) = T(9n/10) + n
    T(n) =? T1(n) + T2(n)

    no

This is O(n log n), but let's check that using the **substitution
method**.

We assume T(k) ≤ c k log k for all k < n.
We need to show that T(n) ≤ c n log n.

      T(n) 
    = T(n/10) + T(9n/10) + n
    ≤ c(n/10) log(n/10)  +  c(9n/10) log(9n/10)  +  n
    = cn/10 (log n - log 10) + 9cn/10 (log(n) + log 9 - log 10) + n
    = cn/10 log n - cn/10 log 10 + 9cn/10 log n + 9cn/10 log 9 - 9cn/10 log 10 + n
    = c n log n + cn (- 1/10 log 10 + 9/10 log 9 - 9/10 log 10) + n
    < c n log n + cn (- 0.332 + 2.853 - 2.989) + n
    = c n log n + cn (- 4.68) + n
    < c n log n                      (e.g. choose c = 1)

So indeed, T ∈ O(n log n).

Next, let's analyze the probability of getting a balanced partition by
randomly selecting the pivot.

Consider elements in sorted order with the following regions 
labeled X and Y:

     X X Y Y Y Y Y Y Y Y Y Y Y Y Y Y Y Y X X

If we randomly choose a Y, its balanced, and if we choose an X it's
not balanced.

The chance of selecting a Y is 80%, an X is 20%.

So the chance of a balanced split is much higher than an unbalanced
split.

As we roll the dice during the algorithm, 1/5 of the partitions will
be unbalanced, but 4/5 of them will be balanced, which is enough to
that the **average** complexity is O(n log n). (It is possible to have
an O(n²) run of the algorithm, but it is highly unlikely.)
