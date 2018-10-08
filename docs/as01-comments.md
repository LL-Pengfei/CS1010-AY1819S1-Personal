# Assignment 1: Comments

## Notes on Marking Schemes

- We apply the penalty to each program independently, i.e., the same mistake repeated in the different program get penalized multiple times.  
- You will receive 0 marks if your program cannot be compiled or is plagiarised from another.

- Code that fails all test cases and showing that student does not understand the main concept tested in each question (writing arithmetic expressions in `invest`, recursion in `digits`, use of functions in `box`, and logical expressions and conditionals in `taxi`), receives 0 marks straight away.

- For the rest, we start with full marks and start deducting marks for each error.

- While the teaching team tries to ensure fair and consistent grading of assignments across all groups, each TA can, at his/her own discretion, and with the approval of Wei Tsang, adjust the grading criteria to align with the messages that they sent to the students in their tutorial group.

- The following list includes some of the common mistakes we encountered.  

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

## Answer Keys

### Invest

This problem assesses if students know how to use arithmetic operations, parenthesis, and math library properly.  Most common mistakes include using `float` and `int` as data type, which would lead to loss of precision and overflow.  Suppose someone has $2.6 billion in his bank account, using `int` would cause an overflow.

```C
/**
 * CS1010 Semester 1 AY18/19
 * Assignment 1: Invest
 *
 * Read in three positive integer corresponding to the principal, the
 * interest rate, and the number of years.  Print the resulting amount
 * after the given num of years to the standard output.
 *
 * @file: invest.c
 * @author: XXX (Group YYY)
 */

#include "cs1010.h"
#include <math.h>


int main()
{
  long principal = cs1010_read_long();
  long rate = cs1010_read_long();
  long num_of_years = cs1010_read_long();

  double r = rate/100.0;
  double result = principal*(1 - pow(r, num_of_years + 1))/(1 - r);

  cs1010_println_double(result);
}
```

### Box

This problem tests if students know how to write and reuse functions.  

Some students wrote different versions of `area_of_rectangle`:
```C
long area_of_rectangle1(long width, long height)
{
  return width * height;
}

long area_of_rectangle2(long width, long base)
{
  return width * base;
}

long area_of_rectangle3(long base, long height)
{
  return base * height;
}
```

demonstrating that they do not understand how functions can be reused by passing in different parameters.

This problem also demonstrates how functions written for another problem (calculating the hypotenuse of a triangle in class) can be used to solve a different problem (diagonal of the box).  The `hypotenuse_of` function written in class, however, takes in `long` instead of `double`, so we have to change that.  Some of you also change the `hypotenuse_of` method to take in three parameters so that it calculates the diagonal directly.  This solution is fine as well.

```C
/**
 * CS1010 Semester 1 AY18/19
 * Assignment 1: Box
 *
 * Read in three positive integer corresponding to the width,
 * height, and length of a box from the standard input, and
 * print the total surface area and the length of the diagonal
 * to the standard output.
 *
 * @file: box.c
 * @author: XXX (Group YYY)
 */

#include "cs1010.h"
#include <math.h>

/**
 * Calculate the hypotenuse of a triangle.
 *
 * @param[in] base The base of the triangle.
 * @param[in] height The height of the triangle.
 * @return The hypotenuse of the triangle.
 */
double hypotenuse_of(double base, double height)
{
  return sqrt(base*base + height*height);
}

/**
 * Calculate the area of a rectangle.
 *
 * @param[in] width The width of the rectangle.
 * @param[in] height The height of the rectangle.
 * @return The surface area of the rectangle.
 */
long area_of_rectangle(long width, long height)
{
  return width * height;
}

int main()
{
  long width = cs1010_read_long();
  long height = cs1010_read_long();
  long length = cs1010_read_long();

  long surface_area = 2*area_of_rectangle(width, height) +
    2*area_of_rectangle(width, length) +
    2*area_of_rectangle(length, height);
  cs1010_print_long(surface_area);

  cs1010_print_string(" ");

  double hypotenuse = hypotenuse_of(hypotenuse_of(width, height), length);
  cs1010_println_double(hypotenuse);
}
```

### Digits

Most students who solve this correctly (without a loop and without segmentation fault) are able to get the idea of how recursion can be used here.  The most common error we see if a mismatch of types, where students use `int` leading to failure when we test with a large integer beyond the range of `int`.  

```C
/**
 * CS1010 Semester 1 AY18/19
 * Assignment 1: Digits
 *
 * Read in a positive integer from the standard input and print
 * the sum of all digits in the integer to the standard output.
 *
 * @file: digits.c
 * @author: XXX (Group YYY)
 */

#include "cs1010.h"

/**
 * Sum the digits in input.
 *
 * @param[in] input The integer whose digits we are summing.
 * @return The sum of digits in the parameter input.
 */
long sum_of_digits(long input)
{
  if (input < 10) {
    return input;
  }
  long last_digit = input % 10;
  long rest = input / 10;
  return last_digit + sum_of_digits(rest);
}

int main()
{
  long input = cs1010_read_long();
  cs1010_println_long(sum_of_digits(input));
}
```

### Taxi

This is a more complex problem.  We hope that students, when thinking about the solution of this problem, can appreciate the importance of breaking down the problem into simpler subproblems so that you can think, solve, and test the sub-solutions, independently, before combining them to solve the larger problem.  

Another objective of this question is to get students to think about what parameters and variables are needed to solve a subproblem.  Students who think through this should realize that to calculate the metered fare only the distance is needed and to calculate the surcharge, only the day and time is needed.  Further, to determine evening peak and midnight peak, only the hour is needed (not minutes nor day).

Some common mistakes include being careless in checking for the peak hour surcharge (bug in logical expressions).  

Some students use `ceil` to calculate the metered fare, which is fine (simpler than the sample code below).  But a few students incorrectly use `round` instead.  

```C
/**
 * CS1010 Semester 1 AY18/19
 * Assignment 1: Taxi
 *
 * Read in four positive integer corresponding to the day of the week,
 * the hour and minute of boarding, and the distance traveled.  Print
 * to the standard output, the fare of the taxi.
 * height, and length of a box from the standard input, and
 * print the total surface area and the length of the diagonal
 * to the standard output.
 *
 * @file: taxi.c
 * @author: XXX (Group YYY)
 */

#include "cs1010.h"
#include <stdbool.h>

/**
 * Check if a given day is a weekday.
 *
 * @param[in] day The day of the week (1 for Monday, 7 for Sunday).
 * @return true if the day is a weekday, false otherwise.
 */
bool is_weekday(long day)
{
  return (day >= 1 && day <= 5);
}

/**
 * Check if a given time falls under morning peak hour (6am to 9:29am)
 *
 * @param[in] hour The given hour.
 * @param[in] minute The given minute.
 * @return true if the given time falls under morning peak hour.  
 *         false otherwise.
 */
bool is_morning_peak(long hour, long minute)
{
  return (hour >= 6 && hour < 9) || (hour == 9 && minute <= 29);
}

/**
 * Check if a given time falls under evening peak hour (6pm to 11:59pm).
 *
 * @param[in] hour The given hour.
 * @return true if the given time falls under morning peak hour.  
 *         false otherwise.
 */
bool is_evening_peak(long hour)
{
  return (hour >= 18 && hour <= 23);
}

/**
 * Check if a given time falls under midnight peak hour (0:00 to 5:59am).
 *
 * @param[in] hour The given hour.
 * @return true if the given time falls under midnight peak hour.  
 *         false otherwise.
 */
bool is_midnight_peak(long hour)
{
  return (hour >= 0 && hour < 6);
}

/**
 * Check if surchange should be applied given day and time.
 *
 * @param[in] day Day of the taxi trip
 * @param[in] hour Hour of boarding time.
 * @param[in] minute Minute of boarding time
 * @return The surchage applied (1.0 if no surcharge)).
 */
double surcharge(long day, long hour, long minute)
{
  if (is_weekday(day) && is_morning_peak(hour, minute)) {
    return 1.25;
  }
  if (is_evening_peak(hour)) {
    return 1.25;
  }
  if (is_midnight_peak(hour)) {
    return 1.5;
  }
  return 1.0;
}

/**
 * Calculate the metered fare for a taxi trip (before surcharge).
 *
 * @param[in] distance The distance travelled.
 */
double metered_fare(long distance)
{
  double fare = 3.40;

  distance -= 1000;
  if (distance <= 0) {
    return fare;
  }

  if (distance <= 9200) {
    fare += 0.22 * (distance / 400);
    if (distance % 400 > 0) {
      fare += 0.22;
    }
  } else {
    fare += 0.22 * (9200 / 400);
  }

  distance -= 9200;
  if (distance <= 0) {
    return fare;
  }

  fare += 0.22 * (distance / 350);
  if (distance % 350 > 0) {
    fare += 0.22;
  }

  return fare;
}

/**
 * Calculate the fare for a taxi trip (including surcharge).
 *
 * @param[in] day Day of the taxi trip
 * @param[in] hour Hour of boarding time
 * @param[in] minute Minute of boarding time
 * @param[in] distance The distance travelled
 */
double fare(long day, long hour, long minute, long distance)
{
  return metered_fare(distance) * surcharge(day, hour, minute);
}


int main()
{
  long day = cs1010_read_long();
  long start_hour = cs1010_read_long();
  long start_minute = cs1010_read_long();
  long distance = cs1010_read_long();

  cs1010_println_double(fare(day, start_hour, start_minute, distance));
}
```
