# Fibonacci numbers

The Fibonacci numbers just start a bit lower.

    F(0) = 0
    F(1) = 1
    F(n) = F(n-1) + F(n-2)       if n > 1

0,1,1,2,3,5,8,13,21,34,55,89,...

They grow rapidly!

    2^(n/2) <= F(n)     for n >= 6.


## Naive Algorithm

Directly inspired by the definiton.

    static int fib(int n) {
        assert(n >= 0);
        if (n < 2) {
          return n;
        } else {
          return fib(n - 2) + fib(n - 1);
        }
    }

(Caveat: `int` is a 64 bit integer, so it can only handle inputs up to
n=46. If you needed to handle larger inputs, you'd need to use a
big-integer library.)

analysis of time:

    T_fib(n) = { 2                              if n < 2
               { 2 + T_fib(n-1) + T_fib(n-2)    if n >= 2

That's like the Fibonacci numbers themselves. It grows very fast!

But there is a lot of repetition and wasted work.


                     ________F(5)_______
                    /                   \
               ___F(4)____              F(3)
              /           \            /   \
            F(3)          F(2)        F(2)  F(1)
          /      \       /   \       /  \
        F(2)     F(1)  F(0)   F(1)  F(0) F(1)
       /   \
    F(0)   F(1)


## A More Efficient Algorithm

Compute the sequence up to the one you want, storing it in an array.

    static int fib_array(int n) {
        int F[] = new int[n + 2];
        F[0] = 0;
        F[1] = 1;
        int i = 2;
        while (i <= n) {
            F[i] = F[i-1] + F[i-2];
            i = i + 1;
        }
        return F[n];
    }

Time:

    static int fib_array(int n) {        // Time:
        int F[] = new int[n + 2];        // n + 2
        F[0] = 0;                        // 1
        F[1] = 1;                        // 1
        int i = 2;                       // 1
        while (i <= n) {                 // 1*n
            F[i] = F[i-1] + F[i-2];      // 1*n
            i = i + 1;                   // 1*n
        }
        return F[n];                     // 1
    }

    T(n) = 4n + 6

How much space is used?

    S(n) = n + 2

Can you do better? That is the topic of this week's lab.
