# Master Method for Solving Recurrences

We've seen a number of recurrences and their big-O solutions via
recursion trees:

Binary Search: T(n) = 1 T(n/2) + O(1),  T(n) ∈ O(log n)

Merge Sort:    T(n) = 2 T(n/2) + O(n),  T(n) ∈ O(n log n)

Karatsuba's:   T(n) = 3 T(n/2) + O(n),  T(n) ∈ O(n^log 3)



**Theorem** (Master Method, simplified a bit)

(This theorem applies to any recursive algorithm that has "a"
recursive calls and evenly divides the input size by "b" (roughly
speaking) in each recursive call and does O(nⁱ) work per call.)

Suppose

    T(n) = a T(n/b) + O(nⁱ)
    
and a > 0, b > 1, i >= 0.
There are three cases:

1. Root wins: If i > log_b(a), then T(n) ∈ O(nⁱ).

2. Equal levels: If i = log_b(a), then T(n) ∈ O(nⁱ log n).

3. Leaves win: If i < log_b(a), then T(n) ∈ O(n^(log_b(a))).

Proof sketch.

Consider the recursion tree:

level                             #              time 
  0                nⁱ ______      1              nⁱ
                  /  \      \
  1         (n/b)ⁱ    (n/b)ⁱ ...  a              a (n/b)ⁱ = nⁱ (a/bⁱ)
             /  \     /   \
  2                               a^2
 ...         ...        ...
            |  |   |   |  |
log_b n     1  1   1   1  1       a^(log_b n)    n^(log_b a)

Why log_b n levels?

     b^d = n
     d = log_b n

**Root wins**
If a/bⁱ < 1 (equiv. i > log_b(a)), then the second level takes less
time than the first, and so on.  As we are adding the big-O's for each
level, we can just take the most significant term, which in this case
is the term for level 0.

     O(nⁱ) + O(nⁱ (a/bⁱ)) + ...
     = O(nⁱ)

**Equal levels**
If a/bⁱ = 1 (equiv. i = log_b(a)), then each level takes the same
amount of time. The total time is the number of levels times the cost
per level, which is nⁱ.

    O(nⁱ log n)

**Leaves win**
If a/bⁱ > 1 (equiv. i < log_b(a)), then the second level takes more
time than the first, and so on. In this case the most significant term
is the one for the last level.

    O(nⁱ) + O(nⁱ (a/bⁱ)) + ... + O(n^(log_b a))
    = O(n^(log_b a))

## Applications of the Master Method

### Hypothetical 3-way variation on Binary Search

Compare the key to two elements, X and Y, at positions 1/3 and 2/3.
Recursively search in one of the regions [0,n/3), [(n/3)+1,n/2), or [(n/2)+1,n)
depending on the result of the comparisons.

    |-------------X-------------Y------------|

    T(n) = 1 T(n/3) + O(1)

    a = 1, b = 3, i = 0, log_b(a) = 0
    equal levels:
    Master theorem: O(n⁰ * log n) = O(log n)

### Case 1 (i > log_b(a))

Closest Pair using a naive combine step that is O(n²):

    T(n) = 2 T(n/2) + O(n²)
    
    a = 2, b = 2, i = 2, log_b a = 1
    root wins: O(n²)

### Case 2 (i = log_b(a))

Binary Search: 

    T(n) = T(n/2) + O(1)

    a = 1, b = 2, i = 0, log_b a = 0
    equal levels:
    O(n⁰ log n) = O(log n)

Merge Sort: T(n) = 2 T(n/2) + O(n)

    a = 2, b = 2, i = 1, log_b a = 1
    equal levels:
    O(n¹ log n) = O(n log n)
    
### Case 3 (i < log_b(a))

Karatsuba's: T(n) = 3 T(n/2) + O(n)

    a = 3, b = 2, i = 1, log_b a = 1.58
    leaves win:
    O(n^(log_2 3)) = O(n^log 3)

Matrix Multiply by Naive Divide-and-Conquer : 

    A₁₁ A₁₂  *  B₁₁ B₁₂  = C₁₁ C₁₂
    A₂₁ A₂₂     B₂₁ B₂₂    C₂₁ C₂₂

    C₁₁ = A₁₁*B₁₁ + A₁₂*B₂₁
    C₁₂ = A₁₁*B₁₂ + A₁₂*B₂₂
    C₂₁ = A₂₁*B₁₁ + A₂₂*B₂₁
    C₂₂ = A₂₁*B₁₂ + A₂₂*B₂₂

    T(n) = 8 T(n/2) + O(n)

    a = 8, b = 2, i = 1, log_b a = 3
    leaves win:
    O(n^(log_2 8)) = O(n³)


Can we apply Master Method to Depth-First Search?

No, because the recursive calls do not sub-divide the graph evenly.
