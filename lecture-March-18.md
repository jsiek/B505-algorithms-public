# Heap Data Structure (continued)

## `extract_max` method

Idea: first record the max element, which is at `data[0]`, then we
need to delete that element. But deleting the first element of an
array is expensive. So we move the last element of the heap to
`data[0]`, shrink the array, and re-establish the max heap
property with `max_heapify`.

Example:

                            __16__
                           /      \
                         14        10
                        /  \      /  \
                       /    \    /    \
                      8      7  9      3
                     / \    /
                    2   4  1

Move `1`  to the root:

                            __1__
                           /     \
                         14       10
                        /  \     /  \
                       /    \   /    \
                      8      7 9      3
                     / \
                    2   4

Apply `max_heapify` to `1`.

                            __14__
                           /      \
                         8        10
                        / \      /  \
                       /   \    /    \
                      4     7  9      3
                     / \
                    2   1

Implementation:

	E extract_max() {
		E max = data.get(0);                    // O(1)
		data.set(0, data.get(data.size()-1));   // O(1)
		data.remove(data.size()-1);             // O(1)
		max_heapify(0, data.size());            // O(log(n))
		return max;
	}

Time complexity analysis: O(log(n))


## `sortInPlace` method

Idea: 

1. Build a heap from an array elements.

2. Iteratively remove the max element and store it at the end of the
   array. This is accomplished by swapping the max element (at position 0)
   with the last element and then applying `max_heapify` to the root.

Conceptually, the array has two parts: the front part represents the
heap and the back part represents the sorted output array.

Example:

Heap:

    [16,14,10, 8, 7, 9, 3, 2, 4, 1]

                            __16__
                           /      \
                         14        10
                        /  \      /  \
                       /    \    /    \
                      8      7  9      3
                     / \    /
                    2   4  1
					
Swap max (root) `16` with the last element `1`:

    [1,14,10, 8, 7, 9, 3, 2, 4, 16]

Heapify the root (just like in the `extract_max` example):

    [14,8,10,4,7,9,3,2,1,   16]

                            __14__
                           /      \
                         8        10
                        / \      /  \
                       /   \    /    \
                      4     7  9      3
                     / \
                    2   1

another iteration, start with swap of `14` and `1`

    [1,8,10,4,7,9,3,2,   14,16]

                            __1__
                           /      \
                         8        10
                        / \      /  \
                       /   \    /    \
                      4     7  9      3
                     / 
                    2 
					
then max_heapify on the root `1` ...

                            __10_
                           /      \
                         8        9
                        / \      /  \
                       /   \    /    \
                      4     7  1      3
                     / 
                    2 

    [10,8,9,4,7,1,3,2,   14,16]

another iteration: swap the `10` with `2`

                            __9__
                           /      \
                         8        3
                        / \      /  \
                       /   \    /    \
                      4     7  1      2
                      
    [9,8,3,4,7,1,2,  10,14,16]


Keep repeating this process until the front heap-part is empty and the
whole array is dedicated to the back sorted-part.

Implementation:

	static <E> void sortInPlace(ArrayList<E> A, 
								BiPredicate<E,E> lessThanOrEqual)
	{
		Heap<E> H = new Heap<E>(lessThanOrEqual);
		H.data = A;
		H.build_max_heap();                                // O(n)
		for (int i  = H.data.size() - 1; i != 0; --i) {    // n iterations
			swap(H.data, 0, i);
			H.max_heapify(0, i);                           // O(log n)
		}
	}
    
Time complexity:
The for loop executes n times, and max_heapify is O(log(n)),
so we have O(n log(n)).

## `increase_key` method (used by insert)

The key of the object at position i has increased.
How should we move it to get a valid heap?
Example: Change the key of 9 to 20.

             ___16___
            /        \
          14          10
         /  \        /  \
        8    7     *9*    3
    
## **Student exercise**, come up with the algorithm for `increase_key`

Answer: the idea is to propagate the element up. For example,

             ___16___
            /        \
          14          10
         /  \        /  \
        8    7     20    3

becomes

             ___16___
            /        \
          14          20
         /  \        /  \
        8    7      10    3

becomes
    
             ___20___
            /        \
          14          16
         /  \        /  \
        8    7      10    3

Implementation:

    void increase_key(int i) {
        while (parent(i) >= 0 && lesseq.test(data.get(parent(i)), data.get(i))) {
            swap(data, i, parent(i));
            i = parent(i);
        }
    }
	
Time complexity: O(log(n))	

## `insert` method

Insert a new element into the heap.

Idea: put it end of the array (leaf in the tree), then apply increase_key.

	void insert(E k) {
		if (data.size() + 1 ≥ data.size()) {
			ArrayList<E> d = new ArrayList<>((data.size() + 1) * 2);
			for (E e : data)
				d.add(e);
			data = d;
		}
		data.add(k);
		increase_key(data.size() - 1);
	}

Time complexity: O(log(n))    (amortized time complexity, talk about next week)

# Priority Queues

- implement using a `Heap`
- the priorities are the keys
- `push`: implement with `insert`
- `pop`: implement with `extract_max`

In Java:

	class PriorityQueue<E> {
		private Heap<E> heap;
		public PriorityQueue(BiPredicate<E,E> lessThanOrEqual) {
			heap = new Heap<>(lessThanOrEqual);
		}
		public void push(E key) {
			heap.insert(key);
		}
		public E pop() {
			return heap.extract_max();
		}
	}
