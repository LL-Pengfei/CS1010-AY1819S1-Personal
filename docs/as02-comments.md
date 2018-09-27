# Assignment 2: Comments

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

## Misunderstanding about `if`-`else`

We noted that there are students who did not understand how `if`-`else` works.  For instance, there are students who write this:

```C
if (day == 0) {
    cs1010_println_string("Monday");
} 
if (day == 1) {
    cs1010_println_string("Tuesday");
} 
```

The above actually compares `day` with `0`, `1`, .. multiple times, even after a match is found, which is redundant.  Since `day` can take on only a single value.  The correct way is to use `else`

```C
if (day == 0) {
    cs1010_println_string("Monday");
} else if (day == 1) {
    cs1010_println_string("Tuesday");
} 
```

Another mistake we see is:
```C
if (dist > 0.7) {
    cs1010_println_string("+");
} else if (dist <= 0.7 && dist > 0.5) {
    cs1010_println_string("*");
} 
```

This demonstrates that students do not fully understand the meaning of `else`, which negates the previous condition.  Once we reach the `else` branch, we are guaranteed that `dist > 0.7` is false, which means that `dist <= 0.7` is true, so the check for `dist <= 0.7` is redundant.

## Answer Keys

### Collatz

We can break the problem down into three sub-problems.  So, write three functions:

1. Given a number $n$, perform one step of the operation.  This can be solved with a simple `if-else` statement.  Most students do not have a problem with this.

2. Given a number $n$, find the stopping time.  This involves repeatedly calling `one_collatz_step` until we reach 1, and count how many steps are needed.  Most students do not have a problem with this.

3. Given a number $N$, find the number between 1 to $N$, inclusive, that gives the largest stopping time, _breaking tie by choosing the larger number_.

This is similar to the max problem that we have seen since Lecture 1, so most students are able to solve this.  About 1/6 of you, however, did not properly break ties by choosing the larger number.  Recall from your Tutorial 1 that, when you compare with `max_so_far`, whether you use `>` or `>=` makes a big difference here.  You get 1 mark deduction if you do not break ties properly.


### Weekday

Let's look at how to solve this problem without using any formula.  The most straightforward way is to count how many days have passed since January 1, 1900, and do a `% 7`.

We can break the problem down into several subproblems.  Suppose the input date is `year`, `month` and `day`,

1. Count the number of days from January 1, 1900, to December 31, the year before (i.e., `year-1`).

This can be done by just summing up, in a loop, the days in every year from 1900 to `year-1`, inclusive.  For each year, if it is a leap year, add 366, otherwise, add 365.

Some common mistakes include an error in loop terminating condition (either adds one extra year or one less year).

2. Check if a year is a leap year.

This comes straight from Exercise 2.

3. Count the number of days since January 1, the current year.  

This subproblem is similar to the problem from Exercise 2.  You have to, however, be careful of whether `year` is a leap year.  (Question 3 from Exercise 2 assumes that the year is not a leap year).  So, you should add 1 to the answer if `month` is larger than 2 and `year` is a leap year.

Common bugs include: (i) not checking if the current year is a leap year, and (ii) add one day for all months (not just March onwards).

4. Determine the day of the week.

From (1) and (3) you get the total number of days since January 1, 1900.  We can then do a `% 7`.  If we get 0, it is a Monday; 1, a Tuesday; etc.  Some students lose points unnecessarily by having spelling errors in the output.

There are other simpler ways to solve this problem.  Some students use the [Zeller's Algorithm](https://en.wikipedia.org/wiki/Zeller%27s_congruence), which is perfectly fine too.

### Circle

Again, break it down into several smaller problems.  

1. Calculate the distance of a location to the center.

Most common issues we see:

- Many of you use `pow(x, 2)` to square an integer.  `pow` is a powerful function and is written to be general, so it is slower than just doing `x*x`.

- Many of you use `labs()` or `fabs()` on the result of a square or a square root function.  This is redundant since the square of a number and the square root of a number is always positive!

2. Draw each "pixel" of the circle.  The drawing part is actually easy.  

- Suppose you have a function to compute the distance, say, `dist(row, col, r)`, and you do this:
```C
if (dist(row, col, r) > 0.7) {
    cs1010_println_string("+");
} else if (dist(row, col, r) > 0.5) {
    cs1010_println_string("*");
} else ..
```

This is functionally correct, but it leads to multiple invocations of `dist` and calculation of the distance, even though the result does not change.  When we start to factor in efficiency into grading criteria, you will be penalized for this.

3. Draw each row of the circle.

4. Draw the circle.

Most students have no problem with (3) and (4) above.

### Pattern

Some students creatively call this the "Christmas Tree" problem :) 

This might seems daunting at first, but once you break it down into smaller problems, it can be solved fairly easily.  This question is designed to see if students can, at this stage, solve problems by breaking it down into multiple smaller problems -- something that we have been iterating since Lecture 2.

Let say the input be the interval $n$ and height $h$.

For this problem, we can break it down into the following sub-problems:

1. Find the first number in each leading cell at each row.  Example, for $n$ = 2, the first number is 1, 3, 7, 13, .. For $n$ = 5, the first number is 1, 6, 16, 31, 51, ..  This leading number can be calculated with the following loop:

```C
long leading_number(long row, long n) {
    long number = 1;
    for (long i = 1; i <= row; i+= 1) {
      number += i*n;
    }
    return number;
  }
```

2. Find the rest of the $n-1$ numbers in each leading cell at each row.   Once we have the first number, we just loop through the rest of the $n - 1$, increment by `row + 1` each time, something like this:
```C
void print_pattern(long row, long col, long n) {
    long num = leading_number(row, n);
    for (long i = 0; i < n; i += 1) {
        num += (row + 1);
    }
}
```

3. We can then find the numbers in the rest of the cells in the same row.

```C
void print_pattern(long row, long col, long n) {
    long num = leading_number(row, n) + col; // note the change here
    for (long i = 0; i < n; i += 1) {
        num += (row + 1);
    }
}
```

4. The steps above give us the number in each cell, all we need to do now is to decide whether to print `#` or ` `.  Suppose we have a function `is_prime`, we can do this:

```C
void print_pattern(long row, long col, long n) {
    long num = leading_number(row, n) + col; // note the change here
    bool found = false;
    for (long i = 0; i < n && !found; i += 1) {
        if (num == 1 || is_prime(num)) {
            cs1010_print_string("#");
            found = true;
        } else {
            num += (row + 1);
        }
    }
    if (!found) {
        cs1010_print_string(" ");
    }
}
```

5. Now, let's figure out how to check if a number is prime.  You have seen this problem in Exercise 3.  But there are different solutions, with different degrees of efficiency. 

Here is the first version:
```C
bool is_prime(long n)
{
  bool is_prime = true;
  for (long i = 2; i <= n - 1; i++) {
    if (n % i == 0) {
      is_prime = false;
    }
  }
  return is_prime;
}
```

It checks all numbers between 2 to `n - 1`, if there is a number `i` by which `n` is divisible, then `n` is not prime.

This is an incredibly slow implementation even though it is correct. The most glaring issue is that it continues to check, if even though it has found an "evidence" that `n` is not a prime.

What we should do, instead, is to immediately bail out of the function once we found that `n` is not a prime.

```C
bool is_prime(long n)
{
  for (long i = 2; i <= n - 1; i++) {
    if (n % i == 0) {
      return false;
    }
  }
  return true;
}
```

We can do even better.  Since the smaller integer factor is 2, there is actually no need to check if `n` is divisible by anything larger than `n/2`.  For instance, to check if 17 is prime, there is no need to check if 17 is divisible by 9, 10, 11, 12, .. 16.  So we can skip checking half the number! 

```C
bool is_prime(long n)
{
  for (long i = 2; i <= n/2; i++) {
    if (n % i == 0) {
      return false;
    }
  }
  return true;
}
```

Can we do better?  It turned out, yes.  Suppose `n` is a composite number is equals to `a * b`, for some integer `a` and `b`.  One of these two numbers must be smaller or equal to square root of `n`.  (Spend a moment to think about why -- if you are taking CS1231, try to prove this by contradiction.)  So, we do not have to check beyond the square root of `n`.

```C
bool is_prime(long n)
{
  for (long i = 2; i <= sqrt(n); i++) {
    if (n % i == 0) {
      return false;
    }
  }
  return true;
}
```

We can further optimize this.  Some of you actually use the fact that every prime besides 2 and 3 must be of the form `6k - 1` or `6k + 1` and use this to speed up the `is_prime()` function.  :+1:

6. Now, coming back to printing the Christmas tree.  Now that we know what to print in each cell, we just need to write functions to print each row and each col, padding the pattern properly to be a triangle.

```C
void print_blanks(long width) {
  for (long i = 0; i < width; i += 1) {
    cs1010_print_string(" ");
  }
}

void print_triangle(long row, long width, long n) {
  for (long i = 0; i < width; i += 1) {
    print_pattern(row, i, n);
  }
}

void draw_triangle(long h, long n) {
  for (long row = 0; row < h; row += 1) {
    print_blanks(h - row - 1);
    print_triangle(row, 2*row + 1, n);
    print_blanks(h - row - 1);
    cs1010_println_string("");
  }
}
```
