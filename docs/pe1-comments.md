# PE1: Comments and Answer Keys 

## vote

### Comments
This is supposed to be a very easy, "give-away" question, but only 60% of students get full marks for this question.

Common mistakes include:

- **Using the wrong type**.  Some students read integer values as `double` or read `long` as `long long`. These do not affect the correctness but it does not demonstrate that the students understand the differences between the different types.  There is a -0.5 penalty for each one.   A more serious error involves using `int` for the number of votes (-1) or `long` for the percentage (-1).  
- **Integer division**.  Some students used integer division when calculating the percentage.  For instance:
```C
double percentage = (m/(m+n))*100.0;
```

    The computation of `m/(m+n)` is done entirely as `long` integer.  You need to either cast `m` or `n` to double, or multiple `100.0` (a double) earlier:
```C
double percentage = (m*100.0/(m+n));
```

- **Redundant Cast**.  Some studnets do not understand the needs for casting and simply cast everything:
```C
double percentage = ((double)m*(double)100.0/((double)m+(double)n));
```
   
    I decided not to deduct marks for this, but this is not the right way to write an arithmetic expression!

- **Redundant Functions**.  Some students wrote something like this:

	```C
	double percentage_of_nixon(long nixon, long total) {
		return nixon*100.0/total;
	}

	double percentage_of_mcneal(long mcneal, long total) {
		return mcneal*100.0/total;
	}
	```

    This shows that students do not understand that functions are supposed to be reusable that there is only one function needed.  There is a -1 penalty for that.

- **Misc.**  Some students made simple mistakes like printing the numbers without a space in between or print the two numbers on different lines (-0.5), or didn't include the right headers (-1).

### Solution

```C
#include "cs1010.h"
int main()
{
  long mcneal = cs1010_read_long();
  long nixon = cs1010_read_long();
  long total = mcneal + nixon;

  cs1010_print_double(mcneal*100.0/total);
  cs1010_print_string(" ");
  cs1010_println_double(nixon*100.0/total);
}
```

## newton

### Comments

Newton is another easy question that surprisingly many students are struggling with.  

- **Needless Careless Mistakes** Many students wrote down the wrong formula for $f(x)$ or $f'(x)$, which I deduct 1 marks after a facepalm.  

- **Wrong Type** Quite a few students read the inputs as `long` instead of `double`.  There is a 1 mark deduction for this.  A few students used `float` instead of `double`.  As I have mentioned, you should never touch `float` at all, since its precision is {--lame--} low.  If you use `float`, you would not get the correct answer for some of the test cases due to floating point errors.  There is a 0.5 mark deduction for using `float` instead of `double`.

- **Uninitialized Variable** There are also a number of students who got the following bugs:
```C
double root;
while (fabs(f(a, b, c, d, x)) >= 0.000000001) {
  :
}
return root;
```

    Suppose that the initial guess `x` is already close enough to the root, the loop is never entered, and the function returns `root` uninitialized.  The lesson here is that always make sure you cover all possible cases.  Also, please think about what variables do you really need to solve a problem.  In this question, you do not any extra variable -- so `root` is completely unnecessary.  The more variable you used, the more complex your code get, the more likely you introduce bugs into your code, as shown in the example above.

- **Terminating Conditions** Another common mistake is the terminating conditions.  A handful of you wrote something like this:
    ```C
    while (fabs(f(a, b, c, d, x) >= 0.000000001)) {
    ```

    Here, the code tries to pass a `true` or `false` expression to `fabs`, which the compiler warns.  A few of you, instead of fixing the parenthesis, change `fabs` to `abs` instead to silence the compiler >.<   

    Other mistakes include not setting the right threshold (some uses 0.001, or 0.00001 -- why oh why?) or use the wrong comparison:
    ```C
    while (fabs(f(a, b, c, d, x) < 0.000000001)) {
    ```

    Some did not use `fabs` at all:
    ```C
    while (f(a, b, c, d, x) >= 0.000000001) {
    ```

    Some checked if the root is small enough, instead of the value of function is small enough:
    ```C
    while (fabs(x) >= 0.000000001) {
    ```

    A small number of students hardcoded the number of steps (e.g., always loop 4 times), which works for the sample but not for the general cases.

    There is a one mark deduction for all errors related to terminating conditions.

    One strange thing some of you do is to use a `for` loop for this question:
    ```C
    for (long i = 0; fabs(x) >= 0.000000001; i += 1)
    ```

    The loop still terminates correctly, but the variable `i` is entirely useless.  No marks are deducted but I should have for not demonstrating that you know which loop to use.

- **Use of pow()** I did not deduct marks for this -- but using `pow` for squaring and cubing is an overkill.  `pow` is a general function that works even for floating point exponentiation.  So it is slower.  Some of you even wrote `pow(x, 1)` and `pow(x, 0)`.  Marks would have been deducted if efficiency is a criterion.

### Answer

```C
#include "cs1010.h"
#include <math.h>

double f(double a, double b, double c, double d, double x) {
  return a*x*x*x + b*x*x + c*x + d;
}

double fp(double a, double b, double c, double x) {
  return 3*a*x*x + 2*b*x + c;
}

double find_root(double a, double b, double c, double d, double x) {
  double fx = f(a, b, c, d, x);
  while (fabs(fx) >= 0.000000001) {
    double fpx = fp(a, b, c, x);
    x -= fx/fpx;
    fx = f(a, b, c, d, x);
  }
  return x;
}

int main()
{
  double a = cs1010_read_double();
  double b = cs1010_read_double();
  double c = cs1010_read_double();
  double d = cs1010_read_double();
  double x = cs1010_read_double();

  x = find_root(a, b, c, d, x);

  cs1010_println_double(x);
}
```

## Goldbach

### Comments

This question is a little bit more tricky than `newton`, but not much.  

- **Checking For Prime** The disappointing thing is that, even though you have gone through an exercise and an assignment that involves checking if a number is prime, with the solution given comparing different approaches, many of you still wrote the wrong function for checking prime, uses the wrong type, or wrote the slow version.  Since I deducted one mark for each bug, an incorrect `is_prime` might cost you 1-3 marks.  This is entirely unnecessary >.<

    Try to see if you can spot the bugs and why it fails in each of the following.  This is a good exam question that I might ask in a future semester -- don't tell your junior *shush*.


    ```C
    bool is_prime(long n)
    {
      for (long i = 1; i <= sqrt(n); i++) {
        if (n % i == 0) {
          return false;
        }
      }
      return true;
    }
    ```
    ```C
    bool is_prime(long n)
    {
      for (long i = 2; i < sqrt(n); i++) {
        if (n % i == 0) {
          return false;
        }
      }
      return true;
    }
    ```

    ```C
    bool is_prime(long n)
    {
      for (long i = 2; i <= ceil(sqrt(n)); i++) {
        if (n % i == 0) {
          return false;
        }
      }
      return true;
    }
    ```

    ```C
    bool is_prime(long n)
    {
      for (long i = 2; i <= sqrt(n); i++) {
        if (n % i == 0) {
          return false;
        } else {
          return true;
        }
      }
    }
    ```

   Another common mistake related to checking of prime is that the function should return a `bool` (since a number is either a prime or not).  Some students returned an integer from this function.  I deducted one mark for not demonstrating an understanding of boolean functions.


   - **Unnecessary Loops**  This is another strange but common code that you have written.  I did not deduct marks since efficiency is not a criterion for PE1 -- but I am puzzled why you write code like this. 

    Let's say I ask you this question: 31 plus what equals to 100?  Any primary school students can tell you, take 100, subtract 31, you get 69.  So 31 + 69 = 100.   

    This is not many of you do to find the answer.  What you do is equivalent to:

    ```C
	for (j = 1; j <= 100; j += 1) {
      if (31 + j == 100) {
        cs1010_println_long(j);
      }
	}
    ```

    Essentially, checking is 31 + 1 equal to 100?  is 31 + 2 equals to 100? etc. until you find an answer.  After that, you continue to check, is 31 + 70 equals to 100? is 31 + 71 equal to 100? and so on.

    This is just silly >.<

    In the context of `goldbach`, the code you wrote is:

    ```C
    for (long i = 0; i <= n/2; i += 1) {
      if (is_prime(i)) {
        for (long j = 0; j < n; j += 1) {
          if (i + j == n) {
            if (is_prime(j)) {
               count += 1;
            }
          }
        }
      }
    }
    ```

    Can you see where the silly loop appears above?

 -  **Short Circuiting** I have shown an example of short-circuiting in class, using exactly checking of prime as an example.  However, some of you still wrote code like the following.  This is another disappointment and I am tempted to deduct marks (but didn't).  The improper use of short-circuiting often occurs with the silly loop to solve `n - i` above.

    ```C
    for (long i = 0; i <= n/2; i += 1) {
      if (is_prime(i)) {
        for (long j = 0; j < n; j += 1) {
          if (is_prime(j) && i + j == n) {
            count += 1;
          }
        }
      }
    }
    ```

    Here, the code is spending lots of time checking if j is a prime.  After that, if i + j is not n, (which is likely since there is only one such j), we do nothing.  The effort in checking if j is prime is wasted.  Remember: do not do redundant work.

    ```C
    for (long i = 0; i <= n/2; i += 1) {
      for (long j = 0; j < n; j += 1) {
        if (is_prime(i) && is_prime(j) && i + j == n) {
          count += 1;
        }
      }
    }
    ```
   
    This is even worse -- note that the code above checks if i is prime, repeatedly, even if i does not change between Lines 3-5.  Remember: do not do repetitive work.

- **Double Counting** Another common bug is to double count the number of pairs, as follows (e.g., count the pair (3,5) and (5,3) once each for input 8.).  Some students do realize that they are double counting and try to solve this by setting `count = count/2`.  This does not work since not every pair gets double counted.  Example, (3, 3) is counted once if the input is 6.

    ```C
    for (long i = 0; i < n; i += 1) {
      if (is_prime(i) && is_prime(n - i)) {
        count += 1;
      }
    }
    ```

### Answer

```C
#include "cs1010.h"
#include <math.h>
#include <stdbool.h>

bool is_prime(long n)
{
  for (int i = 2; i <= sqrt(n); i += 1) {
    if (n % i == 0) {
      return false;
    }
  }
  return true;
}

long count_prime_pairs(long n)
{
  long count = 0;
  for (int i = 2; i <= n/2; i += 1) {
    if (is_prime(i) && is_prime(n-i)) {
      count += 1;
    }
  }
  return count;
}

int main()
{
  long n = cs1010_read_long();
  cs1010_println_long(count_prime_pairs(n));
}
```

## Digits

### Answer

```C
long longest_consecutive_digits(long n)
{
  long longest_count = -1;
  long longest_digit;
  long current_count = 0;
  long current_digit = n % 10;

  do {
    // Increase the counter if we see the same digit.
    // Otherwise reset counter to 1.
    if (n % 10 == current_digit) {
      current_count += 1;
    } else {
      current_count = 1;
    }

    // Checks if we find a longer (or equally long)
    // consecutive sequence.  Update longest_digit
    // and longest_count if so.
    if (current_count > longest_count) {
      longest_digit = current_digit;
      longest_count = current_count;
    } else if (current_count == longest_count) {
      if (current_digit < longest_digit) {
        longest_digit = current_digit;
      }
    }

    // Update the current digit to the last digit of n
    // and shorten n by one digit.
    current_digit = n % 10;
    n = n / 10;
  } while (n > 0);
  return longest_digit;
}

```

### Comments {++ Updated ++}

Overall, more students solved this problem than expected.   63 students receive either 7 or 8 marks for this question.  *clap clap*.  

Marking scheme: I deduct one mark for each bug, and try to find the minimum number of fixes to make the code works correctly.  

A few common mistakes that I saw for this question are as follows.

- **Wrong initialization**  A common error is to reset `current_count` to 0 if a different digit is encountered.  It should be reset to 1 (since this different digit is already encountered _once_).

    ```C
    long longest_consecutive_digits(long n)
    {
      long longest_count = -1;  
      long longest_digit;
      long current_count = 0;
      long current_digit = n % 10;

      do {
        if (n % 10 == current_digit) {
          current_count += 1;
        } else {
          current_count = 0; // <-- bug
        }

        if (current_count > longest_count) {
          longest_digit = current_digit;
          longest_count = current_count;
        } else if (current_count == longest_count) {
          if (current_digit < longest_digit) {
            longest_digit = current_digit;
          }
        }

        current_digit = n % 10;
        n = n / 10;
      } while (n > 0);
      return longest_digit;
    }
    ```

- **Checking if Longer** The `longest_digit` didn't get updated sometimes.  The mistake is to check if we have a longer sequence only when the same digit is encountered, or only when a different digit is encountered.  This would cause the code to fail if the input only contains different digits (e.g., 123) or only the same digits (e.g., 111).  To handle such cases, we need to check if we found a new longest sequence regardless of the digit encountered.

    ```C
    long longest_consecutive_digits(long n)
    {
      long longest_count = -1;
      long longest_digit;
      long current_count = 0;
      long current_digit = n % 10;

      do {
        if (n % 10 == current_digit) {
          current_count += 1;
          if (current_count > longest_count) {  // bug
            longest_digit = current_digit;
            longest_count = current_count;
          } else if (current_count == longest_count) {
            if (current_digit < longest_digit) {
              longest_digit = current_digit;
            }
          }
        } else {
          current_count = 1;
        }

        current_digit = n % 10;
        n = n / 10;
      } while (n > 0);
      return longest_digit;
    }
    ```

    ```C
    long longest_consecutive_digits(long n)
    {
      long longest_count = -1;
      long longest_digit;
      long current_count = 0;
      long current_digit = n % 10;

      do {
        if (n % 10 == current_digit) {
          current_count += 1;
        } else {
          if (current_count > longest_count) { // bug
            longest_digit = current_digit;
            longest_count = current_count;
          } else if (current_count == longest_count) {
            if (current_digit < longest_digit) {
              longest_digit = current_digit;
            }
          }
          current_count = 1; 
        }

        current_digit = n % 10;
        n = n / 10;
      } while (n > 0);
      return longest_digit;
    }
    ```

- **Breaking Ties.** Checking if `current_count` is larger and breaking ties is also commonly written wrongly.  Here are two common buggy versions:

    ```C
        if (current_count >= longest_count) {
          longest_count = current_count;
          if (current_digit < longest_digit) {
            longest_digit = current_digit;
          }
        }
    ```
    ```C
        if (current_count >= longest_count && current_digit < longest_digit) {
          longest_digit = current_digit;
          longest_count = current_count;
        }
    ```

- Finally, students who used a `while` loop might missed out the first (most significant digit) digit, if the code is structured as follows:

    ```C
     while (n > 0) {
        long current_digit = n % 10;
        long next_digit = (n / 10) % 10;
        if (next_digit == current_digit) {
          current_count += 1;
        } else {
          current_count = 1;
        }

        if (current_count > longest_count) {
          longest_digit = current_digit;
          longest_count = current_count;
        } else if (current_count == longest_count) {
          if (current_digit < longest_digit) {
            longest_digit = current_digit;
          }
        }

        current_digit = next_digit;
        n = n / 10;
      }
      return longest_digit;
    ```

## Square

### Comments
This is the hardest problem I set to differentiate the A+ from the As, and I did not expect many students to solve this.  About 10% of students, however, managed to get at least 7 marks for this question!   Well done.

For the rest of the students, who didn't get the right approach to the solution, I still give some marks: If you try to draw the square in some way, without recursion, you get 1 marks (for style) only.  Some students uses recursion as a replacement of loops to draw the border, this does not get to the "heart" of the problem, so still 1 mark.  If you attempted to use recursion to draw the inner squares, you get either 2 or 3 marks, depending on how close you are.   4 - 5 marks are given to those who get the overall idea of how to use recursion in this context, but didn't quite get it right.  6-7 are for those who get the correct answer, but has 1-2 bugs.

Solving this problem requires some _pattern recognition_ skill from computational thinking I mentinoed in the final lecture.  You should recognize that there are three regions to the square:

- The first and last rows (`row == 1 || row == width`) where we have to draw `##..#`.
- The second and penultimate rows (`row == 2 || row == width - 1`) where we have to draw `# .. #`.
- The rest (`row >= 3 && row <= width - 2`), where we have to draw `# .. #`, but with an inner square in between.

The next question is how to draw the inner square?   At `row == 3`, we have to draw the top row of the inner square.  The inner square is also smaller (width is smaller by 4).  So the recursion to draw the inner square is:

```
    print_square(row - 2, width - 4);
```

Once you get the above, everything should fall into place.

### Answer

```C
#include "cs1010.h"

/**
 * Print a line of given width consisting of # only.
 *
 * @param[in] width The width of the line.
 */
void print_line(long width) {
  for (long i = 0; i < width; i+=1) {
    cs1010_print_string("#");
  }
}

/**
 * Print a line of given width, consisting of " " except
 * the first and last characters, which is #.
 *
 * @param[in] width The width of the line.
 */
void print_border(long width) {
  cs1010_print_string("#");
  for (long i = 0; i < width - 2; i+=1) {
    cs1010_print_string(" ");
  }
  cs1010_print_string("#");
}

/**
 * Print one row of a concentric square, recursively.
 *
 * @param[in] row Which row of the square to draw.
 * @param[in] width The width of the square.
 */
void print_square(long row, long width) {
  if (width == 1) {
    cs1010_print_string("#");
  } else if (row == 0 || row == width - 1) {
    print_line(width);
  } else if (row == 1 || row == width - 2) {
    print_border(width);
  } else {
    cs1010_print_string("# ");
    print_square(row - 2, width - 4);
    cs1010_print_string(" #");
  }
}

int main()
{
  long width = cs1010_read_long();
  for (long row = 0; row < width; row += 1) {
    print_square(row, width);
    cs1010_println_string("");
  }
}
```
