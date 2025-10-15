

# Analysis

![image-20241109214701457](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241109214701457.png)

> ![image-20241109215021753](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241109215021753.png)



![image-20241109214848592](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241109214848592.png)

![image-20241110191936940](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241110191936940.png)

> ps:![image-20241110193138168](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241110193138168.png)

# Hash Table

The process of mapping an object or a number onto an integer in a given range is called **hashing**

1. hash function

Necessarily, a hash function *h* should be:

- The hash value must be *deterministic*

- It must always return the same 32-bit integer each time

- Equal objects hash to equal values

![image-20241103123644714](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241103123644714.png)

2. Map to an index 0, ..., *M* – 1

   - modulus%M
   - multiplicative

   The multiplicative method

   ```cpp
   unsigned int const C = 581869333;  // some number
   
   unsigned int hash_M( unsigned int n, unsigned int m ) {
   	unsigned int shift = (32 – m)/2;
   	return ((C*n) >> shift) & ((1 << m) – 1);
   		}
   
   ```

   

>  The bitwise operators: & << >>

![image-20241103124304093](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241103124304093.png)

![image-20241103124252339](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241103124252339.png)

3. hash collision

   - chained hash table![image-20241103170235809](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241103170235809.png)

     > It requires extra memory
     >
     > It uses dynamic memory allocation 

   - load factor![image-20241103170303195](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241103170303195.png)

     > 
     >
     > If the load factor becomes too large, access times will start to increase: **O**(\lambda)
     >
     >  The most obvious solution is to double the size of the hash table and re-insert every object (*rehashing*)

   - open addressing

     - Linear Probing

       - Erase: Lazy Erasing 

         –Mark the bin as ERASED

         –Searching: regard it as occupied

         –Insertion: regard it as unoccupied

       - Primary Clustering：As the cluster length increases, the probability of further increasing the length increases

         –Choose *M* large enough so that we will not pass this load factor

         •This could waste memory

         –Double the number of bins if the chosen load factor is reached

         –Choose a different strategy than linear probing

         •Two possibilities are quadratic probing and double hashing

     - Quadratic probing

       ```cpp
       		int initial = hash_M( x.hash(), M );
       
       		for ( int k = 0; k < M; ++k ) {
       		    bin = (initial + k*k) % M;
       		}
       		
       
       int initial = hash_M( x.hash(), M );
       
       		for ( int k = 0; k < M; ++k ) {
       		    bin = (initial + c1*k + c2*k*k) % M;
       		}
       
       ```



# Sort

## Insertion sort

each time we perform a swap, we remove an inversion

Thus, the body is run only as often as there are inversions

```cpp
template <typename Type>
void insertion_sort( Type *const array, int const n ) {
	for ( int k = 1; k < n; ++k ) {
		for ( int j = k; j > 0; --j ) {
			if ( array[j - 1] > array[j] ) {
				std::swap( array[j - 1], array[j] );
			} else {
				// As soon as we don't need to swap, the (k + 1)st
   				// is in the correct location
   				break;
			}
		}
	}
}

```

> –the outer for-loop will be executed a total of *n* – 1 times
>
> –In the worst case, the inner for-loop is executed *k* times
>
> –Thus, the worst-case run time is O(*n*^2)

- benefit:

​	–The algorithm is easy to implement

​	–Even in the worst case, the algorithm is fast for small problems

​	- stable

- improvement

  Swapping is expensive, so we could just temporarily assign the new entry

  –this reduces assignments by a factor of 3

  ```cpp
  template <typename Type>
  void insertion( Type *const array, int const n ) {
      for ( int k = 1; k < n; ++k ) {
          Type tmp = array[k];
  
          for ( int j = k; j > 0; --j ) {
              if ( array[j - 1] > tmp ) {
                  array[j] = array[j - 1];
              } else {
                   array[j] = tmp;
                   goto finished;
              }
          }
  
          array[0] = tmp;  // only executed if tmp < array[0]
  
          finished;  // empty statement
      }
  }
  
  
  ```

Best：Theta（n）

Average：Theta（n+k）where k is the adjecent swaps（inversions）

Worst（n^2）

## Bubble Sort

- Procedure:

Starting with the first item, assume that it is the largest

- benefit：

​	- stable

Compare it with the second item:

–If the first is larger, swap the two,

–Otherwise, assume that the second item is the largest



Continue up the array, either swapping or redefining the largest item

After one pass, the largest item must be the last in the list

Start at the front again:

–the second pass will bring the second largest element into the second last position



Repeat *n* – 1 times, after which, all entries will be in place

```cpp
		template <typename Type>
		void bubble( Type *const array, int const n ) {
			for ( int i = n - 1; i > 0; --i ) {
				for ( int j = 0; j < i; ++j ) {
					if ( array[j] > array[j + 1] ) {
						std::swap( array[j], array[j + 1] );
					}
				}
			}
		}

```

- Improvement

  - avoid so many swaps

  - flag

    ```cpp
    template <typename Type>
    	void bubble( Type *const array, int const n ) {
    		for ( int i = n - 1; i > 0; --i ) {
    			Type max = array[0];				// assume a[0] is the max
                bool sorted = true;
    
    			for ( int j = 1; j <= i; ++j ) {
    				if ( array[j] < max ) {
    					array[j - 1] = array[j];   // move
                        sorted = false;
    
    				} else {
    					array[j – 1] = max;		// store the old max
    					max = array[j];			// get the new max
    				}
    			}
    			array[i] = max;   				// store the max
                if ( sorted ) {
    				break;
    			}
    
    		}
    	}
    ```

    

   - range-limiting

     ```cpp
     	Update i to at the place of the last swap
     
     	template <typename Type>
     	void bubble( Type *const array, int const n ) {
     		for ( int i = n - 1; i > 0; ) {
     			Type max = array[0];
     			int ii = 0;
     			for ( int j = 1; j <= i; ++j ) {
     				if ( array[j] < max ) {
     	   			array[j - 1] = array[j];
     					ii = j - 1;
     				} else {
     					array[j – 1] = max;
     					max = array[j];
     				}
     			}
     			array[i] = max;
     			i = ii;
     		}
     	}
     
     ```

  - Alternating Bubble Sort



## Merge Sort

- procedure：

  –If the list is of size 1, it is sorted—we are done;

  –Otherwise:

  •Divide an unsorted list into two sub-lists,

  •Sort each sub-list recursively using merge sort

  ```cpp
  template <typename Type>
  void merge_sort( Type *array, int first, int last ) {
      if ( last - first <= N ) {
          insertion_sort( array, first, last );
      } else {
          int midpoint = (first + last)/2;
  
          merge_sort( array, first, midpoint );
          merge_sort( array, midpoint, last );
          merge( array, first, midpoint, last );
      }
  }
  
  ```

  •Merge the two sorted sub-lists into a single sorted list(not in-place ,**memory requirement** is **theta(n)** and **time** is **theta(n)**)

  ```cpp
  #include <cassert>
  		//...
  		int i1 = 0, i2 = 0, k = 0;
  
  		while ( i1 < n1 && i2 < n2 ) {
  		    if ( array1[i1] < array2[i2] ) {
  		        arrayout[k] = array1[i1];
  		        ++i1;
  		    } else {
  		        assert( array1[i1] >= array2[i2] ); 
  		        arrayout[k] = array2[i2];
  		        ++i2;
  		    }
  		    ++k;
  		}
  ```

  ​	We would continue until we have passed beyond the limit of one of the two arrays. After this, we simply copy over all remaining entries in the non-
   empty array

  ![image-20241104212928160](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241104212928160.png)

  ```cpp
  for ( i1 < n1; ++i1, ++k ) {
  		    arrayout[k] = array1[i1];
  		}
  
  		for ( i2 < n2; ++i2, ++k ) {
  		    arrayout[k] = array2[i2]; 
  		}
  
  ```

  - time complexity:

    一共处理logn层，每层都是O(n)的merge处理时间和O(1)/O(2)/.../O(n/2)拆成subarray的时间。

    ![image-20241104213815215](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241104213815215.png)

    T(*n*) = Theta(*n* ln(*n*))



## Quick Sort

- The idea behind the algorithm

  - Chose an object（**pivot**） in the array and partition the remaining objects into two groups relative to the chosen entry

  - Like merge sort, we can either:

    - Sort the sub-lists using quicksort

    - If the size of the sub-list is sufficiently small, apply insertion sort

- The run time and worst-case scenario

  In the best case：

  >  the list will be split into two approximately equal sub-lists, and thus, the run time could be very similar to that of merge sort: **Q(*n* ln(*n*))**

  worst:

  > each time select the minimum. **Theta(n^2)**

  - Analysis of running time

    - Proposition.  The expected number of compares to quicksort an array of *n* distinct elements *a*1 < *a*2 < ⋯ < *an* is *O*(*n* log *n*).

    - Consider BST representation of pivot elements.

      –*ai* and *aj* are compared once if one is an ancestor of the other.

      –**Pr** [ *ai* and *aj* are compared ] = 2 / ( *j* - *i* + 1), where *i* < *j*.![image-20241104215046773](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241104215046773.png)

      ![image-20241104215125122](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241104215125122.png)

- Strategy for avoiding the worst-case: median-of-three

  –Choose the median of the first, middle, and last entries in the list

  –move the smallest entry to the first entry

  -move the largest entry to the middle entry

  ![image-20241104215714274](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241104215714274.png)

–Implementing quicksort in place

```cpp
template <typename Type>
void quicksort( Type *array, int first, int last ) {
    if ( last - first <= N ) {
        insertion_sort( array, first, last );
    } else {
        Type pivot = find_pivot( array, first, last );
        int low  =find_next( pivot, array, first + 1 );//
        int high = find_previous( pivot, array,  last - 2 );

        while ( low < high ) {
            std::swap( array[low], array[high] );
            low  =find_next( pivot, array,  low + 1 );
            high = find_previous( pivot, array, high - 1 );
        }

        array[last – 1] = array[low];
        array[low] = pivot;
        quicksort( array, first,  low );
        quicksort( array,  high, last );
    }
}
```

![image-20241104220108856](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241104220108856.png)

最后pivot放到low的位置，low原本的放到末尾

# Divide and Conquer

- Master Theorem

  ![image-20241102182905229](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102182905229.png)

  > –*a* ≥ 1 is the number of subproblems.
  >
  > –*b* ≥ 2 is the factor by which the subproblem size decreases.
  >
  > –*f* (*n*) ≥ 0 is the work to divide and combine subproblems.

  ![image-20241102185044848](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102185044848.png)

  > •Case 1: d>logba implies the “work” is decreasing as the level increases, then the total work is dominated by Θ(n^d) 
  >
  > •Case 2: d=logba implies the “work” is almost the same across the levels, then the total work is Θ(n^d logn)
  >
  > •Case 3: d<logba implies the “work” is increasing as the level increases, then the total work is dominated by Θ(n^logba) 

   1. Divide-and-conquer multiplication

      ![image-20241102184527984](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102184527984.png)

      ![image-20241102184607798](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102184607798.png)

      ![image-20241102184630869](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102184630869.png)

      2. matrix multiplication

         – Divide:   partition *A* and *B* into ½*n*-by-½*n* blocks.

         – Conquer:   multiply 8 pairs of ½*n*-by-½*n* matrices, recursively.

         – Combine: add appropriate products using 4 matrix additions.

         

         ![image-20241102184902553](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102184902553.png)

         > ![image-20241102185532933](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102185532933.png)
         >
         > multiplying two 3-by-3 matrices via 21 scalar multiplications è multiplying two *n*-by-*n* matrices via 21 two *n/3*-by-*n/3* multiplications 

         

# Tree

1. Phylogenetic trees
   - nodes with degree 2 or 0.


>  depth	
>
> - the length of the path from the root node to that node.

​	

>  height
>
> - The *height* of a tree is defined as the maximum depth of any node within the tree
>
> - The height of a tree with one node is 0
>
>   –Just the root node
>
> - we define the height of the empty tree to be **–1**



>  a node is both an **ancestor and a descendant 9of itself**

–We can add the adjective ***strict*** to exclude equality: *a* is a *strict descendant* of *b* if *a* is a descendant of *b* but *a ≠* *b*

2. 树的多种实现方式

   array:

   –Complete tree can be stored using an array using Q(*n*) memory

   –An arbitrary tree of *n* nodes requires **O**(2*n*) memory

![image-20241027182847631](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027182847631.png)

![image-20241027182914932](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027182914932.png)

![image-20241027183000963](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027183000963.png)

3. Monte Carlo Tree Search

MCTS has four key steps to explore the game tree:

- Selection: Start from the root node and traverse the tree using a tree exploration policy until a leaf node L is reached

- Expansion: Create one (more) new child nodes of L if it is not the terminal state, otherwise just do backpropagation(randomly)

- Simulation: Perform random (Monte Carlo) simulations from the new node to a terminal state (win, draw, or loss)

- Backpropagation: Propagate the results of the simulation back up the tree, updating node statistics (win rates) along the way

![image-20241027183417967](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027183417967.png)

We need to balance Exploration and Exploitation

![image-20241027183506489](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027183506489.png)

> Keep visiting child nodes with the largest UCB until the node is a leaf node.
>
> Create one (more) new child nodes of the leaf node and expand the tree.
>
> Simulate random actions for both players until the game is end (win, draw or loss). Note you might simulate multiple times. 
>
> Backpropagate the result (win rate) from the leaf node to the root node.
>



![image-20241027183603458](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027183603458.png)

![image-20241027183613755](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027183613755.png)

4. Forest:

Traversals on forests can be achieved by treating the roots as children of a national root.

- Any tree can be converted into a forest by removing the root node

- Any forest can be converted into a tree by adding a root node that has the roots of all the trees in the forest as children

## Binary tree

>  A *full* node is a node where both the left and right sub-trees are non-empty trees

![image-20241027183854879](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027183854879.png)

1. full binary tree

![image-20241027183919459](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027183919459.png)

2. perfect binary tree

![image-20241027184032516](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027184032516.png)

> A binary tree of height *h* = 0 is perfect
>
> A binary tree with height *h* > 0 is a perfect if both sub-trees are prefect binary trees of height *h* – 1
>
> A **perfect binary tree** of height *h* has **2^{h+1} – 1** nodes
>
> A perfect binary tree with *n* nodes has height **log(*n* + 1) – 1**
>
> A perfect binary tree with height *h* has 2^*h* leaf nodes



- ![image-20241027184240413](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027184240413.png)

3. complete binary tree

- Reason: we require binary trees which are similar to perfect binary trees, but defined for any number of nodes

- definition:

 Recursive definition: a binary tree with a single node is a complete binary tree of height *h* = 0 and a complete binary tree of height *h* is a tree where either:

–The left sub-tree is a **complete** **tree** of height *h* – 1 and the right sub-tree is a **perfect tree** of height ***h* – 2**, or

–The left sub-tree is **perfect tree** with height *h* – 1 and the right sub-tree is **complete tree** with height *h* – 1

> The height of a complete binary tree with *n* nodes is ***h* = ⌊lg(*n*)⌋**
>
> ![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c0cbacd7948bf3fa72e17a1e0d0fadf3.png)



- Knuth transform

The transformation of a general tree into a left-child right-sibling binary tree has been called the Knuth transform

![image-20241027184725362](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027184725362.png)

![image-20241027184738534](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241027184738534.png)

## Binary heap

>  using complete binary tree to achieve the binary heap.

- Min-heap&&Max-heap

1. Popping the top object is **O**(ln(*n*))

–We **copy something that is already in the lowest depth**—it will likely be moved back to the lowest depth

![image-20241112215640804](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241112215640804.png)



2. Pushing an object is also **O**(ln(*n*))

–If we insert an object less than the root, it will be moved up to the top

An arbitrary removal requires that all entries in the heap be checked: **O**(*n*)

All steps above can be achieved by array,starting from 1.

3. percolate:maintain the min/max element in the top. Check if parent is bigger/smaller than the element we implement. Then swap.
4. ![image-20241102175203460](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102175203460.png)

![image-20241031230026380](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241031230026380.png)

 A removal of the largest object in the heap still requires all leaf nodes to be checked – there are approximately *n*/2 leaf nodes: **O**(*n*)

- build heap(Floyd method)**O(n)**

  Put the keys in a binary tree and fix the heap property!

```cpp
buildHeap(){
  for (i=size/2; i>0; i--)
     percolateDown(i);
}//从最后一层的最后一个内部节点开始下滤

```

> No percolation for the leaf nodes (n/2 nodes)
>
> At most n/4 nodes percolate down 1 level
>
> at most n/8 nodes percolate down 2 levels
>
> at most n/16 nodes percolate down 3 levels


 ![image-20241031224452927](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241031224452927.png)

- heap sort

  - –Place the objects into a heap

    •O(*n*) time

    –Repeatedly popping the top object until the heap is empty

    •O(*n* ln(*n*)) time **(worst case&best case)**

    –Time complexity: O(*n* ln(*n*))

    –This solution requires additional memory: a min-heap of size *n* 

  - unstable

  - in-place implementation
  
    
  
    ![image-20241031230231789](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241031230231789.png)

1. First, we must convert the unordered array with *n* = 10 elements into a max-heap. None of the leaf nodes need to be percolated down, and the last non-leaf node is in position n/2-1

2. pop the maximum element, and then insert it at the end of the array:

3. percolate up

4. time complexity **O(n logn)**



> quiz的其他补充：如果使用heap去获得第k大的元素，那么需要时间复杂度为O(logn)，但如果是用heap对应的数组去获取，那么就是O（1），因为这时候第k大的元素必定在2^k个元素以内，2^k算是个常数。
> 

## BST

1. Insertion：

![image-20241102134400330](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102134400330.png)

> –If we find the object already in the tree, we will return
>
> -The object is already in the binary search tree (no duplicates)
>
> –Otherwise, we will arrive at an empty node
>
> –The object will be inserted into that location
>
> –The run time is **O**(*h*)
>
> Since **BST may be very unbalanced** ,so the worst case of finding the maximum number may be **O(n).**

2. Erase

   - The node is a leaf node![image-20241102175557761](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102175557761.png)

   - It has exactly one child

   ![image-20241102175646625](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102175646625.png)

   ![image-20241102175705622](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102175705622.png)

   - It has two children (it is a full node)

​		–Replace 42 with the minimum object in the right sub-tree

​		–Erase that object from the right sub-tree

3. Find the next (larger) and previous (smaller) objects of a given object which may or may not be in the sorted list.

   - If, however, there is no right sub-tree:

     –It is the first larger object (if any) that exists in the path from the node to the root
     
     > with given value of node, finding the node which is greater and the ->left is<= than the given val 

4. Find the *k*th entry of the sorted list

   – Recall that *k* goes from 0 to *n* – 1

   – If the left-sub-tree has *ℓ* = *k* entries, return the current node,

   – If the left sub-tree has *ℓ* > *k* entries, return the *k*th entry of the left sub-tree,

   – Otherwise, the left sub-tree has *ℓ* < *k* entries, so return the (*k* – *ℓ* – 1)th entry of the right sub-tree

5. Iterate through those objects that fall on an interval [*a*, *b*]



## AVL

A binary search tree is said to be AVL balanced if:

–The difference in **the heights between the left and right sub-trees is at most 1,** and

–Both sub-trees are themselves AVL trees

 Recall:

 An empty tree has height –1

 A tree with a single node has height 0

- Height of an AVL Tree

  > F(*h*) = F(*h* – 1) + 1 + F(*h* – 2)
  >
  > nodes number:
  >
  > F(*h*) ≈ 1.8944 *f* ^h– 1 
  >
  >  where *f* ≈ 1.6180 is the golden ratio（\frac{1+5^{1/2}}{2}）
  >
  > –That is, F(*h*) = **Theta**(*f* ^*h*)
  >
  > ![image-20241106155302626](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241106155302626.png)

- Maintaining Balance

  Introduce a member variable：

   **int** tree_height;

  This variable is updated during inserting and erasing

  find the deepest node that is unbalanced and its parent, child

  - insert

    –Both balances are **Q**(1)

    –All insertions are still **Q**(ln(*n*)): search+1reconstruct

    –It is possible to *tighten* the previous code

    - single rotation: left-left/right-right

      ![image-20241106155831820](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241106155831820.png)

      

    ![image-20241106155853468](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241106155853468.png)

    The height of the tree remains the same. Only need one rotation.

    - double rotation:left-right/right-left

      ![image-20241106160311455](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241106160311455.png)

      ![](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241106160357628.png)

  - Erase
    - same rotation

  –May require O(*h*) corrections to maintain balance

  –Each correction require Q(1) time

  –Depth *h* is **Q**( ln(*n*) )

  So the time complexity is **O**( ln(*n*) )
  
- Both insertion and erasion 's corrections take theta(1) .

## Huffman Coding

1. reason: compress,reduce size of data 

 (number of bits needed to represent data)

2. Algorithm

   1. Scan text to be compressed and count frequencies of all characters.

   2. Prioritize characters based on their frequencies in text.

      ![image-20241102182601204](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102182601204.png)

      ![image-20241102182628898](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102182628898.png)

   3. Build Huffman code tree based on <u>prioritized list</u>.

      - **Huffman tree must be a full binary tree.**
      - full binary tree must has odd nodes.
   
   4. Perform a traversal of tree to determine all code words.
   
   5. Encode the text using the Huffman codes.

![image-20241102182647495](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241102182647495.png)

​	6. decoding

# Disjoint_Set

- **find(a)**: find the representative object of the disjoint set that **a** belongs to/–Find the root element of the tree that contains i

  > –Given two elements **a** and **b**, they are in the same set if
  >
  >  **find( a ) == find( b )**

- set_union( int i, int j )

  –Find the root elements of i and j

  –Update the parent of one root element to be the other root element

  - Optimization 1:union-by-rank

    point the root of the shorter tree to the root of the taller tree,–The height of the taller will increase if and only if the trees are equal in height.

    - Worst-Case Scenario![image-20241118185931944](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241118185931944.png)
    - ![image-20241118185956031](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241118185956031.png)
    - The height and average depth of the worst case are **O**(ln(*n*))
    -  the average height of such a tree is **o**(ln(*n*))

  - 2:Path Compression

    - ![image-20241118190104647](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241118190104647.png)
    - when find an element,just put all his ascendents as a child of the root.

- Maze Generation迷宫生成

  ![image-20241118190403922](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241118190403922.png)

# Graph

**Undirected Graphs**

1. {*v*1, *v*1} will not define an edge.

2. The maximum number of edges in an undirected graph is

![image-20241115115817881](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241115115817881.png)

3. The degree of a vertex is defined as the number of adjacent vertices



- Weighted graphs

- ![image-20241115122336920](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241115122336920.png)

  A weight may be associated with each edge in a graph

  The ***length*** of a path within a weighted graph is the sum of all of the edges which make up the path.

- Sub-graphs

  A *sub-graph* of a graph contains a **subset** of the vertices and a subset of the edges that connect the **subset** of the vertices in the original graph

- Vertex-induced sub-graphs

  A *vertex-induced* *sub-graph* contains a **subset** of the vertices and **all** the edges in the original graph between those vertices

- Path

  1. Simple path

     A *simple path* has no repetitions vertex 

  2. Simple cycle

     *A simple cycle is a cycle in a Graph with no repeated vertices (except for the beginning and ending vertex).*

- Tree

  A graph is a tree if it is connected and there is a unique path between any two vertices

  Any tree can be converted into a rooted tree 

  > Properties:
  >
  > –The number of edges is |*E*| = |*V*| – 1 
  >
  > –The graph is *acyclic*, that is, it does not contain any cycles
  >
  > –Adding one more edge must create a cycle
  >
  > –Removing any one edge creates two unconnected sub-graphs

  - Forests

    > –The number of edges is |*E*| < |*V*|
    >
    > –The number of trees is |*V*| – |*E*|
    >
    > –Removing any one edge adds one more tree to the forest

**Directed graph**

1. The maximum number of directed edges in a directed graph is![image-20241115123238880](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241115123238880.png)

2. The degree of a vertex in a directed graph:

   –Vertices with an in-degree of zero are described as *sources*

   –Vertices with an out-degree of zero are described as *sinks*

3. Connectedness

   –A graph is ***strongly connected*** if there exists a **directed** path between any two vertices。必须含有成环。

   –A graph is ***weakly connected*** there exists a path between any two vertices that ignores the direction![image-20241115123450993](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241115123450993.png)

- Weighted directed graphs

- DAG（有向无环图）

**平面图**：在一个平面上，如果能够画出无向图 G 的图解，其中没有任何边的交叉，则称图 G 是个**平面图**；否则，称 G 是**非平面图**。

- **库拉托夫斯基图：**由观察法均可证明， K3,3 与 K5 都是非平面图， K3,3 是边最少的非平面图， K5 是结点最少的非平面图。
- **同胚操作：**在原图的边上增加或者删除度数为 2 的结点，不会影响图的平面性
- **同胚图：**设 G1 和 G2 是两个无向图。如果 G1 和 G2 是同构的，或者通过同胚操作，能够把 G1 和 G2 转化成同构的图，则称 G1 和 G2 是**同胚**的。
- **库拉托夫斯基定理：**设 G 是一个无向图。图 G 中**不**存在任何与 K3,3 或 K5 同胚的子图，当且仅当图 G 是平面图。

欧拉公式：

![image-20241122074154958](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241122074154958.png)

对偶图：

![image-20241122074322614](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241122074322614.png)

着色问题：

![image-20241122074402247](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241122074402247.png)

**Representation**

1. Binary-relation list(most inefficient)

   > –Requires Q(|*E*|) memory
   >
   > –Determining if *v**j* is adjacent to *v**k* is O(|*E*|)
   >
   > –Finding all neighbors of *v**j* is Q(|*E*|)

2. Adjacency matrix()

![image-20241115123952819](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241115123952819.png)

> –Requires Q(|*V*|^2) memory
>
> –Determining if *vj* is adjacent to *vk* is O(1)
>
> –Finding all neighbors of *vj* is Q(|*V*|)

3. Adjacency list

4. ![image-20241115180520591](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241115180520591.png)

5. ![image-20241115180548978](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241115180548978.png)

![image-20241115124149318](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241115124149318.png)

遍历的时间复杂度V+E

**Graph Traversal**

1. Breadth-first traversal **用来找两点之间最短的路径**，边权相等。

> –Choose any vertex, mark it as visited and push it onto queue
>
> –While the queue is not empty:
>
> •Pop the top vertex *v* from the queue
>
> •For each vertex adjacent to *v* that has not been visited:
>
> –Mark it visited, and
>
> –Push it onto the queue

2. Depth-first traversal(结果不唯一)

    Use a stack:

   –Choose any vertex

   •Mark it as visited

   •Place it onto an empty stack

   –While the stack is not empty:

   •If the vertex on the top of the stack has an unvisited adjacent vertex *v*,

   –Mark *v* as visited

   –Place *v* onto the top of the stack

   •Otherwise, pop the top of the stack

> –Choose any vertex, mark it as visited
>
> –From that vertex:
>
> •If there is another adjacent vertex not yet visited, go to it
>
> •Otherwise, go back to the previous vertex
>
> –Continue until no visited vertices have unvisited adjacent vertices
>
> 空间复杂度 theta（v）
>
> 时间：用邻接矩阵表示时，theta（v+e），BFS同理。

3. check connectedness

>  Consider implementing a br eadth-first traversal on an undirected graph:
>
> –Choose any vertex, mark it as visited and push it onto queue
>
> –While the queue is not empty:
>
> ​	•Pop to top vertex *v* from the queue
>
> ​	•For each vertex adjacent to *v* that has not been visited:
>
> ​		–Mark it visited, and
>
> ​		–Push it onto the queue

 This continues until the queue is empty

–Note:  if there are no unvisited vertices, the graph is connected,

# Greedy Algorithm

Suppose it is possible to build a solution through a sequence of partial solutions

–At each step, we focus on one particular partial solution and we attempt to extend that solution

–Ultimately, the partial solutions should lead to a feasible solution which is also optimal

局部最优推出全局最优。

## Coin changing

- method

–Making the exact change with the minimum number of coins

–Consider the Euro denominations of 1, 2, 5, 10, 20, 50 cents

–Stating with an empty set of coins, add the largest coin possible into the set which does not go over the required amount

- Our coin change example is greedy because:

–Any subset of *k* coins is a partial solution

–The value to any partial solution is the sum of the values（0.5 （+0.2）0.7）

 Then given a partial solution, we

–Add that coin which maximizes the increase in value without going over the target value

 We continue building the set of coins until we have reached the target value

 An optimal solution is found with euros and cents, but not with our *quadratic* dumbledore coins。

>  Consider 1, 4, 9, 16, 25, 36, and 49 dumbledores,会出现（72==36+36）

## Interval scheduling

![image-20241124123132105](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124123132105.png)

Earliest-finish-time-first algorithm

![image-20241124123413260](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124123413260.png)

![image-20241124123518312](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124123518312.png)

Pf:[by contradiction]

・**Assume** greedy is not optimal, and let’s see what happens.

・Let *i*1, *i*2, ... *i**k* denote set of jobs selected by greedy.

・Let *j*1, *j*2, ... *j**m* denote set of jobs in an optimal solution with
 *i*1 = *j*1, *i*2 = *j*2, ..., *i**r* = *j**r* for the largest possible value of *r*. 

![image-20241124123723598](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124123723598.png)

现在ik和im做好之后，时间无法再安排其他工作。从i1到ir，两种方法都安排了一样的工作，但是jr+1没有安排结束的早的工作，并且jr+1到jm里面安排的工作比i多,即m>k。按照贪心算法的选取规则，ik结束的时间不会比jk晚，但由于更优算法里面的m>k，说明中间还有其他工作可以供贪心算法选择。

## Interval partitioning

![image-20241124124816694](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124124816694.png)

至少需要的教室数量就是同一时间点最大的同时上课的数量。

**Greedy template**: Consider lectures in some natural order. Assign each lecture to an available classroom (which one?); allocate a new classroom
 if none are available.

![image-20241124125013649](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124125013649.png)

![image-20241124125152939](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124125152939.png)

![image-20241124125206704](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124125206704.png)

Def. The depth of a set of open intervals is the maximum number of intervals that contain any given point.

Key observation. Number of classrooms needed ≥ depth.

![image-20241124125604608](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124125604608.png)

Pf.:

・Let *d* = number of classrooms that the algorithm allocates.

・Classroom *d* is opened because we needed to schedule a lecture, say *j*,
 that is incompatible with a lecture in each of *d* – 1 other classrooms.

・Thus, these *d-1* lectures each end after *sj*.

・Since we sorted by start time, each of these incompatible lectures start no later than *sj*.

・Thus, we have *d* lectures overlapping at time *s**j* + e.

・Key observation : all schedules use>=*d* classrooms. ▪

## Scheduling to minimize lateness

![image-20241124125726644](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124125726644.png)



![image-20241124130106757](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124130106757.png)

observation1：The earliest-deadline-first schedule has **no idle time**.

![image-20241124130212017](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124130212017.png)

Def. Given a schedule *S*, an inversion is a pair of jobs *i* and *j* such that:
 d_i ≤ d_j but *j* is scheduled before *i*.

observation2：The earliest-deadline-first schedule is the unique idle-free schedule with no inversions.

![image-20241124130438100](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124130438100.png)

**Key claim**. Exchanging two adjacent, inverted jobs *i* and *j* reduces the n















umber of inversions by 1 and does not increase the max lateness.

![image-20250102225202918](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20250102225202918.png)

![image-20241124130820126](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124130820126.png)

![image-20241124130845638](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124130845638.png)

贪心

算法没有inversion，假设最优算法有最少数量的inversion，那么如果交换相邻逆序对的话，并不会增加最大lateness，反而可能减少，这下就不是最优的，所以矛盾了。

## Minimum Spanning Tree

–If All edge weights are distinct

 This guarantees that given a graph, there is a **unique** minimum spanning tree.

**Proof:Greedy exchange argument** 

![image-20241124121447518](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124121447518.png)

虽然e很小，但是因为e和T‘会成环，所以T’不选e。

![image-20241124121834124](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241124121834124.png)



**Minimum Spanning Tree**: 

最小生成树再加一条边就会有环，此时要去除环里面最大的一条边，所以加入一条边最多引起一处变化。

完全图有n^{n-2}棵生成树

### Prim’s algorithm

​	Add the edge *e_k* with least weight that connects this minimum spanning tree to a new vertex *v_{k* + 1}

 - proof:

   Cut property

   ![image-20241120154135598](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241120154135598.png)

- implementation

  1. Initialization:

  –Select a root node and set its distance as 0

  –Set the distance to all other vertices as ∞ 

  –Set all vertices to being unvisited

  –Set the parent pointer of all vertices to 0

  If all vertices have been visited, we have a spanning tree of the entire graph.

  **If there are vertices with distance ∞, then the graph is not connected and we only have a minimum spanning tree of the connected sub-graph containing the root**

  2.  Iterate while there exists an unvisited vertex with distance < ∞

     –Select the unvisited vertex *v* with minimum distance

     –Mark *v* as having been visited

     –For each unvisited adjacent vertex of *v*, if the weight of the connecting edge is less than the current distance to that vertex:

     •Update the distance to the weight of the edge

     用堆维护点，不在最小生成树的点都在堆里面。最开始就是初始点的邻居有距离。每次选取一个点把它拿出来，设为visited，然后更新：（跟存图的方式）遍历v节点的边，总共就是2E，更新的时间复杂度是lnV，更新的时间复杂度是ElnV（邻接表里面）最多往堆里面塞2E次，堆里面会有过时信息。
     
     每次放出一个点要花lnV时间调整。
     
     •Set *v* as the parent of the vertex

- analysis

  ![image-20241120155343663](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241120155343663.png)

  每个节点找到所有的邻居都是O(V+E)，每个节点都要找最近的邻居Theta（V(V-1)+E），update已经包括在前面了。

  ![image-20241120155703337](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241120155703337.png)

### Kruskal’s algorithm

​	•Sort the edges by weight

​	•Go through the edges from least weight to greatest weight

​	add the edges to the spanning tree so long as the addition does not create a cycle

- implementaion

  –We would store the edges and their weights in an array

  –We would sort the edges using some sorting algorithm: O(|*E*| ln(|*E*|))

  –For each edge, add it if no cycle is created. 

  •How do we determine if a cycle would be created?

  •Check if the two vertices of the edge are already connected by the added edges.

  - disjoint set

  

- analysis

 1. The critical operation is determining if two ver2tices are connected. If we perform a traversal on the added edges, it is O(|*V*|). Consequently, the total run-time would be O(|*E*| ln(|*E*|) + |*E*|·|*V*|) = O(|*E*|·|*V*|)

 2. disjoint sets O(a(n))near 4.

    –Consider edges in the same connected sub-graph as forming a set

    –If the vertices of the next edge are in different sets, take the union of the two sets

    –Do not add an edge if both vertices are in the same set

​	![image-20241120160448784](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241120160448784.png)

![image-20241120160457620](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241120160457620.png)

 The dominant time is now the time required to sort the edges, which is O(|*E*| ln(|*E*|)) = O(|*E*| ln(|*V*|))

例题：

一幅图的各个点v之间要么不连接独自算点权重ai，要么连接算边权重ei。那么最短的连接权重解法是，再加一个空的点，让这个空的点和每一个点连接，计算V+1顶点图的MST。

# Topological Sorting

- motivation:

  Dependency between tasks: one task is required to be done before the other task can be done

- premise

  A topological sorting of the vertices in a DAG is an ordering

*v*1, *v*2, *v*3, …, *v*|*V*|

 such that *vj* appears before *vk* if there is a path from *vj* to *vk*

如果有一条路径从j到k，并且由于图有向，那么j出现在k之前。

DAG：有向的环一定是最后回到起点。并且一定会有入度为0的点，这些是没有任何要求的先修课程/已经完成先修要求的课程。一定至少有一个sink和一个source。

![image-20241127083433084](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241127083433084.png)

- Idea:

–Given a DAG *V*, iterate:

•Find a vertex *v* in *V* with in-degree zero

•Let *v* be the next vertex in the topological sort

•Continue iterating with the vertex-induced sub-graph *V* \ {*v*}

![image-20241127083645104](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241127083645104.png)

选了c之后，把c连着的那条边删掉，那么D现在的入度为1。C

![image-20241127083758218](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241127083758218.png)

CH

![image-20241127083810949](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241127083810949.png)

CHD

每次产生入度为0的点会有多个，所以最后选择的可以结果不唯一。

- Theorem:

 **A graph is a DAG if and only if it has a topological sorting**

先证明两个lemma：

1. –A DAG always has at least one vertex with in-degree zero

​	•That is, it has at least one *source*



>  Proof by contradiction: 
>
> 假设没有入度为0的点。
>
> –If we cannot find a vertex with in-degree zero, we will show there must be a cycle
>
> –Start with any vertex and define a list *L* = (*v*)
>
> –Then iterate this loop *|V*| times:
>
> •The first vertex *ℓ*1 in the list *L* does not have in-degree zero
>
> •So we can find a vertex *w* such that (*w*, *ℓ*1) is an edge
>
> •Add *w* to the list: *L* = (*w*, *ℓ*1, …, *ℓk*)
>
> –By the pigeon-hole principle, **at least one vertex must appear twice**
>
> •This forms a cycle; hence a contradiction, as this is a DAG



2. –Any sub-graph of a DAG is a DAG

>  Proof: 
>
> 假设子图里面有环
>
> –If a sub-graph has a cycle, that same cycle must appear in the super-graph
>
> –We assumed the super-graph was a DAG
>
> –This is a contradiction
>
>  ∴ the sub-graph must be a DAG
>
> 

3. DAG一定有Topological Sort

>  数学归纳法证明
>
> Proof by induction:
>
>  基础情况：A graph with one vertex is a DAG and it has a topological sort
>
> 归纳假设 Assume a DAG with *n* vertices has a topological sort
>
> 归纳证明：A DAG with *n* + 1 vertices must have at least one vertex *v* of in-degree zero(lemma1)
>
>  Removing the vertex *v* and consider the vertex-induced sub-graph with the remaining *n* vertices(lemma2)
>
> –If this sub-graph has a cycle, so would the original graph—contradiction
>
> –Thus, the graph with *n* vertices is also a DAG, therefore it has a topological sort
>
>  Add the vertex *v* to the start of the topological sort to get one for the graph of size *n* + 1

4. 如果图有Topological Sort，那么是DAG

> 由逆否命题来证明：
>
>  If a graph is not a DAG, it does not have a topological sort
>
>  By definition, it has a cycle: (*v*1, *v*2, *v*3, …, *vk*, *v*1)
>
> –In any topological sort, *v*1 must appear before *v*2, because (*v*1, *v*2) is a path
>
> –However, there is also a path from *v*2 to *v*1: (*v*2, *v*3, …, *v**k*, *v*1)
>
> –Therefore, *v*2  must appear in the topological sort before *v*1
>
> This is a contradiction, therefore the graph cannot have a topological sort



- 用队列实现快速找到入度为0的点

  C出队列，用邻接表把连接的点的度数-1

![image-20241127092606173](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241127092606173.png)

时间复杂度：Theta（V+E）

–Initially, we must scan through each of the vertices: Theta(|*V*|)

–For each vertex, we will have to push onto and pop off the queue once, also Theta|*V*|)

Finally, every time we remove a vertex *v*, all its edges shall also be removed and the in-degree table be updated

–The run time of these operations is Omega(|*E*|)

–If we are using an adjacency matrix: Theta(|*V*|2)

–If we are using an adjacency list: Theta(|E|)

Q(|*V*| + |*E*|)  if we use an adjacency list

 Q(|*V*|2) if we use an adjacency matrix

 and the memory requirements is Q(|*V*|)



What happens if at some step, all remaining vertices have an in-degree greater than zero?

–There must be at least one cycle within that sub-set of vertices

**–对于connected graph，E>=V-1,所以时间复杂度也可以写成Theta（E）**



- Critical Path

  ![image-20241127093136632](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241127093136632.png)

  要完成E必须先完成D和C

  Suppose each task has a performance time associated with it

  –If the tasks are performed serially, the time required to complete the last task equals to the sum of the individual task times

  –These tasks require 0.3 + 0.7 + 0.5 + 0.4 + 0.1 = 2.0 s to execute serially

Suppose Task A completes

–We can now execute Tasks B and D in parallel

–The least time in which these five tasks can be completed is
  0.3 + 0.5 + 0.4 + 0.1 = 1.3 s

–This is called the *critical time* *of all tasks*

–The path (A, B, C, E) is said to be the *critical path*

定义：

 The *critical path* is the sequence of tasks determining the minimum time needed to complete the project

–If a task on the critical path is delayed, the entire project will be delayed

Tasks that have no prerequisites have a critical time equal to the time it takes to complete that task



 For tasks that depend on others, the critical time will be:

**–The maximum critical time that it takes to complete a prerequisite** 

–Plus the time it takes to complete this task



Each time we pop a vertex *v*, in addition to what we already do:

–For *v*, add the task time onto the critical time for that vertex:

•That is the critical time for *v*

–For each adjacent vertex *w*:

•If the critical time for *v* is greater than the currently stored critical time for *w*

–Update the critical time with the critical time for *v*

–Set the previous pointer to the vertex *v*

| **Task** | **In-degree** | **Task     Time** | **Critical  Time** | **Previous**  **Task** |
| -------- | ------------- | ----------------- | ------------------ | ---------------------- |
| A        | 0             | 5.2               | 5.2                | Ø                      |
| B        | **0**         | 6.1               | **5.2**            | **A**                  |
| C        | 3             | 4.7               | 0.0                | Ø                      |
| D        | **2**         | 8.1               | **5.2**            | **A**                  |
| E        | 2             | 9.5               | 0.0                | Ø                      |
| F        | 0             | 17.1              | 0.0                | Ø                      |

| F    | **B** |      |      |
| ---- | ----- | ---- | ---- |
|      |       |      |      |

![image-20241127094339118](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241127094339118.png)

popF：

| **Task** | **In-degree** | **Task     Time** | **Critical  Time** | **Previous**  **Task** |
| -------- | ------------- | ----------------- | ------------------ | ---------------------- |
| A        | 0             | 5.2               | 5.2                | Ø                      |
| B        | 0             | 6.1               | 5.2                | A                      |
| C        | 3             | 4.7               | 0.0                | Ø                      |
| D        | 2             | 8.1               | 5.2                | A                      |
| E        | 2             | 9.5               | 0.0                | Ø                      |
| **F**    | **0**         | **17.1**          | **17.1**           | **Ø**                  |

更新C、E：

| **Task** | **In-degree** | **Task     Time** | **Critical  Time** | **Previous**  **Task** |
| -------- | ------------- | ----------------- | ------------------ | ---------------------- |
| A        | 0             | 5.2               | 5.2                | Ø                      |
| B        | 0             | 6.1               | 5.2                | A                      |
| **C**    | **2**         | **4.7**           | **17.1**           | **F**                  |
| D        | 2             | 8.1               | 5.2                | A                      |
| **E**    | **1**         | **9.5**           | **17.1**           | **F**                  |
| F        | 0             | 17.1              | 17.1               | Ø                      |

pop B的时候要加上自己的task time：

| **Task** | **In-degree** | **Task     Time** | **Critical  Time** | **Previous**  **Task** |
| -------- | ------------- | ----------------- | ------------------ | ---------------------- |
| A        | 0             | 5.2               | 5.2                | Ø                      |
| **B**    | **0**         | **6.1**           | **11.3**           | **A**                  |
| C        | 2             | 4.7               | 17.1               | F                      |
| D        | 2             | 8.1               | 5.2                | A                      |
| E        | 1             | 9.5               | 17.1               | F                      |
| F        | 0             | 17.1              | 17.1               | Ø                      |

因为B的critical time更大，所以拿B的critical time去更新D

| **Task** | **In-degree** | **Task     Time** | **Critical  Time** | **Previous**  **Task** |
| -------- | ------------- | ----------------- | ------------------ | ---------------------- |
| A        | 0             | 5.2               | 5.2                | Ø                      |
| B        | 0             | 6.1               | 11.3               | A                      |
| C        | **1**         | 4.7               | 17.1               | F                      |
| D        | **1**         | 8.1               | **11.3**           | **B**                  |
| E        | **0**         | 9.5               | 17.1               | F                      |
| F        | 0             | 17.1              | 17.1               | Ø                      |





# Shortest Path-Dijkstra‘s&Bellman-Ford

## Dijkstra’s algorithm

找到图中在最短的一条路径(可以找到每个点到初始点的最小距离)

- 假设：

所有边都是正的，可以为小数

负权边，负权环都不可以有。![img](https://i-blog.csdnimg.cn/blog_migrate/d22cbf6e0ad66725f85586539aac7f0e.png#pic_center)

因为按照dijkstra算法，它的原则是基于贪心进行查找，所以它的查找顺序应该是：BE。

造成的结果就是：找到第二个节点时，就会直接判断E的最短路径为-3.并且后续节点的查找也不会改变这个值。

但是显然，我们知道E的最短路径为-4.

用通俗的话来说，就是面对负权值的环时，[算法](https://edu.csdn.net/course/detail/40020?utm_source=glcblog&spm=1001.2101.3001.7020)在查找时容易”鼠目寸光“，很简单的下定结论。

![image-20241129082851914](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129082851914.png)



能确定AB之间最短的路径是5，但不能确定AD之间是否是最短的路径。因为BD里面可能存在比4更小的路径。

选取路径只能选取已知路径里面最短的那一条路。

Pf:

已知：d(v) is the unvisited path shortest,

假设：assume d'(v) is the shortest path.d'(v)<d(v),s,...,u,v

u有两种情况：

1.u属于Visited set

d'(v)=d(u)+w(u,v)

而由于d(v)是shortest，d（v）<=d(u)+w(u,v)=d'(v)

矛盾

2.u unvisted

d(u)>d(v),因为已知d（v）是所有未被访问过的节点里面最短的。

因此d‘(v)>d(v).



和MST的区别：

该算法的方法是从出发点到下一最优解的最短路径长度。

Prims是从已知最小生成树之间连接的最小连接。（连接到已生成的MST的最短距离）

- strategy

  We will iterate |*V*| times:

  –Find the **unvisited** vertex **v** that has a minimum distance to it

  –Mark it as visited

  –Consider its every adjacent vertex **w** that is **unvisited**:

  •Is the distance to **v** plus the weight of the edge (**v**, **w**) less than our currently known shortest distance to **w** ?

  •If so, update the shortest distance to **w** and record **v** as the previous pointer

   Continue iterating until all vertices are visited or all remaining vertices have a distance of infinity

  ![image-20241129091954560](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129091954560.png)

  ![](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129092248998.png)



![image-20241129092120336](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129092120336.png)



每次update的是新的点到起始点的路径长度。

最终找到的已经不是最小生成树了。

![image-20241129092607462](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129092607462.png)



![img](https://i-blog.csdnimg.cn/blog_migrate/5132498800f6d109ed320658aeb68359.png)

prim的结果

![img](https://i-blog.csdnimg.cn/blog_migrate/b022227dd07528f127a695062cb647ba.png)

dij的结果：Dijkstra算法的执行过程中,从v1到v3的最短路径选择的是v1->v3,而不是v1->v4->v3,原因是Min[3]=Min[4]+G[4][3]，即v1到v3的初始最短距离与v1到v4的最短路径加上v4到v3的距离相等，因此在更新过程中保留v1->v3的最短路径为v1->v3而非v1->v4->v3,所以最后,构造的生成树的边权值之和为1+4+6=11,远大于用Prim算法构造的最小生成树边权值之和1+2+4=7。

![img](https://i-blog.csdnimg.cn/blog_migrate/a8b40722fad0dcc6204000bca0c71797.png)



时间复杂度：

The initialization requires Q(|*V*|) memory and run time

We iterate |*V*| times, each time finding next closest vertex to the source

–Iterating through the table requires Q(|*V*|) time

–Each time we find a vertex, we must check all of its neighbors

•With an adjacency matrix, the run time is Q(|*V*|(|*V*| + |*V*|)) = Q(|*V*|2)

•With an adjacency list, the run time is Q(|*V*|2 + |*E*|) = Q(|*V*|2) as |*E*| = O(|*V*|2)

堆优化：

The initialization still requires Q(|*V*|) memory and run time

–The priority queue will also requires O(|*V*|) memory

–We must use an adjacency list, not an adjacency matrix

用这个记录距离的更新。

 We iterate |*V*|  times, each time finding the *closest* vertex to the source

这个堆每pop出一个元素，就再去加入这个元素的邻接点维护，知道每条相邻边的权重之后到表里面更新。

–Place the distances into a priority queue

–The size of the priority queue is O(|*V*|)

–Thus, the work required for this is O(|*V*| ln(|*V*|))



 Is this all the work that is necessary?

–Recall that each edge visited may result in a distance being updated

–Thus, the work required for this is O(|*E*| ln(|*V*|))

Thus, the total run time is O(|*V*| ln(|*V*|) + |*E*| ln(|*V*|)) = O(|*E*| ln(|*V*|)) 

## Bellman-Ford algorithm

•For each vertex v, dist[v] is an upper bound on the actual shortest distance

–start of at ¥ 

–only update the value if we find a shorter distance

An update procedure（relax）

![image-20241129093617091](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129093617091.png)

![image-20241129094159968](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129094159968.png)

不选择路径，全部走完一遍

![image-20241129094225038](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129094225038.png)

更新FE

![image-20241129094249434](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129094249434.png)

更新A E B（同步更新）

![image-20241129094639520](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129094639520.png)

更新EBC

![image-20241129094931692](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129094931692.png)

更新BCD

![image-20241129095049428](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241129095049428.png)

更新CD

当每个点没有其他边指向自己的更新，那么就更新完毕。

![image-20241204123336439](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241204123336439.png)

O（V.E）

第一层for循环代表的是到source的距离，所以如果第一层只做了k次，那么最后得到的路径就是k条边。但这种方法只能用来求已知k条边是最短路径的情况。

结束后再进行一次遍历，若还能得到某些节点更短的路径就说明有负权环

Bellman只能通过再加一个循环看看路径是否还能更短判断有没有负权环，但它在有负权环的时候是没法找到最短路径的，负权边是可以的。

## A*

和dijkstra的区别

![image-20241204104758385](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241204104758385.png)



–It solves the **single-source shortest** path problem

 Assume we have a heuristic lower bound for the length of a path between any two vertices

This heuristic overestimates the actual distance（the Euclidean distance ：多维空间里衡量两个点之间的绝对距离）
 from B to C

- Heuristic

  1. admissible：**h(*u*, *v*) ≤ d(*u*, *v*)**

     A problem with fewer restrictions on the actions is called a **relaxed problem**

     –The cost of an optimal solution to a relaxed problem is an admissible heuristic for the original problem

     **Theorem**: If *h(n)* is admissible, A* using TREE-SEARCH is optimal

  2. consistent

     A heuristic is consistent if for every node *n*, every successor *n'* of *n* generated by any action *a*, we have 

     ​    *h(n)* *≤* *c(**n,a,n**') + h(n')*这个条件确保了*w**(n)* is non-decreasing along any path.否则的话不能保证每条边是正的可以把heutistic看成地势，如果从高地势走到低地势，那么就是d-h，h>d时出现负权边，那么不能用dijk，所以设置heu表的时候要确保不会出现负权边。

     w(u,v)=d(u,v)+h(v)-h(u)>=0

     w(v,u)=d(u,v)+h(u)-h(v)

     ![image-20241204110417527](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241204110417527.png)

- method

–Marks each vertex as unvisited

–Starts with a **priority queue** containing only the initial vertex *a*

•The priority of any vertex *v* in the queue is the weight w(*v*) which assumes we have found the shortest path to *v* (**initialize it to be infinity** except for the initial vertex a)

•Shortest weights have highest priority

–For each vertex *v*, *d(a, v)* is the shortest known distance from *a* to *v*, *d(a, a) = 0* and *d(a, v)= infinity* for all *v ≠ a*

–For each vertex *v*, *h(v, z)* is the heuristic distance from *v* to *z*

w(v)=d(v)+h(v)

The algorithm then iterates:

1. 图搜索（本质上是dijkstra，要保证w(u,v)>=0）



–Pop the vertex *u* with highest priority

•mark *u* as visited

–For each **unvisited** adjacent vertex (neighbor) *v* of *u*:

•If w(*v*) = d(*a*, *u*) + d(*u*, *v*) + h(*v*, *z*) is less than the current weight/priority of *v**,* update the path leading to *v* and its priority

–If *v* is not in the queue, push v into the queue 

Continue iterating until the item popped from the priority queue is the destination vertex *z*

出现负权边：

2. 树搜索

–Pop the vertex *u* with highest priority

–For **each adjacent** vertex (neighbor) *v* of *u*:（就会再次访问已经被访问过的点，有点类似Bellman）

•If w(*v*) = d(*a*, *u*) + d(*u*, *v*) + h(*v*, *z*) is less than the current weight/priority of *v**,* update the path leading to *v* and its priority

–If *v* is not in the queue, push v into the queue 

Continue iterating until the item popped from the priority queue is the destination vertex *z*

- 时间复杂度

  O(b^d) where b is the branching factor (the average number of successors per state) and d is the depth of the solution

- N Puzzle

You are allowed to move a tile adjacent to the blank into the location of the blank（A*）

The objective is to find a minimum number of moves which will transform the tiles into a standard solution

![image-20241204111025469](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241204111025469.png)

![image-20241204111046241](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241204111046241.png)

The graph of solvable eight puzzles has:

–181 440 vertices

–241 920 edges



 The fifteen puzzle graph has:

–10 461 394 944 000 vertices

–15 692 092 416 000 edges



 In general and
 in the limit:

**(*N* + 1)!/2 vertices**

**(*N* + 1)! edges**



We will consider three distances which we will use with the A* search:

–The discrete distance

•If two objects are equal, the distance is 0, otherwise it is 1

–The Hamming distance

•The number of tiles (including the blank) which are in an incorrect location

–The Manhattan distance

•The sum of the minimum number of moves required to put a tile in its correct location

![image-20241204111645628](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241204111645628.png)

the discrete distance does not improve the situation: it is equivalent to Dijkstra’s algorithm

The Hamming distance isn’t much better:

•It reduces the vertices searched from 179 680 to 178 005 

•It is only useful when we are very close to the actual solution

–The Manhattan distance, however, allows the A* search to find the minimal path with only 6453 vertices searched

## Floyd-Warshall

与Bellman区别：Bellman只要是每次都更新已走的附近节点到起始点的距离。但是Floyd首先拿到每两点之间的距离，之后通过插入不同节点，再次更新每两点之间的距离。最后得到的其实是一个更广的最短路径范围。（能够知道每两点之间的最短路径）

- 目的：

  If we wanted to find the shortest path between all pairs of vertices, we could apply Dijkstra’s algorithm to each vertex:

  –Run time: O(|*V*| |*E*| ln(|*V*|))

  If  E=\Theta{V^2} , running Dijkstra for each vertex is O(|*V*|^3 ln(|*V*|))

  为了找到一个算法复杂度低于O(|*V*|^3 ln(|*V*|))

  We will look at the Floyd-Warshall algorithm

  –It works with positive or negative weights with no negative cycle

  原理：d\[i]\[j]=min{d\[i]\[j],d\[i]\[k]+d\[k]\[j]}

- example

  ![image-20241206085835533](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241206085835533.png)

![image-20241206085850087](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241206085850087.png)

每次两点之间插入一个点，更新所有边。

总共插入V次。最后所有点都塞完了，没有负权环那么就是最短的路径。但如果再塞一个点它还能更短，那么一定有负权环。

节省时间的思路：记录下会需要重新利用的点。我们能保证之后没加入一个点，d[j,k]都是最短路径

更新的点的路径变为vi、vk、vj



- 如何找路径

  需要一个表记录起始点到达的下一个点。

  These are all the adjacent edges that are still the shortest distance

  ![image-20241206094205438](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241206094205438.png)

  ![image-20241206094604889](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241206094604889.png)

  

![image-20241206094152420](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241206094152420.png)*p*4,1 = 5, *p*5,1 = 2, *p*2,1 = 3, *p*3,1 = 1;
   the shortest path to *v*1 is (4, 5, 2, 3, 1)

![image-20241216190425317](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241216190425317.png)

```cpp
for ( int k = 0; k < num_vertices; ++k ) {//cha'ru
    for ( int i = 0; i < num_vertices; ++i ) {
        for ( int j = 0; j < num_vertices; ++j ) {
            if ( d[i][j] > d[i][k] + d[k][j] ) {
                p[i][j] = p[i][k];//记录下一个点走哪个
                d[i][j] = d[i][k] + d[k][j];
            }
        }
    }
}

```

# Dynamic Programming

**Greedy.** Process the input in some order, myopically making irrevocable decisions.

**Divide-and-conquer.** Break up a problem into independent subproblems;
 solve each subproblem; combine solutions to subproblems to form solution to original problem. 
 **Dynamic programming.** Break up a problem into a series of overlapping subproblems; combine solutions to smaller subproblems to form solution to large subproblem.

斐波那契数列的时间复杂度$ \Theta(2^n)$

时间复杂度这么高是因为有很多项需要重复调用（如F(0)，F（1））

If sub-problems re-occur, the problem is said to have *overlapping* *sub-problems*

优化：利用extra memory把需要重复使用的项存起来。

动态规划问题有传递函数，最优解与最优解之间的传递。

divide And conquer 子问题之间是独立的。

## **例子1：**Weighted interval scheduling

・Job *j* starts at *s**j*, finishes at *f**j*, and has weight *w**j*  > 0.

・Two jobs are compatible if they don’t overlap.

・Goal: find max-weight subset of mutually compatible jobs.



- **Earliest finish-time first.**

・Consider jobs in ascending order of finish time.

・Add job to subset if it is compatible with previously chosen jobs.



- *Recall.** Greedy algorithm is correct if all weights are 1.



- **Observation.** Greedy algorithm fails spectacularly for weighted version.

- **Convention.** Jobs are in ascending order of finish time: *f*1 ≤ *f*2 ≤ . . . ≤ *f**n* .按照结束时间排序

![image-20241211090112193](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241211090112193.png)**Def.** *p*(*j*) = largest index *i < j* such that job *i* is compatible with *j*.

**Ex.** *p*(8) = 1, *p*(7) = 3, *p*(2) = 0.

**Def.** *OPT*(*j*) = max weight of any subset of mutually compatible jobs for subproblem consisting only of jobs 1, 2, ..., *j*.


**Goal.** *OPT*(*n*) = max weight of any subset of mutually compatible jobs.

- Case 1. *OPT*(*j*) does not select job *j*.没选

・Must be an optimal solution to problem consisting of remaining
 jobs 1, 2, ..., *j* – 1. 

- Case 2. *OPT*(*j*) selects job *j*.选了

・Collect profit *w**j*.

・Can’t use incompatible jobs { *p*(*j*) + 1, *p*(*j*) + 2, ..., *j* – 1 }.

・Must include optimal solution to problem consisting of remaining compatible jobs 1, 2, ..., *p*(*j*).



![image-20241211085449059](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241211085449059.png)

| j        | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| -------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| p(j)     | 0    | 0    | 0    | 0    | 1    | 0    | 2    | 3    | 1    |
| OPT(j)   | 0    | 1    | 2    | 3    | 5    | 5    | 8    | 10   | 10   |
| w_j      | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| Solution | 空集 | 1    | 2    | 3    | 1、4 | 1、4 | 2、6 | 3、7 | 3、7 |

每次更新OPT时，Solution选择的是更大的情况。

暴力实现：不使用memory

![image-20241211092606875](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241211092606875.png)

Theta（nlogn）二分查找

![image-20241211092617040](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241211092617040.png)

复杂度（2^n）

![image-20241211092822798](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241211092822798.png)

![image-20241211092716742](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241211092716742.png)Top-down dynamic programming (memoization).

![image-20241211093056555](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241211093056555.png)

・Sort by finish time: 𝚹(*n* log *n*) via merge sort.

Compute *p*[*j*] for each *j* : *O*(*n* log *n*) via binary search.

![image-20241211093113372](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241211093113372.png)

・M-Compute-Opt(*j*): each invocation takes *O*(1) time and either

-(1) returns an initialized value *M*[*j*]

-(2) initializes *M*[*j*] and makes two recursive calls

・Progress measure Φ = # initialized entries among *M*[1..*n*].

-initially Φ = 0; throughout Φ ≤ *n*. 

-increases Φ by 1 ⇒ ≤ 2*n* recursive calls.

Overall running time of M-Compute-Opt(*n*) is *O*(*n*).  

总共：\$ Theta{nlogn}\$

## **例子2：House coloring problem**

Paint a row of *n* houses red, green, or blue so that

1. No two adjacent houses have the same color.

2. Minimize total cost, where *cost*(*i*, *color*) is cost to paint *i* given color.

限制条件：和前一个房子的兼容性。

![image-20241211094036890](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241211094036890.png)

|       | A    | B    | C    | D    | E    | F    |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- |
| Red   | 7    | 6    | 7    | 8    | 9    | 20   |
| Green | 3    | 8    | 9    | 22   | 12   | 8    |
| Blue  | 16   | 10   | 4    | 2    | 5    | 7    |

Subproblems.

・*R*[*i*] =  min cost to paint houses 1, …, *i* with *i* red.

・*G*[*i*] =  min cost to paint houses 1, …, *i* with *i* green.

・*B*[*i*] =  min cost to paint houses 1, …, *i* with *i* blue.

・Optimal cost = min { *R*[*n*], *G*[*n*], *B*[*n*] }.


 Dynamic programming equation.

・*R*[*i* + 1] = *cost*(*i+*1, *red*)   + min { *B*[*i*], *G*[*i*] }

这一步能涂成红的，前面要么是蓝、绿，选最小的

・*G*[*i* + 1] = *cost*(*i+*1, *green*) + min { *R*[*i*], *B*[*i*] }

・*B*[*i* + 1] = *cost*(*i+*1, *blue*)  + min { *R*[*i*], *G*[*i*] }

 Running time. *O*(*n)*.



## ***例子3：背包问题

- Knapsack problem

![image-20241213083616427](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241213083616427.png)

![image-20241213090724149](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241213090724149.png)



兼容性：item的重量会改变兼容性

OPT(i-1,w-wi)是之前算好的



![image-20241213090123609](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241213090123609.png)

![image-20241213084430296](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241213084430296.png)

![image-20241213090217767](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241213090217767.png)

|             | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   |
| ----------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 空集        | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| {1}         | 0    | 1    | 1    | 1    | 1    | 1    | 1    | 1    | 1    | 1    | 1    | 1    |
| {1,2}       | 0    | 1    | 6+0  | 7    | 7    | 7    | 7    | 7    | 7    | 7    | 7    | 7    |
| {1,2,3}     | 0    | 1    | 6    | 7    | 7    | 18   | 19   | 24   | 25   | 25   | 25   | 25   |
| {1,2,3,4}   | 0    | 1    | 6    | 7    | 7    | 18   | 22   | 24   | 28   | 29   | 29   | 40   |
| {1,2,3,4,5} | 0    | 1    | 6    | 7    | 7    | 18   | 22   | 28   | 29   | 34   | 35   | 40   |

如何找解：从最后一个解网上回推。每次和j-1，w的状态比较，如果比上面大，说明第j个物品放进去了。

最后结果：放入了3,4.

例子4：

**Problem.** Given *n* coin denominations { *c*1, *c*2, …, *c**n* } and a target value *V*, find the fewest coins needed to make change for *V* (or report impossible).

 **Recall.** Greedy cashier’s algorithm is optimal for U.S. coin denominations,
 but not for arbitrary coin denominations.

![image-20241218082024331](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241218082024331.png)


 Ex. { 1, 10, 21, 34, 70, 100, 350, 1295, 1500 }.

Optimal. 140¢ = 70 + 70.

opt(i,v)=opt(i-1,v),1+opt(i,v-c_i)

V=11，c={1,2,5}

|       | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 空    | 无穷 | 无穷 | 无穷 | ..   |      |      |      |      |      |      |      |      |
| 1     | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   |
| 1,2   | 0    | 1    | 1    | 2    | 2    | 3    | 3    | 4    | 4    | 5    | 5    | 6    |
| 1,2,5 | 0    | 1    | 1    | 2    | 2    | 1    | 2    | 2    | 3    | 3    | 2    | 3    |

 

Maximum subarrary problem



**Quiz：如果是求组合数一定是要所有可能备选之和。**

>  if dp\[i][j]==dp\[i-1][j]，cnt\[i][j]=cnt\[i-1][j]单独加个判断，如果符合条件，加上这种情况的组合数。
>
> cnt\[i][j]+=cnt\[i-1][j-weight[i]]注意在这里是+=

**如果每个背包的重量可以是任意正数，那么算法复杂度不是nW，而是无穷大。因为我们遍历背包容量的时候要考虑小数。必须是正整数。**



## 例子4 Least Square

一方面用多条线最小化误差，另一方面，线段不能太多(过拟合)。

minimize cost:f(x)=E+cL

E:sum of the sums of the squared errors in each segment.

L: number of lines.

OPT(j)=minimum cost for point p_1,...p_j

e_ij=SSE for points p_i,p_i+1,...p_j

cost=E_L1+E_L2+c多加了一条线

cost=E_L1+E_L2+E_L3+2c多加两条线

cost取min（e_ij+c+OPT（j-1））（取所有1<i<j）







作业：要找s点到t点，k条边的最短路径（可能会有大于k条边的，但是我们只要k条边）

- 利用动态规划：

  ```cpp
  初始化第0层就是原图的到source的距离
  for(v=1,...,t)
  	for(i=1,...,k)
  		for(u)//u是上一层能与vlia所有点
  			dp[v][i]=min(dp[u][i-1]+w(u,v),dp[v][i]);
  final:dp[t][k]		
  ```

  想象构造一个k层的图（虽然实际操作时候每一次只会构造两层，每一层的状态只依赖于上一层。）

# P&NP

**NP 完全问题的特性**

- **难解性**：目前没有已知的多项式时间算法可以解决 NP 完全问题。

- **归约性**：如果一个问题是 NPC的，那么它可以通过多项式时间归约（polynomial-time reduction）转化为另一个 NPC问题。这意味着如果一个 NPC问题被解决了，那么其他所有 NPC问题也可以被解决。

-  **如何证明一个问题是 NP 完全问题**

  证明一个问题是 NP 完全问题通常需要以下步骤：

  1. **证明问题属于 NP 类**：
     - 说明可以在多项式时间内验证一个给定的解是否正确。
  2. **证明问题是 NP 中最难的**：
     - 通过多项式时间归约，将一个已知的 NPC归约到当前问题。

## Reduction:

Problem *X* polynomial-time (Cook) reduces to problem *Y* if arbitrary instances of problem *X* can be solved using:

- Polynomial number of standard computational steps, plus

- Polynomial number of calls to oracle that solves problem *Y*.



**Design algorithms.** If *X* ≤ P *Y* and *Y* can be solved in polynomial time,
 then *X* can be solved in polynomial time.


 **Establish intractability.** If *X* ≤ P *Y* and *X* cannot be solved in polynomial time, then Y cannot be solved in polynomial time.

 **Establish equivalence.** If both *X* ≤ P *Y* and *Y* ≤ P *X*, we use notation *X* ≡ P *Y*.
 In this case, *X* can be solved in polynomial time iff *Y* can be.



**例子解释：**

Independent-Set. Given a graph *G* = (*V*, *E*) and an integer *k*, is there
 a subset of *k* (or more) vertices such that no two are adjacent?

- independent set

![image-20241220084911991](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241220084911991.png)



- vertex cover

![image-20241220085306600](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241220085306600.png)

子集里的点能囊括所有边。

要找到size为k的independent set和size为n-k的cover是互相规约的。

**Theorem. I**ndependent-Set ≡ P Vertex-Cover.

Pf. We show *S* is an independent set of size *k* iff *V* − *S* is a vertex cover of size *n* – *k*.

1. X<=pY

   - 对于边（u，v），那么要么u不属于S，要么v不属于S，要么都不在里面。
   - 那么要么u属于V-S，要么v属于V-S,要么都在V-S里面
   - V-S就是 a vertex cover of size *n* – *k* vertex cover of size *n* – *k*

   

2. Y<=pX

   - V-S is a vertex cover of size *n* – *k*
   - 对于边（u，v），要么u属于V-S，要么v属于V-S,要么都在V-S里面
   - 那么要么u不属于S，要么v不属于S，要么都不在里面。
   - S is independent set of size k.





**Decision problem**. Does there exist a vertex cover of size ≤ *k* ?

**Search problem**. Find a vertex cover of size ≤ *k*.

**Optimization problem**. Find a vertex cover of minimum size.


 Goal. Show that  **all three problems poly-time reduce to one another.**

​	1. Theorem. Vertex-Cover ≡ P Find-Vertex-Cover.


 Pf. ≤ P  Decision problem is a special case of search problem. ▪


 Pf. ≥ P 

To find a vertex cover of size ≤  *k* :用cook方法

・Determine if there exists a vertex cover of size ≤ *k*.

・Find a vertex *v* such that *G* − { *v* } has a vertex cover of size ≤ *k* − 1.
 (any vertex in any vertex cover of size ≤  *k* will have this property)

・Include *v* in the vertex cover.

・Recursively find a vertex cover of size ≤  *k* − 1 in *G* − { *v* }. ▪

​	2. Theorem. Find-Vertex-Cover ≡ P Find-Min-Vertex-Cover.


 Pf. ≤ P  Search problem is a special case of optimization problem. ▪


 Pf. ≥ P   To find vertex cover of minimum size:

・Binary search (or linear search) for size *k** of min vertex cover.

・Solve search problem for given *k**. ▪





- Set cover

k个U的子集的并集是U

vertex cover<=p set cover

U=E

・Include one subset for each node *v* ∈ *V* : *S**v* = {*e* ∈ *E* : *e* incident to *v* }.

![image-20241220093952032](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241220093952032.png)

每一个子集对应vertex cover里面的每一个点。

pf steps：

1. define the problem
2.   design the reduction
3. prove the correctness of the reduction
   - yes-instance(A有解 B有解)
   - no-instance（A无解，B无解/B有解，A有解）
4. time complexity 

Suppose the vertex cover Pb has a solution V包含于G of size k, that cover all edges in E.

In the Set cover instance, the sub set collection S={S_v|v in V} will cover all edges in E=U

Then if Vertex-Cover has solution, Set-cover has Solution       



## Satisfiablility

1. 



![image-20241225083746159](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241225083746159.png)

3-SAT指Clause里面有三个literal

对于含n个literal的SAT，要确定true，false，需要2^n.

 2. 3-satisfiability reduces to independent set

    **Theorem.** 3-Sat ≤ P Independent-Set.

    **Pf.** Given an instance Φ of 3-Sat, we construct an instance (*G*, *k*) of Independent-Set that has an independent set of size *k* = ⎜Φ⎜ iff Φ is satisfiable.

    **Construction.**Encoding with gadgets

    ・*G* contains 3 nodes for each clause, one for each literal.

    ・Connect 3 literals in a clause in a triangle.

    ・Connect literal to each of its negations.

    找三个顶点代表三个clause，每个clause用一个点表示（因为是并集）只要这三个顶点都是真的那么SAT就是真的。

    ![image-20241225085629326](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241225085629326.png)

3-SAT是个NP问题

- 3-colorability

  ![image-20241225104610753](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241225104610753.png)

  A

**Lemma.** Graph *G* is 3-colorable iff Φ is satisfiable.

Pf. Þ Suppose graph *G* is 3-colorable.

・WLOG, assume that node *T* is colored *black*, *F* is *white*, and *B* is *blue*.

・Consider assignment that sets all *black* literals to *true* (and *white* to *false*).

・(iv) ensures each literal is colored either *black* or *white*.

・(ii) ensures that each literal is *white* if its negation is *black* (and vice versa).

・(v) ensures at least one literal in each clause is *black*. ▪

![image-20241225094250042](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241225094250042.png)

Pf. Ü  Suppose 3-Sat instance Φ is satisfiable.

・Color all *true* literals *black* and all *false* literals *white*.

・Pick one *true* literal; color node below that node *white*,
 and node below that *blue*.

・Color remaining middle row nodes *blue*.

・Color remaining bottom nodes *black* or *white*, as forced. ▪

![image-20241225094411102](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241225094411102.png)

## P

![](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241225094641435.png)

![image-20241227082749771](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241227082749771.png)

decision=psearch=pOtimism三者都是等价.

P属于NP。

P不等于NP。



## NP

不知道是不是P类问题的问题

![image-20241225095339413](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241225095339413.png)

![image-20241227082636341](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241227082636341.png)

也是decision problem，知道一个解t之后可以在多项式时间内判断多项式是否为真。

如果你找不到一个t最后C(s,t)为yes，那么就不是严格的NP。



- **多项式时间验证器 f\*f\***：一个可以在多项式时间内运行的算法，用于验证一个给定的解是否正确。    

- 例子：3—SAT：

  ![image-20241227083828251](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241227083828251.png)

![image-20241227084547911](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241227084547911.png)

E

t:可以随便找一条path=k

longest可能会比k大，这个时候B肯定certificate=yes。





**Polynomial transformations**

Def.  Problem *X* polynomial **(Cook)** reduces to problem *Y* if arbitrary instances of problem *X* can be solved using:

・Polynomial number of standard computational steps, plus

・Polynomial number of calls to oracle that solves problem *Y*.多项式时间调用解决Y文体的实例。

用于证明decision=psearch

Pf. ≤ P  Decision problem is a special case of search problem. ▪
 Pf. ≥ P 

To find a vertex cover of size ≤  *k* :

・Determine if there exists a vertex cover of size ≤ *k*.

・Find a vertex *v* such that *G* − { *v* } has a vertex cover of size ≤ *k* − 1.
 (any vertex in any vertex cover of size ≤  *k* will have this property)

・Include *v* in the vertex cover.

・Recursively find a vertex cover of size ≤  *k* − 1 in *G* − { *v* }. ▪






 Def. Problem *X* polynomial **(Karp)** transforms to problem *Y* if given(所有的) any instance *x* of *X*, we can construct an instance *y* of *Y* such that *x* is a *yes* instance of *X* iff *y* is a *yes* instance of *Y*. 这也叫many to one reduction.

这个很常见，



## EXP

Decision problems for which there exists an exponential-time algorithm.

Proposition. **P** ⊆ **NP**.

Proposition. **NP** ⊆ **EXP**.

Fact. **P** ≠ **EXP** ⇒ either **P** ≠ **NP**, or **NP** ≠ **EXP**, or both.

## NPC

**NP-complete**：A problem *Y* ∈ **NP** with the property that for every
 problem *X* ∈ **NP**, *X* ≤ P *Y*.

**Proposition.** Suppose *Y* ∈ **NP**-complete. Then, *Y* ∈ **P** iff **P = NP**.

也就是说如果能解决Y，那么所有NP类问题都能被解决。那么如果Y是P类问题，那么P=NP

NPC问题实例：

circuit

证明一个



NPC问题：

需要让一个NPC问题X让它规约到Y。

![image-20241227094029070](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241227094029070.png)

  例子：3—SAT

1. 它是NP
2. 它可以由circuit-SAT规约来。（证明谁是NPC就由它规约）

![img](https://i-blog.csdnimg.cn/blog_migrate/1258acf3cd56a7e27052ac2e5249c44b.png)
