
# Assignment 3: Comments

## Mark Distribution

Output from `histogram`.  Marks are scaled up by 3x.

```
          ┴┴┴┴┴┴┴┴┴┴
  0 - 10  ▌
 10 - 20  ▌
 20 - 30
 30 - 40  ▌
 40 - 50  ▌
 50 - 60  ▌
 60 - 70  █
 70 - 80  ████
 80 - 90  ███
 90 - 100 ██▌
```

## Notes on Marking Schemes

- We apply the penalty to each program independently, i.e., the same mistake repeated in the different program get penalized multiple times.  
- You will receive 0 marks if your program cannot be compiled or is plagiarised from another.

- Code that fails all test cases and showing that student does not understand the basic concepts receives 0 marks straight away.

- For the rest, we start with full marks and start deducting marks for each error.

- While the teaching team tries to ensure fair and consistent grading of assignments across all groups, each TA can, at his/her own discretion, and with the approval of Wei Tsang, adjust the grading criteria to align with the messages that they sent to the students in their tutorial group.

## Style

Two marks are allocated for each question. We won't penalize for every violation, but instead, the grader judges whether the code is clean, understandable, properly indented, use proper variable names, etc. 0 - 0.5 marks for very bad style, 1.0 - 1.5 for something in the middle, 2 for an ok code. 

To be safe, please follow strictly the CS1010 coding standards.

## General Mistakes

This penalty applies once per program (not per occurrence).

- -1 for missing type in function declaration or definition
- -1 for using `int`/`long` instead of `bool` for boolean functions or variables
- -1 for using `double` instead of `long` when it is not necessary (does not involve integer division).  For instance, using `double` for `invest.c` during calculation is ok, using `double` for calculating the area of the box is not.
- -1 for using of `int` instead of `long`.  Using `int` for this assignment is ok only for `day`, `hour`, and `minute`, where there is an implicit constraint on the range of the value.
- -1 for failing to use parenthesis (e.g., `a && b || c`)
- -1 for unnecessary/unused parameters passed into a function
- -1 for unnecessary/unused variables declared
- -1 for not including @author/@group
- -1 for global variables
- -1 for unreachable code / return
- -1 for using `==` to compare real numbers

## Logical Mistakes (bugs)

In general, -1 for each bug.

- -1 for each incorrect logical expression
- -1 for each incorrect arithmetic expression
- -1 for each incorrect use of `if`-`else`
- -1 for each uninitialized variables causing incorrect output.
- -1 if program prints extra text / newline
- -1 if the program returns non-zero from `main` even if the program exits successfully.

Note: we didn't deduct marks for using VLA for this assignment, as you have not learned about `malloc`/`calloc` yet. 

## Answer Keys

### Kendall

We need to count the total number of inversions in a given array.  I break this problem into two:

1. Given an element $a_i$, how many elements are out of the order with respect to this element?   

2. We then sum up the total number of elements out of order, for each $a_i$.  

This question should be quite straightforward, except that we need to consider the following:

- if the pair $a_i$ and $a_j$ are out of order, then we can count this pair as _one_ inversion only.  We need to fix a convention on how we account for inversion. I use convention that we count the inversion if $i < j$ and $a_i > $a_j$.  In other words, for the subproblem 1 above, we only need to check for inversions for each element $a_j$, if $j > i$.

- $a_i$ shouldn't be the last element, since there is no $j$ that is bigger than $i$.

Slightly more 4/5 of the class get this question correct, with full marks.  Well done!

(The snippet for this program can be found in [Unit 22](22-efficiency.md) on efficiency).

### Histogram

To solve this problem, we should break the problem into two steps.  First, we go through the data and count the number of items in each basket.  This can be done with the following snippet.

```C
#define NUM_OF_BUCKETS 10
#define MAX_SCORE 100
 :
/**
 * Populate the buckets of the histogram based on the input array marks, containing
 * num_of_items items.
 *
 * @param[in] histogram Counters to store how many items fall into each bucket.
 * @param[in] num_of_items The number of input marks to sort into the buckets.
 * @param[in] marks An array of marks to be sorted into the buckets.
 */
void populate_histogram(long histogram[NUM_OF_BUCKETS], int num_of_items, const double marks[num_of_items])
{
  double interval = MAX_SCORE/(NUM_OF_BUCKETS*1.0);
  for (long i = 0; i < num_of_items; i += 1) {
    long bucket = marks[i]/interval;
    if (bucket == NUM_OF_BUCKETS) {
      bucket -= 1;
    }

    histogram[bucket] += 1;
  }
}
```

The only tricky part of this code is what happen if `marks[i]` is 100, in which case, `marks[i]/interval` would lead to `bucket` being `10`.  Since the indices to the buckets are `histogram[0]`,..., `histogram[9]` only, we would be writing into memory location that we do not own if we increment `histogram[10]`.  For this question, we were told that 100 belongs to $b_9$.  So, we need to decrement the variable `bucket` by 1.

Second, after populating the buckets, we need to draw out the histogram:

```C
#define BAR_LENGTH 10.0
   :
  double height[NUM_OF_BUCKETS];
  for (long i = 0; i < NUM_OF_BUCKETS; i += 1) {
    cs1010_print_string(axis_labels[i]);

    height[i] = BAR_LENGTH*histogram[i]/num_of_items;

    // Print full blocks
	long num_of_cells = floor(height[i]);
    for (long j = 0; j < num_of_cells; j += 1) {
      cs1010_print_string(BLOCK);
    }

    // Print final half-block, block or no block if height is integer
    double delta = height[i] - floor(height[i]);

    if (delta > 0.5) {
      cs1010_print_string(BLOCK);
    } else if (delta > 0) {
      cs1010_print_string(HALF_BLOCK);
    }

    cs1010_println_string("");
  }
}
```

The purpose of this question to see how arrays can be used as a list (the input marks) and a lookup table (for histogram frequency, height, and y-axis).

This should be quite straight forward.  At least 4/5 of the class received 9 marks or above for this.  Well done!  

There are some students, however, who tried to compare `double` variable using `==` operator, and got deducted one mark for it.  Revisit [Unit 08](https://nus-cs1010.github.io/1819-s1/08-if-else/index.html#comparing-real-numbers) if you have doubts about why we should never compare `double` variable using `==`.

Another common mistake that students make, at least at the beginning of doing this assignment, is to use `cs1010_read_long()` to read a `double`.  The function `cs1010_read_long()` gives a warning if it is used to read a `double` value.  This is one of the advantages of using the CS1010 library to read instead of the `scanf` functions.  `scanf` would just silently failed and you would be left scratching your head why your code does not work!

### Counting Sort

This is the assignment question that most students are having trouble with.

Students who do not follow the instructions and use any other sorting algorithm (e.g., bubble sort) will receive 0 for correctness.

Students who just loops through the array and prints out all the numbers, without counting, will get at most 2 marks for correctness.  The question clearly asked you to count.

```C
// A 2 mark solution
for (int i = 1; i <= k; i += 1) {
  for (int j = 0; j < n; j += 1) {
	if (a[j] == i) {
	  cs1010_println_long(i);
	}
  }
}
```

Students who just loops through the array and count, but goes through it multiple times to store the frequency of each number, will get at most 4 marks for correctness.  Here, you are not exploiting array properly to help you solve the problem!

```C
// A 4 mark solution
long counter[k+1] = {0};

for (int i = 1; i <= k; i += 1) {
  for (int j = 0; j < n; j += 1) {
	if (a[j] == i) {
	  counter[i] += 1;
	}
  }
}

for (int i = 1; i <= k; i += 1) {
  for (int j = 0; j < counter[i]; j += 1) {
    cs1010_println_long(i);
  }
}
```

In the code above, the `counter` array is useless, since we could have just use a simple counter variable (instead of an array) to achieve the same thing!

```C
// A 4 mark solution
for (int i = 1; i <= k; i += 1) {
  long counter = 0;
  for (int j = 0; j < n; j += 1) {
	if (a[j] == i) {
	  counter += 1;
	}
  }
  for (int j = 0; j < counter; j += 1) {
    cs1010_println_long(i);
  }
}
```

What we are looking for, and the "mental leap" that we want to see in the students, is how we can use array as a lookup table to store the frequency counters.

```C
// An 8 mark solution
long counter[k+1] = {0};

for (int j = 0; j < n; j += 1) {
  counter[a[j]] += 1;
}

for (int i = 1; i <= k; i += 1) {
  for (int j = 0; j < counter[i]; j += 1) {
    cs1010_println_long(i);
  }
}
```

Only about half the class get this.  But hopefully, after this assignment, everyone gets how powerful a lookup table is as a problem solving tool.

A final comment about the loop:

```C
for (int j = 0; j < n; j += 1) {
  counter[a[j]] += 1;
}
```

We should not actually write code that looks like this, unless we can be sure that `a[j]` is within range of 1 and $k$.  There is a reason that we ask you to validate the input for this question, but not for any other questions.  Because, for this question, if you do not validate the input, it is very easy to access a location that is out-of-bound of your array and crash your program.

```C
for (int j = 0; j < n; j += 1) {
  if (a[j] >= 1 && a[j] <= k) {
	counter[a[j]] += 1;
  }
}
```
