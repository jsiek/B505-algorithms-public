## Running `minimum`

Run the `MinTwo` test in the IntelliJ debugger.

Empirical approach to correctness and runtime analysis:

1. Run `minimum` on many different inputs that cause all
   branches to be taken.

2. Plot the execution time versus input size for many
   inputs of different size.


## Proof of correctness

The invariant of the `while` loop is that `min` is the smallest
element in the range `[0,i)`.  To be clear, by smallest element we
mean an element that is less-than-or-equal every element in the range.

We often work with half-open intervals like `[0,i)`. Recall that they
include the left endpoint but not the right endpoint.

To gain confidence in this invariant, we can write a method to check
the invariant and insert a call to it at the top of the loop.

    static public Boolean is_minimum(int min, int A[], int begin, int end) {
        for (int i = begin; i != end; ++i) {
            if (min > A[i]) return false;
        }
        return true;
    }

    static public int minimum(int A[]) throws Exception {
        ...
        while (i != A.length) {
            assert(is_minimum(min, A, 0, i));
            if (A[i] < min) {
        ...
    }

Recipe for proving that a loop satisfies a loop invariant.
1. Prove that the loop invariant is true just before the loop.
2. For some hypothetical iteration of the loop, 
   prove that the loop invariant is true at the bottom of the loop
   assuming that it started out true at the beginning of the loop.

Once you have shown that a loop satisfies an invariant, you can reason
about what comes after the loop, using the loop invariant and the
negation of the loop's condition.

Let's apply this to the `minimum` algorithm.

1. Just before the loop, `min` is the smallest element in `[0,1)`
  because it is the only element in that range.

2. Now consider a hypothetical iteration of the loop.

  We may assume that `min` is the smallest element in `[0,i)`
  at the start of the loop. We need to show that this remains
  true at the end of the loop.
  We consider two cases.
  a. `A[i] < min`
     So `min` is changed to `A[i]`. This new value of `min`
     is equal to `A[i]` and less-or-equal the elements in `[0,i)`,
     so `min` is the smallest element in `[0,i+1)`.
     At the end of the loop, `i` is changed to `i+1`, 
     at which point `min` is the smallest element in `[0,i)`.
  b. `min <= A[i]`
     So `min` is the smallest element in `[0,i+1)`.
     At the end of the loop, `i` is changed to `i+1`, 
     at which point `min` is the smallest element in `[0,i)`.

After the loop is finished, we know that `i == A.length` and that
`min` is the smallest element in `[0,i)`.  So `min` is the smallest
element in `A`.

## Runtime analysis

Let n be the length of the array.

As a proxy for actual times, we'll count how many non-trivial lines of
code get executed. There are 4 lines of code in the `while` loop and 3
lines of code outside the loop. The loop executes n - 1 times.

    T(n) = 4(n - 1) + 3 = 4n - 1

    is an upper bound on the time this function takes.
