# Sorting without Comparisons (Feb. 23)

## Counting Sort: Sorting Small Integers

    2 3 2 1 3 2 2 3 2 0 2 1
    
Let n be the length of the sequence and 
let k be one more than the maximum element of the array.


If n <= 1 then the sequence is already sorted
and we are done.

Otherwise we do the following.

1. Count the number of occurrences of each element. The
   following is the resulting `count` array of length k.

     0 | 1 | 2 | 3
    ---|---|---|---
     1 | 2 | 6 | 3

```java "count occurences"
for (int i = 0; i != k; ++i)
  count[i] = 0;
for (int i = 0; i != n; ++i)
  count[A[i]] += 1;
```

2. Determine starting position in the final result for each set of
   duplicate elements, filling in the `pos` array.

    0 | 1 | 2 | 3 |
    --|---|---|---|
    0 | 1 | 3 | 9 |

```java "compute positions"
pos[0] = 0;
for (int i = 1; i != k; ++i)
  pos[i] = pos[i-1] + count[i-1];
```

3. Copy the elements from the intput array into the output array
   according to their position.
   
   Recall the input array:

    2 3 2 1 3 2 2 3 2 0 2 1

    Working left-to-right across the input array, we
    start by putting 2 in position 3.

    0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11
    --|---|---|---|---|---|---|---|---|---|----|----
      |   |   | 2 |   |   |   |   |   |   |    |

    To make sure that the next occurence of 2 goes into
    index 4, we increment `pos[2]`
    
    0 | 1 | 2 | 3 |
    --|---|---|---|
    0 | 1 | 4 | 9 |

    Next we place the 3. It goes in position 9.

    0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11
    --|---|---|---|---|---|---|---|---|---|----|----
      |   |   | 2 |   |   |   |   |   | 3 |    |

   And we increment `pos[3]`.
   
    0 | 1 | 2 | 3 |
    --|---|---|---|
    0 | 1 | 4 | 10|

    Continuing in this way, we produce the output:

    0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11
    --|---|---|---|---|---|---|---|---|---|----|----
    0 | 1 | 1 | 2 | 2 | 2 | 2 | 2 | 2 | 3 |  3 | 3

```java "place elements in output"
for (int i = 0; i != n; ++i) {
  out[pos[A[i]]] = A[i];
  pos[A[i]] += 1;
}
```

Putting these three steps together, we obtain the following
implementation of Counting Sort.

```java "CountingSort"
static void CountingSort(int A[], int out[], int k) {
  int n = A.length;
  if (n == 1) {
    out[0] = A[0];
  } else if (n > 1) {
    int[] count = new int[k];
    int[] pos = new int[k];
	<<<count occurences>>>
    <<<compute positions>>>
	<<<place elements in output>>>
  }
}
```

Time complexity:

      O(k) + O(n) + O(n) + O(n)
    = O(k + n)

If `k` is O(n), then time complexity of Counting Sort is O(n).

Space complexity:

    O(k) + O(k) + O(n)
    = O(k + n)


Example with k > n:

     n = 3
	 k = 5001
     [ 1000, 400, 5000 ]



## Radix Sort

Radix sort also works on integers, and it sorts them by one digit at a
time, starting with the least significant digit.

It's important to use a sorting algorithm that is stable for the
sorting of each digit.

Example:
    
	       i
           V       V      V
         329      720     720    329
         457      355     329    355
         657      436     436    436
         839  ->  457 ->  839 -> 457
         436      657     355    657
         720      329     457    720
         355      839     657    839

    12319827312938712
	54319827312938712
	00019827312938712

    // extract_postfix(329, 1) = 9
    // extract_postfix(329, 2) = 29
    // extract_postfix(329, 3) = 329
    extract_postfix(int x, int num_digits) { ... }
	
    static void RadixSort(int[] A, int d) {
       int[] B = new int[A.length];
       for (int i = 0; i != d; ++i) {
	      assert sorted(map(extract_postfix(0,i), A));
          CountingSort(A, B, 10, extract_digit(i, d));
          // swap array A and B
          for (int j = 0; j != A.length; ++j) {
             int tmp = A[j];
             A[j] = B[j];
             B[j] = tmp;
          }
       }
    }

We adapt Counting Sort to extract the key from an element using a
client-provided function, in this case extracting digit `i` from a
number of at most `d` digits.

Time complexity of Radix Sort: O(d (n + b))
  where b is the base.

If b is a constant (like 10), and d is a constant, then 
the time complexity is O(n).

Radix sort is better than Counting Sort when the maximum
element is really big.



## Bucket Sort

Bucket Sort assumes that the input is drawn from a uniform
distribution. It then partitions the space into buckets and puts the
input elements into their buckets.

Let's fix the space of elements to be real number in the interal
[0,1). Then if we make the bucket array B the same size as A, we can
just multiply the element number by the length of A to get the bucket
number.

        static void bucket_sort(double[] A) {
           // Allocate the buckets 
           ArrayList<ArrayList<Double>> B = new ArrayList<>();
           for (int i = 0; i != A.length; ++i) {
              B.add(new ArrayList<Double>());
           }
           // Distribute the elements of A to the buckets
           for (int i = 0; i != A.length; ++i) {
              int bucket = (int)Math.floor(A[i] * A.length);
              B.get(bucket).add(A[i]);
           }
           // Sort each bucket
           for (int i = 0; i != B.size(); ++i) {
              B.get(i).sort((Double x, Double y) -> x < y ? -1 : (x > y) ? 1 : 0);
           }
           // Put the results back in A
           int k = 0;
           for (int i = 0; i != B.size(); ++i) {
              for (int j = 0; j != B.get(i).size(); ++j) {
                 A[k] = B.get(i).get(j);
                 ++k;
              }
           }
        }

Time complexity of bucket_sort:  (assuming evenly distributed elements)

    O(n) + O(n) + O(n) = O(n)

What if the elements are not evenly distributed?

    O(n) + O(n) + O(n log n) + O(n) = O(n log n)
