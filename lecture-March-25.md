# Amortized Analysis of Dynamic Tables

By "tables" we mean anything that uses continuguous memory, such as
arrays, stacks, and hashtables.

Given a table T, let T.num be the number of elements stored in the table
and T.size be the number of available slots in the table.

The *load factor* α(T) = T.num / T.size.
(I think of this as "percent full".)

If T.num is about to become larger than T.size, we'd like to resize
the table by allocating a new array and copying the items over.

First let's analyze tables that can grow but not shrink.  Also, let's
use the heuristic that whenever we grow the table, we always double
its size.
    
What is the cast of one insertion?
* O(1) if there is room in the table (T.num + 1 <= T.size)
* O(n) if there is not room, because we copy the table into a new one

What is the cost of n table insertions?

## Straightforward big-O analysis of n table insertions

Worst case for 1 insertion is O(n).

For n insertions:

    O(n) * n = O(n^2)
  
Not tight.
  
## Aggregate analysis of n table insertions

Cost of ith insertion is

    c_i = { i   if i - 1 is a power of 2
          { 1   otherwise

So the total cost is the following.

There are at most n insertions that cost 1.
There are log n insertions in which the table doubled.

    Sum_{i=1}^n c_i <= n + Sum_{k=0}^{lg n} 2^k
                     = n + (2^{lg n + 1} - 1)/(2 - 1)     (by A.5)
                     = n + 2^{lg n + 1} - 1
                     = n + 2 * 2^{lg n} - 1
                     = n + 2 * n - 1
                     < 3n 

    (A.5) Sum_{k=0}^n x^k = (x^{n+1} - 1)/(x - 1)
	
Total time for n insertions is O(n).
Amortized cost of an insertion is O(1).

## Accounting method applied to n table insertions

Charge 3 units for table insertion.

Suppose the table size is m immediately after an expansion,
so there are m/2 elements.

Over the next m/2 inserts, we spend 1 unit to insert the element,
place 1 credit on the element, and place 1 credit on one of the m/2
original elements that does not yet have a credit.

After the m/2 inserts, all the elements in the table will have a
credit.

The next insert will trigger a copy, which is paid for by the credit
on each element.


# Dynamic Tables: Insertions and Deletions

What if we also allow delete operations?  We'd like to shrink the
table if the load factor gets too low.  But exactly when should we
contract?
	
* When the load factor drops below 1/2? No! Ping-pong.
* Let the load factor drop to 1/4 before contracting.
     
What is the cost of a sequence of n inserts and deletes?

## Potential method applied to n insertions and deletions

We define the potential of a table T as follows

    P(T) = { 2 T.num - T.size    if α(T) >= 1/2
	       { T.size/2 - T.num    if α(T) < 1/2

* When the load factor is 1/2, P(T) = 0.

* As the load factor grows from 1/2 towards 1, the potential increases.

* When the load factor reaches 1, T.num = T.size, so

        P(T) = 2 T.num - T.size = 2 T.num - T.num = T.num
  
  There is enough potential energy to copy all the elements.

* As the load factor decreases from 1/2 towards 1/4, the potential
  increases.

* When the load factor reaches 1/4, T.size = 4 T.num

        P(T) = T.size/2 - T.num =  2 T.num - T.num = T.num
		
  There is enough potential energy to copy all the elements.

Next we compute the amortized cost aᵢ of the ith insert operation.
We have several cases to consider.

* Case α(Tᵢ₋₁) >= 1/2 and α(Tᵢ) >= 1/2:

    * Subcase (double the table size and copy),
	
        Tᵢ₋₁.size = Tᵢ₋₁.num = Tᵢ.num - 1  
        Tᵢ.size = 2 (Tᵢ.num - 1)  
	
        aᵢ = 1 + Tᵢ₋₁.num + P(i) - P(i-1)  
           = Tᵢ.num + (2 Tᵢ.num - Tᵢ.size) - (2 Tᵢ₋₁.num - Tᵢ₋₁.size)  
		   = Tᵢ.num + (2 Tᵢ.num - 2 (Tᵢ.num - 1)) - (2 (Tᵢ.num - 1) - (Tᵢ.num - 1))  
		   = Tᵢ.num + 2 Tᵢ.num - 2 Tᵢ.num + 2 - 2 Tᵢ.num + 2 + Tᵢ.num - 1  
		   = 3  

    * Subcase (insert the element)

        Tᵢ₋₁.size = Tᵢ.size  

        aᵢ = 1 + P(i) - P(i-1)  
		    = 1 + (2 Tᵢ.num - Tᵢ.size) - (2 Tᵢ₋₁.num - Tᵢ₋₁.size)  
			= 1 + (2 Tᵢ.num - Tᵢ.size) - (2 (Tᵢ.num - 1) - Tᵢ.size)  
			= 1 + 2 Tᵢ.num - Tᵢ.size - 2 Tᵢ.num + 2 + Tᵢ.size  
			= 3  

* Case α(Tᵢ₋₁) < 1/2 and α(Tᵢ) < 1/2.

	aᵢ = 1 + P(i) - P(i-1)  
	   = 1 + (Tᵢ.size/2 - Tᵢ.num) - (Tᵢ₋₁.size/2 - Tᵢ₋₁.num)  
	   = 1 + (Tᵢ.size/2 - Tᵢ.num) - (Tᵢ.size/2 - (Tᵢ.num - 1))  
	   = 1 + Tᵢ.size/2 - Tᵢ.num - Tᵢ.size/2 + Tᵢ.num - 1  
	   = 0  
			
* Case α(Tᵢ₋₁) < 1/2 and α(Tᵢ) >= 1/2.

	aᵢ = 1 + P(i) - P(i-1)  
	  = 1 + (2 Tᵢ.num - Tᵢ.size) - (Tᵢ₋₁.size/2 - Tᵢ₋₁.num)  
	  = 1 + (2 (Tᵢ₋₁.num + 1) - Tᵢ₋₁.size) - (Tᵢ₋₁.size/2 - Tᵢ₋₁.num)  
	  = 1 + 2 Tᵢ₋₁.num + 2 - Tᵢ₋₁.size - Tᵢ₋₁.size/2 + Tᵢ₋₁.num  
	  = 3 + 3 Tᵢ₋₁.num - (3/2) Tᵢ₋₁.size  
	  < 3 + 3 (Tᵢ₋₁.size/2) - (3/2) Tᵢ₋₁.size  
	  = 3  

So the amortized cost of insert is 3.

Next we compute the amortized cost aᵢ of the ith delete operation.

* Case α(Tᵢ₋₁) < 1/2. (which implies α(Tᵢ) < 1/2)

    * Subcase (shrink the table in half)
	
		Tᵢ₋₁.num = Tᵢ₋₁.size/4  
		Tᵢ.size = Tᵢ₋₁.size/2 = 2 Tᵢ₋₁.num = 2 (Tᵢ.num + 1)  

		aᵢ = 1 + Tᵢ.num + P(i) - P(i-1)  
		  = 1 + Tᵢ.num + (Tᵢ.size/2 - Tᵢ.num) - (Tᵢ₋₁.size/2 - Tᵢ₋₁.num)  
		  = 1 + Tᵢ.num + (Tᵢ.num + 1) - Tᵢ.num - ((2 Tᵢ₋₁.num) - (Tᵢ.num + 1))  
		  = 1 + Tᵢ.num + Tᵢ.num + 1 - Tᵢ.num - 2 Tᵢ.num - 2 + Tᵢ.num + 1  
		  = 1  

    * Subcase (don't shrink, just remove the element)

		aᵢ = 1 + P(i) - P(i-1)  
		  = 1 + (Tᵢ.size/2 - Tᵢ.num) - (Tᵢ₋₁.size/2 - Tᵢ₋₁.num)  
		  = 1 + Tᵢ.size/2 - Tᵢ.num - (Tᵢ.size/2 - (Tᵢ.num + 1))  
		  = 1 + Tᵢ.size/2 - Tᵢ.num - Tᵢ.size/2 + Tᵢ.num + 1  
		  = 2  

* Case α(Tᵢ₋₁) >= 1/2. See textbook exercise 17.4-2.


# Binomial Heaps

Improves on binary heaps by providing an efficient merge operation
that combines two heaps in O(log n).

In contrast, merging of two binary heaps is O(n log n).

Further, using the accounting method, we can show that the amortized
time for insert is O(1).

## Binomial Trees

Recall the binomial coefficient, n choose k, which is the number of
ways to choose k elements from a set of size n.  For example, there
are three ways to choose 2 elements from a set of size 3. For {a,b,c}
we have {a,b}, {a,c}, {b,c}.  In general, we have:

  n choose k =  n! / k!(n - k)!
        
e.g.

  3 choose 2 =  3! / 2!(3 - 2)! = 6/2 = 3

Recall Pascal's Triangle:

                       0=k  (diagonals)
        n=0          1 / 1=k
                  ----/-- /
        n=1        1 / 1 / 2=k
                ----/---/-- /
        n=2      1 / 2 / 1 / 3=k
               ---/---/---/-- /
        n=3    1 / 3 / 3 / 1 / 4=k
             ---/---/---/---/--
        n=4  1 / 4 / 6 / 4 / 1 

Each cell is the sum of the two cells diagonally above it:

    n choose k = (n-1 choose k-1) + (n-1 choose k)
        
**Definition** A *binomial tree* B(n) is a tree whose root has n
children, where the first child is B(n-1), the second is B(n-2), ...,
on down to the last child, which is B(0).

        B(0)   B(1)   B(2)    B(3)
         o      o     o       o---\
                |     |\      | \  \
                B0    B1 B0   B2 B1 B0
        
Consider the number of nodes at each depth within a binomial tree.

        depth
        0    o 1   o 1   o   1   o_____   1
                   |     |\      |  \  \
        1          o 1   o o 2   o   o  o 3
                         |       |\  |
        2                o   1   o o o    3
                                 |
        3                        o        1
        
**Theorem** In a binomial tree B(n), there are n choose k nodes at depth k.

Each binomial tree B(n) can be formed by taking two trees of B(n-1)
and putting one of them as a child of the other's root.

Turning them on their side and aligning by depth:

        1     1 1       1 2 1
        +
          1     1 1       1 2 1
        =
        1 1   1 2 1     1 3 3 1

So we get the rows of Pascal's triangle.


**Theorem** The height of a binomial tree B(n) is n. 

**Theorem** A binomial tree B(n) has 2ⁿ nodes.

So height = log nodes. It's balanced!

## Student exercise: draw B(4). 

How many nodes does it have?  How many nodes at each depth?

Solution:

        depth
        0    o---\--\    1
             | \  \  \
        1    o   o  o  o 4
             |   |  |
        2    ooo oo o    6
             |   |
        3    ooo o       4
             |
        4    o           1


## Binomial Heaps and Forests 

**Definition** A *binomial heap* B(n) is a binomial tree that also has
the max-heap property.

        class BinomialHeap<K> {
            K key;
            int height;
            ListNode<BinomialHeap<K>> children;
            BiPredicate<K,K> lessEq;
            ...
        }

**Definition** A *binomial forest* is a list of binomial trees.
    
We can implement a priority queue with a forest of binomial heaps.
It's a forest to allow for numbers of nodes that are not powers
of 2. We'll store the forest in order of *increasing* height and we
will not allow two trees of the same height.  The forest is
represented as a cons-list (nested pairs ending in None).

        public class BinomialQueue<K> {
            List<BinomialHeap<K>> forest;
            BiPredicate<K,K> lessEq;
            ...
        }

The forest will contain at most log n trees, analogous to the number
of bits in a binary number.

Next we discuss three operations on binomial trees and forests.

* Link two trees
* Insert a tree into a forest
* Merge two forests

## Link Operation

This operation applies to two binomial trees of the same height. 

It makes one of the trees the first child of the other.  Pick the one
with the larger key to be on top to maintain the max heap property.

        B(k)  ∪  B(k) =  B(k+1)

        o       o      o______
        |\      |\     |   \  \
        o o  ∪  o o =  o    o  o
        |       |      |\   |
        o       o      o o  o
                       |
                       o

Time complexity: O(1)


To be continued...
