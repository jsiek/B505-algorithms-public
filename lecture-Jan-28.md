# Review of Big-O

**Definition** Suppose g is a function. O(g) is the set of all
functions that grow at the same rate or slower than g, as expressed by
the following:

    f ∈ O(g)
    iff
    there exists numbers n₀ and c such that
    for all n ≥ n₀, 0 ≤ f(n) ≤ c g(n).

Show the [plot](./On2.png) for O(n²).

Show the [plot](./Thetan2.png) for Θ(n²).

These plots were generated using matplotlib, a python library.

Another good option for creating plots is the following web site:

    https://www.desmos.com/calculator


## in-class exercise

Show that

     10 n log n + 5n + 100 ∈ O(n²)

It may be helpful to note that computing the log (base 2) 
is easy when n is a power of 2:

n  | log n
---|------
2  | 1
4  | 2
8  | 3
16 | 4
32 | 5
64 | 6
128| 7
256| 8

Answer: [plot](./On210logn5n100.png)


## Further discussion

Is n^2 in O(n log n)?  no

    for all n >= n0. n^2 <= 10000 (n log n)    false!

Is 10 n log n + 5n + 100 in O(n log n)?   Yes

    c = 11
    10 n log n + 5n + 100 <= 11 n log n     for large enough n
    
Is 10 n log n + 5n + 100 in Θ(n log n)?   Yes


# Preview of Insertion Sort for Lab

Reference: page 18 of the textbook CLRS.

Insertion Sort is commonly used to sort the cards in your hand when
playing a card game.

Suppose you've been dealt a small pile of cards.

1. pick up one card and put in the other hand.

2. pick up another card and compare right-to-left until you find the
   appropriate spot.

3. continue this process until you've picked up all of the cards

To apply this idea to an array, conceptually divide the array into two
parts, what's in your hand (on the left) and the what you've been
dealt (on the right). We'll place a vertical bar between the two
sides. We start with one element on the left side and the rest on the
right side.

Demonstration:

       [2, 8, 7, 1, 3, 5, 6, 4]
         |
    => [2, 8, 7, 1, 3, 5, 6, 4]
            |
    => [2, 7, 8, 1, 3, 5, 6, 4]
               |
    => [1, 2, 7, 8, 3, 5, 6, 4]
                  |
    => [1, 2, 3, 7, 8, 5, 6, 4]
                     |
    => [1, 2, 3, 5, 7, 8, 6, 4]
                        |
    => [1, 2, 3, 5, 6, 7, 8, 4]
                           |
    => [1, 2, 3, 4, 5, 6, 7, 8]
                              |

Is insertion sort in Theta(n^2)?

Is n(n-1) / 2  in Theta(n^2)?  yes

Is 0.5 n^2 - n/2  in Theta(n^2)? yes

Choose c1 = 0.1, c2 = 1.


# Review of polynomials, exponentials, logarithms

## Polynomials

Example:

    p(x) = 2 x² + 5 x - 3   is in Theta(n^2)

is a *polynomial* in x of degree 3. The numbers 2, 5, and 3 are the
*coefficients*.

In general, a polynomial in `x` of degree `n` has the form

    n-1
    ⎲
    ⎳ aᵢ xⁱ
    i=0

A polynomial is asymptotically positive if the coefficient aⁿ > 0.

For any asymptotically positive polynomial p in n of degree i,

    p(n) ∈ Θ(n^(i-1))

Example:

    2 n² + 5 n - 3 ∈ Θ(n²)

The *coefficient representation* of a polynomial is the sequence of
its coefficients:

    a₀, a₁, ...


## Exponentials

Recall the following equations:

    a⁰ = 1
    a¹ = a
    a⁻¹ = 1/a
    (aⁱ)ⁿ = aⁱⁿ
    aⁱaⁿ = aⁱ⁺ⁿ

## Logarithms

Logarithm is the inverse of exponentiation.
The base `a` raised to which exponent is equal to `x`?

    logₐx = n  if  aⁿ = x         (x > 0, a > 0, a ≠ 1)

Recall the following equations:

    a^(logₐx) = x
    logₐa = 1                    (because a¹ = a)
    logₓ(ab) = logₓa + logₓb
    logₓ(aⁿ) = n logₓa
    logₓ y = logₐ y / logₐ x      (change of base formula)
    logₓa = 1 / logₐx
    a^(logₓ c) = c^(logₓ a)

Proof of next to last formula, using the change of base formula:

    logₓa = logₐa / logₐx = 1 / logₐx

Proof of the last formula. Start with the change of base formula:

    logₐ c = logₓ c / logₓ a
    (logₓ a)(logₐ c) = logₓ c
    logₐ(c^(logₓ a)) = logₓ c
    logₐ(c^(logₓ a)) = logₐ a^(logₓ c)
    c^(logₓ a) = a^(logₓ c)
    a^(logₓ c) = c^(logₓ a)
    
# in-class exercise

What is the big-O time complexity of the following sorting algorithm?

    public static void sort(int A[]) {
        for (int i = 0; i != A.length; ++i) {               // O(n) times
            for (int j = A.length - 1; j != i; --j) {       // O(n * n)
                if (A[j] < A[j-1])
                    swap(A, j, j-1);  // O(1)
            }
        }
    }

answer: [plot](./bubblesort.png)
