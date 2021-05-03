# Growth of Functions

Analyzing the exact time an algorithm takes to run is brittle and
messy. It is brittle because the time can change every year due to
changes in computers. It is messy, mathematically, because the time
depends on every branch of the code and often depends on the data.

We solve both problems by developing a method that 

1. suppresses constant factors, and
2. compares the functions as the input grows towards infinity.


## Big O

**Definition** Suppose g is a function. O(g) is the set of all
functions that grow at the same rate or slower than g.
To be precise, for some function f, we have
f ∈ O(g)
iff
there exists numbers n0 and c such that
for all n >= n0, 0 <= f(n) <= c g(n).

We say that g is an *asympototic upper bound* for f.
(Or just "upper bound" for short.)

first_three_odds =  {1,3,5}
odds = {1,3,5,7,9,...}

O(n²) = {n², 2n², 0.5 n², n, 2n, 1, 2n² + 3n + 5, ... }

n^3 ∈ O(n²)? No, that's false.

### Example: 2n² + 3n + 5 ∈ O(n²)

We need to find the constants n0 and c.

We need 
2n² + 3n + 5 <= c n²
choose c = 4 because 4 is larger than 2, the coefficient on n².
2n² + 3n + 5 <= 4n²
but that's not true when n=0!
5 <= 0
Ok, so we need to choose a larger n0.

n | 2n² + 3n + 5 | 4n²
--|--------------|----
0 | 5            | 0
1 | 10           | 4
2 | 19           | 16
3 | 32           | 36
4 | 49           | 64

Choose n0 = 3.
for all n >= 3, 2n² + 3n + 5 <= 4n².      ✓

solving the inequality:
(2n² + 3n + 5)/n² <= c n² / n²
2 + 3/n + 5/n² <= c            when n grows large
2 < c


Example of f not in O(n^2)?

n^3 not in O(n^2)

n^3 <= c n^2

n^3 / n^2 <= c n^2 / n^2
n <= c   false!




### f ≾ g notation for Big-O

**Definition** We write f ≾ g to mean f ∈ O(g).

This relation is a preorder:
* reflexive: f ≾ f for any f.
* transitive: If f ≾ g and g ≾ h, then f ≾ h, for any f, g, h.

This relation is not anti-symmetric. 
(Anti-symmetry: If a <= b and b <= a, then a = b.)
For example, above we showed that
2n² + 3n + 5 ≾ n²
It's also true that
n² ≾ 2n² + 3n + 5.
but of course, 2n² + 3n + 5 ≠ n².



## Big-Omega

**Definition** Suppose g is a function. Ω(g) is the set of all
functions that grow at the same rate or greater than g.
To be precise, for some function f, we have
f ∈ Ω(g)
iff
there exists numbers n0 and c such that
for all n >= n0, 0 <= c g(n) <= f(n).

We say that g is an *asympototic lower bound* for f.
(Or just "lower bound" for short.)

**Definition** We write f ≳ g to mean f ∈ Ω(g).

This relation is also a preorder.

Big-O and Big-Omega are opposites (transpose symmetry):

    f ≾ g iff g ≳ f

## Big Theta

**Definition** Suppose g is a function. Θ(g) is the set of all
functions that grow at the same rate as g.
So, for some function f, we have
f ∈ Θ(g)
iff
there exists numbers n0, c1, c2 such that
for all n >= n0, 0 <= c1 g(n) <= f(n) <= c2 g(n).

So f is sandwiched between g multiplied by a small constant and g
multiplied by a big constant.

We say that g is an *asympototic tight bound* for f.
(Or just "tight bound" for short.)

**Definition** We write f ≍ g to mean f ∈ Θ(g).

This relation is an equivalence relation.
* reflexive: f ≍ f for any f.
* symmetric: If f ≍ g, then g ≍ f for any f and g.
* transitive: If f ≍ g and g ≍ h, then f ≍ h, for any f, g, h.

Big-Theta is related to big-O and big-Omega as you would expect

    f ≍ g iff f ≾ g and g ≾ f.

For each equivalence class of this relation, we pick out the simplest
function to be the representative. Here are some of them:

* log n
   other functions in Θ(log n) are 2 log n, log n + 5, ...
* √n
* n
* n log n
* n²
* 2ⁿ



## Aside: combining functions, combining big-O

**Definition** Pointwise addition:

    (f + g)(x) = f(x) + g(x)
  
**Definition** Pointwise multiplication:

    (f × g)(x) = f(x) × g(x)

**Definition** Addition of big-O:

    O(f) + O(g) = { h | h ≾ f + g }

**Definition** Multiplication of big-O:

    O(f) × O(g) = { h | h ≾ f × g }


## Properties of Big-O

Ignore constants: c n² ∈ O(n²)

Ignore terms of polynomial with smaller exponents: n² + 2n ∈ O(n²)

For pointwise-addition, take the larger, for example:
  If f ∈ O(n), g ∈ O(n²), then f + g ∈ O(n²).
In other words, if f ≾ g then
* O(f) + O(g) = O(g)

Example: O(log n) + O(n) = O(n).

For pointwise-multiplication, multiply, for example:
  If f ∈ O(n), g ∈ O(n²), then f × g ∈ O(n³).
In other words
* O(f) × O(g) = O(f × g)

## Big-O of `fib_array`

    static int fib_array(int n) {
        int F[] = new int[n + 2];      O(n)
        F[0] = 0;                      O(1)
        F[1] = 1;                      O(1)
        int i = 2;                     O(1)
        while (i <= n) {               O(1) × O(n) = O(n)
            F[i] = F[i-1] + F[i-2];    O(1) × O(n) = O(n)
            i = i + 1;                 O(1) × O(n) = O(n)
        }
        return F[n];                   O(1)
    }

  (O(n) + O(1)) + ... + O(n) + O(n) + O(n) + O(1)
= O(n) + ... + O(n) + O(n) + O(n) + O(1)
....
= O(n)

Answer: O(n)

(It would be O(n²) if we handled big integers because then integer
addition would be O(n).)


## Example: what is the time complexity of the minimum function?

    static public int minimum(int A[]) {
        int min = A[0];           // O(1)
        int i = 1;                // O(1)
        while (i != A.length) {   // O(1) * O(n) = O(n)
            if (A[i] < min) {     // O(1) * O(n) = O(n)
                min = A[i];       // O(1) * O(n) = O(n)
            }
            i = i + 1;            // O(1) * O(n) = O(n)
        }
        return min;               // O(1)
    }

    O(1) + O(1) + O(n) + O(n) + O(n) + O(n) + O(1) = O(n)

Answer: O(n)
