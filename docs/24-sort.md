# Unit 24: Sorting

Sorting is one of the most fundamental computational problem.  Given a list of items, we want to rearrange the items in some order.

We have actually seen two algorithms to sort in this module: counting sort and selection sort.  We are going to see two more today.

## Bubble Sort

Bubble sort is probably the most well known, under-performed sorting algorithm[^1], but is taught in most CS classes because of its simplicity.  The idea of bubble sort is to make multiple passes through the list.  In each pass, we look for all possible adjacent pair of items.  Any adjacent pair that is out of order are swapped so that they are in order.  This process repeats until everything is in order.

Let's look at an example.  Suppose we have, as an input, the numbers `8 4 23 42 16 15`.  In the first pass, we starts from the first item and check from left to right.  The pair `8 4` is out of order, so we swap them, and we get `4 8 23 42 16 15`.  The pair `8 23` and `23 42` are in order, so we do not need to swap them.  The pair `42 16` is out of order.  We swap them and get `4 8 23 16 42 15`.  The pair `42 15` is again out of order, so we swap them and get `4 8 23 16 15 42`.

The following sequence show the first pass through the array:

```
 8  4 23 42 16 15   <- swap
-- --
 4  8 23 42 16 15  
   -- -- 
 4  8 23 42 16 15  
      -- --
 4  8 23 42 16 15   <- swap
         -- --
 4  8 23 16 42 15   <- swap
            -- --
 4  8 23 16 15 42  
```

After the first pass, notice that the largest element, 42, "bubbles" up through the list until it reaches the maximum position.  We can now make the second pass, but we can exclude the last item, since it is already in place.

```
 4  8 23 16 15 42  
-- --
 4  8 23 16 15 42  
   -- -- 
 4  8 23 16 15 42   <- swap
      -- --
 4  8 16 23 15 42   <- swap
         -- --
 4  8 16 15 23 42   
         -- --
```

After the second pass, the second largest element, 23, is in its position.  So we can exclude this item in the subsequent pass.

The rest of the passes operates similarly.  In the $i$-th pass, we scan through array item 0 to $n-i$, swapping any adjacent element that is out of order, until $i = n - 1$, in which case we only have two elements, we swap them if we are out of order, and we are done!

The code for bubble sort can be written as follows:

```C
void bubble_pass(long last, long a[]) 
{
  for (long i = 0; i < last; i += 1) {
    if (a[i] > a[i+1]) {
      swap(a, i, i+1);
    }
  }
}

void bubble_sort(long n, long a[n]) {
  for (long last = n - 1; last > 0; last -= 1) {
    bubble_pass(last, a);
  }
}
```

How many steps does it take to bubble-sort an array of $n$ elements?   Since the $i$-th pass scans through $n-i$ elements, and there are a $n$ passes in total, the analysis is similar to the one we did for the algorithm to compute the Kendall-tau distance -- bubble sort takes $O(n^2)$ steps.

## Insertion Sort

The next sorting algorithm we are going to discuss is insertion sort.  This is another classic algorithm, that could perform better than bubble sort in some scenario.  The idea of insertion sort is simple: we partition the input list into two, a sorted partition, and an unsorted partition.  Then we repeatedly take the first element from the unsorted partition, find its rightful place in the sorted partition, and _insert_ it into place.  We start with a sorted partition of one element, and we end if the sorted partition contains all the elements.

Take `8 4 23 42 16 15` as an example.  I will use `|` to partition the array into a left sorted partition, and a right, unsorted, partition.  

```
8 | 4 23 42 16 15 
```

We pick the first element on the unsorted partition, 4, and insert it into the sorted partition.  This involves shifting the elements in the sorted partition to the right until we find the rightful place for `4`.   After this step, the sorted partition grows by 1 and the unsorted partition shrinks by 1.

```
4 8 | 23 42 16 15 
```

In the next round, we take `23`, and finds its rightful place.  It turns out `23` is already in its correct place.  
```
4 8 23 | 42 16 15 
```

In the next step, `42` is also in its correct place.
```
4 8 23 42 | 16 15 
```

`16` is next element, and we insert it between 8 and 23.
```
4 8 16 23 42 | 15 
```

Finally we insert `15` and we are done, as there is no more element in the unsorted partition.
```
4 8 15 16 23 42
```

The code for insertion sort can be written as follows:

```C
void insert(long a[], long curr)
{
  long i = curr - 1;
  long temp = a[curr];
  while (temp < a[i] && i >= 0) {
    a[i+1] = a[i];
    i -= 1;
  }
  a[i+1] = temp;
}

void insertion_sort(long n, long a[n]) {
  for (long curr = 1; curr < n; curr += 1) {
    insert(a, curr);
  }
}
```

## Animation

Animations for various sorting algorithms, including some which you will learn in CS2040C, are available online on [VisuAlgo](https://visualgo.net/bn/sorting)


## Problem Set 24

### Problem 24.1

In the implementation of bubble sort above, we always make $n-1$ passes through the array.  It is, however, possible to stop the whole sorting procedure, when a pass through the array does not lead to any swapping.  Modify the code above to achieve this optimization.

[^1]: https://www.youtube.com/watch?v=k4RRi_ntQc8

### Problem 24.2

a) Suppose the input list to insertion sort is already sorted.  What is the running time of insertion sort?
b) Suppose the input list to insertion sort is inversely sorted.  What is the running time of insertion sort?

### Problem 24.3

What is the loop invariant for the loop in the function `insert`? 

## Appendix: Complete Code From Lecture

```C
#include <assert.h>
#include "cs1010.h"

void swap(long a[], int i, int j) {
  long temp = a[i];
  a[i] = a[j];
  a[j] = temp;
}

void print(long len, long a[len]) {
  for (long i = 0; i < len; i += 1) {
    cs1010_print_long(a[i]);
    cs1010_print_string(" ");
  }
  cs1010_println_string("");
}

void bubble_pass(long last, long a[]) {
  for (long i = 0; i < last - 1; i += 1) {
    if (a[i] > a[i+1]) {
      swap(a, i, i+1);
    }
  }
  print(6, a);
}

void bubble_sort(long len, long a[len]) {
  for (long last = len - 1; last > 0; last -= 1) {
    bubble_pass(last, a);
  }
}

int main()
{
  long a[] = {44, 4, 23, 42, 16, 15};
  bubble_sort(6, a);
  print(6, a);
}
```


```C
#include "cs1010.h"

void print(long len, long a[len]) {
  for (long i = 0; i < len; i += 1) {
    cs1010_print_long(a[i]);
    cs1010_print_string(" ");
  }
  cs1010_println_string("");
}

void insert(long a[], long curr) {
  // insert a[curr] into a[0]..a[curr-1] in the right position
  long temp = a[curr];
  long i = curr - 1;
  while (temp < a[i] && i >= 0) {
    a[i+1] = a[i];
    i -= 1;
  }
  // { temp >= a[i] || i < 0 }
  a[i+1] = temp;
}

void insertion_sort(long n, long a[n]) {
  for (long curr = 1; curr < n; curr += 1) {
    insert(a, curr);
    print(n, a);
  }
}

int main()
{
  long a[] = {44, 4, 23, 42, 16, 15};
  insertion_sort(6, a);
}
```
