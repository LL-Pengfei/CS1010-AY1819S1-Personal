# Assignment 5: Comments

## Notes on Marking Schemes

- We apply the penalty to each program independently, i.e., the same mistake repeated in the different program get penalized multiple times.  
- You will receive 0 marks if your program cannot be compiled or is plagiarised from another.

- Code that fails all test cases and showing that student does not understand the basic concepts receives 0 marks straight away.

- For the rest, we start with full marks and start deducting marks for each error.

- While the teaching team tries to ensure fair and consistent grading of assignments across all groups, each TA can, at his/her own discretion, and with the approval of Wei Tsang, adjust the grading criteria to align with the messages that they sent to the students in their tutorial group.

## Style

No style marks are allocated for each question, but if you code is hard to understand, not properly indented, use non-descriptive variable names, etc, we will deduct up to 2 marks. 

To be safe, please follow strictly the CS1010 coding standards.

## Documentation

Two marks are allocated for each question. We do not penalize for formatting errors violation in Assignment 5.  If you did not document your function, parameters, return values, or document wrongly (e.g., confused between `@param[out]` with `@return`, you will get a deduction of 1 mark.  You get 0 if there is no documentation at all.  

The following are common formatting errors:

- `@param` command used in a comment that is not attached to a function declaration 
- `@return` command used in a comment that is attached to a function returning `void` 
- Parameter name not found in the function declaration.  You should put the name of the variable only, exclude the type, `*`, `[]`.
- Putting `@param` with the parameter name, but no description of what the parameter does.
- Missing `[in]`, `[out]`, or `[in,out]`.  Note that there is no space after `,` in `[in,out]`. 

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
- -1 for using variable length array
- -1 for extra elements in the array initializers
- -1 for hardcoding the size of the array, hoping that it is big enough.
- -1 for memory leaks

## Logical Mistakes (bugs)

In general, -1 for each bug.

- -1 for each incorrect logical expression
- -1 for each incorrect arithmetic expression
- -1 for each incorrect use of `if`-`else`
- -1 for each uninitialized variables causing incorrect output.
- -1 if program prints extra text / newline
- -1 if the program returns non-zero from `main` even if the program exits successfully.

## Assignment 5: Memory Errors

Memory leaks are memory errors are especially serious in Assignment 5.  About 70% of you have at least one memory-related bugs.  

A common error is to allocate a 2D array like this:

```C
char **matrix = calloc(m, sizeof(char *));
for (i = 0; i < m; i += 1) { 
  matrix[i] = calloc(n, sizeof(char));
  matrix[i] = cs1010_read_word();
}
```

Note that here you are reassigning `matrix[i]` that originally points to the memory region returned by `calloc` to the memory returned by `cs1010_read_word`.  The memory returned by `calloc` is now unreachable and cannot be free.

After using `matrix`, you should free them 

```C
for (i = 0; i < m; i += 1) { 
  free(matrix[i]);
}
free(matrix);
```

Another common memory error is the failure to consider the terminating null character in allocation of string.  If your string is of size $n$, then you need to allocate $n+1$ characters for the string.

## Answer Keys

### Social

This is not meant to be a hard question.  There is a similar question (where $k$ is infinity and the array is not jagged) appeared in the final exam of CS1010 Semester 2 AY17/18.  Unexpectedly, many students struggled with it.  

There are several issues with most submissions I read.  First, many students did not separate out the representation (using jagged array) from the operation (checking if two person is a friend).  This is despite a similar question is asked in Problem 19.2.

If you do not write a function similar to `dist` from Problem 19.2, to check if two person is a friend, then the complexity of your solution increases sigificantly, as you have to deal with looping through the right column or the right row.  Many bugs occur due to students forgetting to consider scanning through a row or a column.  It is easier if you write a function that looks like the following:


```C
bool is_friend(char **network, int i, int j) {
  if (i >= j) {
    return network[i][j] == FRIEND;
  }
  return network[j][i] == FRIEND;
}
```

Then you no longer have to worry about whether to traverse by row or by column!

With the issue of how to access a jagged array out of the way, let's focus on how to find the $k$-hop friendships among the people.  

Some of you tried to employ complicated methods (using recursion and advanced CS2040/C concepts such as breadth-first-search or depth-first-search) to find the $k$-hop friendships directly.  If you managed to do it, then bravo, you have learned something beyond CS1010 and are one step ahead of your peers!  But I actually did not expect students to do this.  My approach, as usual, is to break down the problem into smaller ones that we can solve.  So let's try to find a 2-hop friendship first.  How can we build the social network of 2-hop friendship?  If two person $i$ and $j$ are not yet connected, we find a third person $m$ who is a mutual friend between the two of them.  If we can find such a third person $m$, then $i$ and $j$ are 2-hop friend.  We can do something like this:

```C
char is_common_friend(long n, char **degree_one, long i, long j) {
  for (int m = 0; m < n; m += 1) {
    if ((is_friend(degree_one, i, m) && is_friend(degree_one, m, j))) {
      return FRIEND;
    }
  }
  return STRANGER;
}
```

(I have #defined `FRIEND` to be `'1'` and `STRANGER` to be `'0'`)

Now we can just loop through every pair $i$ and $j$ 
```C
void compute_degree_2(long n, char **degree_one, char **degree_two) {
  for (int i = 0; i < n; i += 1) {
    for (int j = 0; j <= i; j += 1) {
      if (is_friend(degree_one, i, j)) {
        degree_two[i][j] = FRIEND;
      } else {
        degree_two[i][j] = is_common_friend(n, degree_one, i, j);
      }
    }
  }
}
```

There is an important poitn in the code above, we need to store the result network in a new 2D array (I called it `degree_two`) instead of updating the network in place:

```C
  // not like this
  degree_one[i][j] = is_common_friend(n, degree_one, i, j);
```

Some students stored the results back into the original 2D array, and as a result get the wrong output.  This is the same for the next question `life` -- but it is easier to visualize and see the bug in `life`.

And now we have constructed the social network of two hops.  How do we construct a network of three hops?  Well, a 3-hop relationship means that there must be someone $m$, who is a 1-hop friend of $i$ and 2-hop friend of $j$.  We can make this more general: a $h$-hop relationship means that there must be someone $m$ who is a 1-hop friend of $i$ and ($h-1$)-hop friend of $j$.

We can thus modify `compute_degree_2` to `compute_degree_h`, making the code more general.

```C
/**
 * Checks if i and j has a common friend
 * @param[in] n The number of users
 * @param[in] degree_one The 1-hop friendship information
 * @param[in] degree_h The h-hop friendship information
 * @param[in] i A user
 * @param[in] j Another user
 * @return FRIEND if i and j has a (h+1)-hop connection, STRANGER otherwise.
 */
char is_common_friend(long n, char **degree_one, char **degree_h_minus_1, long i, long j) {
  for (int m = 0; m < n; m += 1) {
    if ((is_friend(degree_one, i, m) && is_friend(degree_h_minus_1, m, j))) {
      return FRIEND;
    }
  }
  return STRANGER;
}

/**
 * Computers the h-hop friendship for the whole network.
 * @param[in] n          The number of users.
 * @param[in] degree_one The 1-hop friendship network
 * @param[in] degree_h_minus_1   The (h-1)-hop friendship network
 * @param[out] degree_h   The h-hop friendship network
 */
void compute_degree_h(long n, char **degree_one, char **degree_h_minus_1, char **degree_h) {
  for (int i = 0; i < n; i += 1) {
    for (int j = 0; j <= i; j += 1) {
      if (is_friend(degree_h_minus_1, i, j)) {
        degree_h[i][j] = FRIEND;
      } else {
        degree_h[i][j] = is_common_friend(n, degree_one, degree_h_minus_1, i, j);
      }
    }
  }
}
```

Note that we need three 2D arrays now, one storing ($h-1$)-hop friendship, one storing 1-hop friendship, and one more to store the newly computed $h$-hop friendship.  Once we finish computed the $h$-hop friendship, we do not need the array storing $(h-1)$-hop friendship anymore and we can deallocate it (or better still, reuse it for $h+1$-hop).

Finally, we are ready to calculate the $k$-hop friendship:

```C
for (int h = 1; h < k; h += 1) {
  compute_degree_h(n, degree_one, degree_h_minus_1, degree_h);
  swap(&degree_h_minus_1, &degree_h);
}
```

The call to `swap` is how we put the results of $h$-hop friendship into the array `degree_h_minus_1`, getting ready to be read for the next loop, and get the 2D array `degree_h` ready to store new results for the next loop.

### Life

Most students do not have problem solving `life`.   The most common bugs are memory-related, which we have explained above.  The gist of the solution to `life` falls on the three functions below:

```C
/**
 * Count the number of neighbors that is alive.
 *
 * @param[in] universe The game of life universe
 * @param[in] i The row coordinate of the current cell whose
 *              neighbors we are counting.
 * @param[in] j The column coordinate of the current cell whose
 *              neighbors we are counting.
 * @return The number of live neighbors.
 */
int count_live_neighbor(char **universe, long i, long j) {
  long count = 0;
  for (long x = -1; x <= 1; x += 1) {
    for (long y = -1; y <= 1; y += 1) {
      if (x != 0 || y != 0) {
        if (is_alive(universe[i+x][j+y])) {
          count += 1;
        }
      }
    }
  }
  return count;
}

/**
 * Update the cell (i, j) in the universe.
 *
 * @param[in] universe The game of life universe
 * @param[in] i The row coordinate of the current cell we are updating
 * @param[in] j The column coordinate of the current cell we are updating
 * @return The new status of the cell (DEAD or LIVE)
 */
char update_cell(char **universe, long i, long j) {
  int live_neighbor_count = count_live_neighbor(universe, i, j);
  if (is_alive(universe[i][j])) {
    if (live_neighbor_count < 2 || live_neighbor_count > 3) {
      return DEAD;
    }
  } else {
    if (live_neighbor_count == 3) {
      return LIVE;
    }
  }
  return universe[i][j];
}

/**
 * Simulate one step of the game of life.
 *
 * @param[in] nrows The number of rows
 * @param[in] ncols The number of columns
 * @param[in] universe The curr game of life universe
 * @param[out] new_universe The updated game of life universe
 */
void simulate_game(long nrows, long ncols, char **universe, char **next_universe) {
  for (long i = 1; i < nrows-1; i += 1) {
    for (long j = 1; j < ncols-1; j += 1) {
      next_universe[i][j] = update_cell(universe, i, j);
    }
  }
}
```
