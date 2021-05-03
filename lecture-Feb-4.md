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

Solution: recursion tree method

                n log n                     = n log n
               /       \
       (n/2)log(n/2)    (n/2)log(n/2)       = n log(n/2) = n(log n - log 2) 
        /       \        /        \                      = n log n - n
       /         \      /          \
    (n/4)log(n/4) ...                       = n log(n/4) = n log n - 2n


How many levels? (let h be the number of levels)
n
n/2
n/4
...
...
4
2
1

2^h = n

log(2^h) = log n
h = log n

Each level is in `O(n log n)`.

So we multiple `O(n log n)` per level times `log n` levels.

    O(n log² n)


## Prelude: Polynomial Addition

          3x² + 2x + 5       (polynomial A)
        + 5x² + 1x + 2       (polynomial B)
        ---------------
          8x² + 3x + 7

Let n be the degree of the polynomials.

Time complexity: O(n)


## Polynomial Multiplication

Reference: page 899 of the textbook CLRS.

                  3x² + 2x + 5       (polynomial A)
                × 5x² + 1x + 2       (polynomial B)
    ----------------------------
                   6x² + 4x + 10
            3x³ +  2x² + 5x
    15x⁴ + 10x³ + 25x²
    ----------------------------
    15x⁴ + 13x³ + 33x² + 9x + 10     (polynomial C = A * B)

### Naive Algorithm

    static void multiply_poly(double A[], double B[]) {
        int n = A.length;
        double C[] = new double[2n-2];                          // O(n)
        for (int i = 0; i != 2n-2; ++i)                         // O(n)
            C[i] = 0;
        for (int j = 0; j != n - 1; ++j) // iterate over B      // n times
            for (int i = 0; i != n - 1; ++i) // iterate over A  // n*n times
                C[i + j] = C[i + j] + A[i] * B[j];              // O(n^2)
        return C;                                               // O(1)
    }

What's the time complexity of the naive algorithm?

      O(n) + O(n) + O(n^2) + O(1) = O(n^2)


### Divide and conquer, Karatsuba's algorithm

Polynomial addition and subtraction is fast, whereas polynomial
multiplication is slow. Can we replace some of the multiplications
with additions and/or subtraction?

Example:

The straigtforward way to multiply the following polynomials takes 4
multiplications:

    A = a₁ x + a₀
    B = b₁ x + b₀
    C = a₁b₁ x² + (a₁b₀ + a₀b₁) x + a₀b₀

But we can rearrange the middle coefficient:

      a₁b₀ + a₀b₁ 
    = a₁b₀ + a₀b₁ + (a₁b₁ - a₁b₁) + (a₀b₀ - a₀b₀)
    = a₁b₁ + a₁b₀ + a₀b₁ + a₀b₀ - a₁b₁ - a₀b₀
    = a₁(b₁ + b₀) + a₀(b₁ + b₀) - a₁b₁ - a₀b₀
    = (a₁ + a₀)(b₁ + b₀) - a₁b₁ - a₀b₀

Uh oh, we've gone from 2 multiplies to 4!
But we already had to compute a₁b₁ and a₀b₀, so in
total we're replacing 4 multiplications with 3 multiplications.

How to apply this idea to higher-degree polynomials?
For example:

    A = 4x³ + 3x² + 2x + 1
    B = x³ + 2x² + 3x + 4

Split each polynomial into two parts:

    A = 4x³ + 3x² + 2x + 1
    A₁ = 4x + 3
    A₀ = 2x + 1
    A = A₁ x² + A₀

    B = x³ + 2x² + 3x + 4
    B₁ = x + 2
    B₀ = 3x + 4
    B = B₁ x² + B₀

Then multiply them using Karatsuba's idea.

      (A₁ x² + A₀) (B₁x² + B₀)
    = A₁B₁ x⁴ + (A₀B₁ + A₁B₀) x² + A₀B₀
    = A₁B₁ x⁴ + ((A₁ + A₀)(B₁ + B₀) - A₁B₁ - A₀B₀) x² + A₀B₀

In general, the algorithm for Polynomial Multiplication is:

1. Divide: split each polynomial A and B in half, factoring out
   x^(d/2) from the front half (where d is the degree),
   to obtain A₁, A₀, B₁, B₀.

2. Conquer: compute A₀B₀, A₁B₁, and (A₁ + A₀)(B₁ + B₀)
   recursively using Polynomial Multiplication.

3. Combine: return the polynomial

        A₁B₁ x^d + ((A₁ + A₀)(B₁ + B₀) - A₁B₁ - A₀B₀) x^(d/2) + A₀B₀

        static double[] multiply_poly(double A[], double B[]) {
            if (...) { // if small enough
               // naive multiplication
            } else {

                // Divide A into A1, A0. B into B1 B0
                A1 = ...
                A0 = ...
                B1 = ...
                B0 = ...

                A1A0 = add_poly(A1, A0); // O(n)
                B1B0 = add_poly(B1, AB);

                // Conquer (recursion)
                A0B0 = multiply_poly(A0, B0);
                A1B1 = multiply_poly(A1, B1);
                A1A0B1B0 = multiply_poly(A1A0, B1B0);

                // Combine
                return ...;
            }
        }


### Runtime Analysis for Karatsuba's Algorithm

Recurrence:

    T(n) = 3 T(n/2) + O(n)
            conquer   divide+combine

What's the time complexity? Let's visualize the recursion tree:

                              #            work per level
            __  n __          1            n
           /    |   \
          /     |    \
       n/2     n/2   n/2      3            (3/2) n
      / | \   / | \ / | \
                              9            (9/4) n
       ...     ...   ...
             n/(2^i)          3^i          (3/2)^i n
       ...     ...   ...
    O(1) O(1) ...             3^(log n)    (3/2)^(log n) n
                            = n^(log 3)
Total:

    n + (3/2)n + (9/4)n + ... + (3/2)^i n + ... + (3/2)^(log n) n
    = n (1 + 3/2 + 9/4 + ... + (3/2)^i + ... + (3/2)^(log n))

The `1 + 3/2 + 9/4 + ...` part is a geometric series.

From the Appendix of the textbook:

      n
      ⎲
      ⎳ xⁱ = xⁿ⁺¹ - 1       (A.5)
      i=0     ---------
                x - 1

How to remember this formula:

Take the series

    S = 1 + x + x² + x³ + ... + xⁿ

and substract itself times `x`

    Sx = x + x² + x³ + ... + xⁿ + xⁿ⁺¹

Most of the terms cancel out

    S - Sx = 1 - xⁿ⁺¹

Then solve for S

    S (1 - x) = 1 - xⁿ⁺¹
    
    S = 1 - xⁿ⁺¹
        --------
         1 - x

And multiply the top and bottom by `-1`.

    S = xⁿ⁺¹ - 1
        --------
         x - 1

For this problem, we have

    x = 3/2
    n = log n
    
      (3/2)^(log n + 1) - 1
      -----------------------
         (3/2) - 1

    = 2 (3/2)^(log n + 1) - 2
    = 2 (3/2)^(log n) (3/2)^1 - 2
    = 3 (3/2)^(log n) - 2
    = 3 n^(log 3/2) - 2
    = 3 n^(log 3 - log 2) - 2
    = 3 n^(log 3 - 1) - 2
    = 3 n^(log 3)n^(-1) - 2

Multiply by `n` we get an upper bound of

      3 n n^(log 3)n^(-1) - 2n
    = 3 n^(log 3) - 2n

So the big-O time complexity is

    O(n^(log 3)) ≈ O(n^1.58)        (compare to O(n^2) for naive algorithm)
