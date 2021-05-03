# Amortized Analysis

Instead of focusing on the cost of a single operation, we'll compute
the cost of a sequence of n operations: T(n).

**Definition.** The *amortized cost* of each operation is T(n)/n.

## Aggregate Analysis

The **aggregate analysis** method directly computes the total time
T(n).

### Example: stacks with multi_pop

Suppose S is a stack of size m.

The `multi_pop(k)` operation pops k elements off the stack.

    def multi_pop(self, k):
	    while not self.empty() and k > 0:
		  self.pop()
		  k = k - 1

| Operation        | Time Complexity
| ---------------- | -----------------
| S.push(x)        | O(1)
| S.pop()          | O(1)
| S.multi_pop(k)   | O(min(m,k)) = O(m)

Suppose we have a sequence of n push, pop, and multi_pop operations.

#### Straightforward big-O analysis of stacks with multi_pop

The maximum stack size during the sequence is n.
So the cost for a multi_pop() is worst-case O(n), and the worst-case
for any of the three operations is O(n). 
There are n operations, so the total cost is O(n²) and the amortized
cost of each operation is O(n²/n) = O(n). 
While this is a valid upper bound, it is not tight.

#### Aggregate analysis of stack with multi_pop

Let's do a more refined analysis by computing an exact total cost
instead of using big-O for each operation.

We can only pop each object once for each time we push it on the stack
(including pops that occur inside a multi_pop). Within a sequence of n
operations, the number of pushes is bounded above by n so the number
of pops is also bounded above by n.

So the total cost is c₁ n + c₂ n = O(n). Thus, the amortized cost of
each operation is O(n/n) = O(1).

### Example: incrementing a binary counter for k-bit integers

We represent a binary number with an array A where A[0] is the
least-significant bit and A[k-1] is the most-significant bit.

To increment, if the least-significant bit is 0, set it to 1.
Otherwise, we replace the leading string of 1's with 0's and put a
1 in the next digit.

| binary number | i  | cost | total cost
| ------------- | -- | ---- | ----------
|   0 0 0 0 0 0 | 0  | 0    | 0
|   0 0 0 0 0 1 | 1  | 1    | 1
|   0 0 0 0 1 0 | 2  | 2    | 3
|   0 0 0 0 1 1 | 3  | 1    | 4
|   0 0 0 1 0 0 | 4  | 3    | 7
|   0 0 0 1 0 1 | 5  | 1    | 8
|   0 0 0 1 1 0 | 6  | 2    | 10
|   0 0 0 1 1 1 | 7  | 1    | 11
|   0 0 1 0 0 0 | 8  | 4    | 15

Implementation:

	def increment(A):
	   j = 0
	   while j != len(A) and A[j] == 1:
		  A[j] = 0
		  j += 1
	   if j < len(A):
		  A[j] = 1

#### Straightforward big-O analysis of incrementing a binary counter

The worst-case for increment is O(k), so for a sequence of n
increments, we have O(n k). But again, that's not tight.

#### Aggregate analysis of incrementing a binary counter

In a sequence of n increments, 

* bit 0 is flipped once every time: n flips total
* bit 1 is flipped once every 2 times: n/2 flips total
* bit 2 is flipped once every 4 times: n/4 flips total
* ...
* bit j is flipped once every n/(2^j) times
    
Total number of flips is

Sum from j=0 to k-1, n/(2^j)  
  < n * Sum from j=0 to ∞, 1/(2^j)  
  = n * Sum from j=0 to ∞, (1/2)^j  
  = n * 1 / (1 - 1/2)         (by equation A.6)  
  = n * 2  

(A.6) Sum from k=0 to ∞, x^k = 1 / (1 - x)

Thus, the total worst-case cost of n operations is O(n).  So the
amortized cost of an increment is O(1).

## The Accounting Method

We assign a "charge" to each operation. 

When the charge is more than the actual cost, the difference is
captured in "credit" that is associated with an object in the data
structure.

Credit can be used to pay for later operations.

We would like the sum of all the charges to be an upper bound
on the sum of all the actual costs. 

We can ensure this by requiring that the total credit in the data
structure never goes below zero.

### Accounting method applied to stacks with multi_pop

Let's charge
* 2 for push
* 0 for pop
* 0 for multi_pop

When we perform a push, 1 charge pays for the push itself and 1 charge
becomes credit attached to the pushed element.

Thus, 1 credit is attached to every element in the stack.

When we perform a pop (directly or inside a multi_pop), we pay for it
with the credit attached to the element and remove the element from
the stack.

The amount of credit in the stack is equal to the number of elements.
The number of elements is never negative, so neither is the total credit.

The total charge for n operations is 2n ∈ O(n), so the
amortized cost of each operation is O(1).

### Accounting method applied to incrementing a binary counter

Let's charge
* 2 for setting a bit to 1
* 0 for setting a bit to 0

In the increment algorithm, when we set a bit to 1 we use
1 charge for that and place 1 credit on the bit. This
happens at most once per call to increment.

When we set a bit to 0, we use the credit that was stored in that bit.

The total charge for n operations is 2n ∈ O(n), so the
amortized cost of each operation is O(1).

## The Potential Method

The idea is similar to the accounting method, except that credit is
associated with the data structure as a whole instead of individual
elements.

We define a function P(D) that returns the potential energy (credit)
of a data structure.

The amortized cost of an operation aᵢ is the actual cost cᵢ plus the
difference in potential energy.

aᵢ = cᵢ + P(Dᵢ) - P(Dᵢ₋₁)

We want the sum of the amortized costs to bound the sum of the actual
costs, so we require that the potential energy be non-negative.


### Potential method applied to stacks with multi_pop

Define the potential to be the size of the stack

P(S) = S.length

The size of the stack is never negative.

The amortized cost `push` on a stack with m elements is 

    1 + (1 + m) - m = 2

The amortized cost of `pop` on a stack with m elements is 

    1 + (m - 1) - m = 0

The amortized cost of `multi_pop(k)` on a stack with m elements  is

    k + (m - k) - m = 0

So the amortized cost of each operation is O(1).

### In-class exercise: potential method applied to binary counters

Solution:

P(B) = count the bits set to 1

for example, the counter in steps 7 and 8:
* P(0 0 0 1 1 1) = 3
* P(0 0 1 0 0 0) = 1

amortized cost  =  actual cost  +  P(after)  -  P(before)

going from step 7 to 8, amortized cost = 4 + 1 - 3 = 2

In general, 
* let k be the 1's in the "before"
* let z be number of times a bit is set to zero during increment

amortized cost =  z + 1 + (k + 1 - z) - k =  2
