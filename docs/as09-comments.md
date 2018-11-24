# Assignment 9: Comments

## Answer Keys

### Digits

This assignment involves many concepts that you have learnt over the semester.

### Constants

There are a few constants involved in this assignment.  So let's define that first.

```C
#define NCOLS 28
#define NROWS 28
#define WHITE '.'
#define BLACK '#'
#define K 5
```

### Representing A Digit

We should put all the information that are logically related to a digit together in a structure.  Let's start with the most straightforward one:

```C
typedef struct {
  char *input[NROWS];
  long label;
} digit;
```

We will change it later.  Here is an example of how we use this `struct`:

```C
void digit_read(digit *sample)
{
  sample->label = cs1010_read_long();
  for (long i = 0; i < NROWS; i += 1) {
    sample->input[i] = cs1010_read_word();
  }
}
```

The above read a sample digit (either training of testing) from the standard input.

The following is how to find the distance between two digits:

```C
long find_distance(digit a, digit b) {
  long d = 0;
  for (long i = 0; i < NROWS; i += 1) {
    for (long j = 0; j < NCOLS; j += 1) {
      if ((a.input[i][j] == BLACK && b.input[i][j] == WHITE) ||
          (a.input[i][j] == WHITE && b.input[i][j] == BLACK)) {
        d += 1;
      }
    }
  }
  return d;
}
```

### Finding K Nearest Neighbors

After we find the distance from the testing sample (the digit we want to recognize) and one of the training samples, we need to check if it is among the $k$ nearest neighbors.  This subproblem can be
formulated as the following: given an input of $n$ numbers, find the $k$ smallest.  This is a generalization of the midterm problem (find the second largest number from a list).  

One possible solution is to sort the $n$ numbers and get the first $k$.  This takes $O(n^2)$ time using the sorting algorithms that you have learned or $O(n \log n)$ if you use quick sort or merge sort (from CS2040C).  We can't use counting sort even though the maximum among the numbers to be sorted is only $28^2$, because it is not enough to sort the distances, we need to keep track of the training samples (the `digit` variables) associated with the distances as well.

To solve this in $O(kn)$ time, one way is to keep the closest $k$ neighbors so far in a sorted array of size $k$, and perform insertion into this array, similar to the procedure of insertion sort. 

We would need to keep the corresponding distance values as well.  Let's create a new `struct` to store the neigbours:

```C
typedef struct {
  digit sample;
  long distance;
} neighbor;
```

and declare an array of `K` neighbors with the distance initialized to infinity:
```C
  neighbor nearest[K];
  for (long i = 0; i < K; i += 1) {
    nearest[i].distance = LONG_MAX;
  }
```

Now, given a test sample `test`, a list of training samples `trains`, we can do the following:
```C
  for (long i = 0; i < num_of_samples; i += 1) {
    long d = find_distance(test, trains[i]);
    insert(trains[i], d, nearest);
  }
```

Where the function `insert` is:

```C
void insert(digit sample, long distance, neighbor nearest[]) {
  if (distance > nearest[K-1].distance) {
    return;
  }
  if (distance == nearest[K-1].distance && 
      sample.label > nearest[K-1].sample.label) {
    return;
  }
  long i = K-1;
  do {
    nearest[i] = nearest[i-1];
    i -= 1;
  } while (i >= 0 && (nearest[i].distance > distance || 
      (nearest[i].distance == distance && 
      nearest[i].sample.label > sample.label)));

  // { nearest[i].distance <= sample.distance }
  nearest[i+1].sample = sample;
  nearest[i+1].distance = distance;
}
```

The `insert` function takes $O(k)$ times.

After we identify the $k$ nearest neighbors for a test sample, the final step is to find the most common label that appears in these $k$ neighbors.  We can use something similar to counting sort to count the number of occurances of each digit, and return the most frequent one.  This takes $O(k)$ times.

```C
long most_common_digit(const neighbor nearest[K])
{
  long freq[10] = {0};
  for (long i = 0; i < K; i += 1) {
    freq[nearest[i].sample.label] += 1;
  }
  long max_so_far = freq[0];
  long max_digit = 0;
  for (long i = 1; i < 10; i += 1) {
    if (freq[i] > max_so_far) {
      max_so_far = freq[i];
      max_digit = i;
    }
  }
  return max_digit;
}
```

### Avoiding Redundant Work

We can further improve the code by pruning unnecessary computation.  Observe that, any training sample with distance larger than `nearest[k-1]` will have no chance of "making it" into the $k$ nearest neighbors.  We can thus prune these computations and speed up `find_distance`.  The modified `find_distance` method below has a new `limit` parameter.  Once `d` exceeds that, the function returns `d`.  It won't be returning the true distance between the digits `a` and `b`, but it will not matter anyway.

```C
long find_distance(digit a, digit b, long limit) {
  long d = 0;
  for (long i = 0; i < NROWS; i += 1) {
    for (long j = 0; j < NCOLS; j += 1) {
      if ((a.input[i][j] == BLACK && b.input[i][j] == WHITE) ||
          (a.input[i][j] == WHITE && b.input[i][j] == BLACK)) {
        d += 1;
        if (d > limit) {
          return d;
        }
      }
    }
  }
  return d;
}
```

The new `find_distance` is called as follows:

```C
  for (long i = 0; i < num_of_samples; i += 1) {
    long d = find_distance(test, trains[i], nearest[k-1].distance);
    insert(trains[i], d, nearest);
  }
```

This simple act of pruning reduces the actual running time by 60% for my case (your milage may vary).
