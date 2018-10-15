# Unit 20: C Preprocessor: Constants and Macros

In this unit, we are going to delve deeper into the C compilation process, focusing on a step called _preprocessing_.  
This step is usually the first step in the compilation process.

Preprocessing is, in essence, a text processing and substitution process, and so it is not C specific.  This process is used by C to implement, among other things: (i) file inclusion, (ii) macro, and (iii) conditional compilation. 

A C preprocessor processes all the lines in the input file that starts with a _preprocessor directive_.  A directive starts with the letter `#`.  You have seen two of such directives, `#include` to include a file, and `#define` to define a constant.

## `#include`

The `#include` directive has the format

```C
#include <filename>
```
or

```C
#include "filename"
```

When the C preprocessor sees this directive, it reads the file specified by the given filename, and inserts the text, line-by-line, from this file, into the current file, in the location where the line `#include` occurs.   Any C preprocessor directive in the included file are recursively processed.

## #define Constant

We have seen how we can use `#define` to define constant values in our code in our exercises and assignments.  

We should avoid hardcoding constant values (also called _magic numbers_) in our code, so that our code can be easily changed when the requirement of our program has changed.

Take this code, for instance, from Taxi:

```C
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
```

In my answer posted, I have used many hardcoded values.  Suppose one day, the taxi fare changes (and that day will come).  Perhaps the base fare is more, perhaps the distance threshold is shorter.  We will have to change the code above to calculate the new fare.  By littering the code above with hardcoded values, the code is difficult and error prone to change.  Suppose we rewrite the code as:

```C
#define BASE_FARE 3.40
#define BASE_DISTANCE 1000
#define TIER_ONE_FARE 0.22
#define TIER_ONE_DISTANCE 400
#define TIER_ONE_LIMIT 9200
#define TIER_TWO_FARE 0.22
#define TIER_TWO_DISTANCE 350

double metered_fare(long distance)
{
  double fare = BASE_FARE;

  distance -= BASE_DISTANCE;
  if (distance <= 0) {
    return fare;
  }

  if (distance <= TIER_ONE_LIMIT) {
    fare += TIER_ONE_FARE * (distance / TIER_ONE_DISTANCE);
    if (distance % TIER_ONE_DISTANCE > 0) {
      fare += TIER_ONE_FARE;
    }
  } else {
    fare += TIER_ONE_FARE * (TIER_ONE_LIMIT / TIER_ONE_DISTANCE);
  }

  distance -= TIER_ONE_LIMIT;
  if (distance <= 0) {
    return fare;
  }

  fare += TIER_TWO_FARE * (distance / TIER_TWO_DISTANCE);
  if (distance % TIER_TWO_DISTANCE > 0) {
    fare += TIER_TWO_FARE;
  }

  return fare;
}
```

We have factored out all the hardcoded values into constants we defined.  It does make the code a bit harder to read, but now, it is super easy to change.  Suppose, one day, the base taxi fare is decreased to $3.20, and then $0.20 per 500m subsequently up to 10km, and $0.15 per 600m thereafter, we only need to change:

```C
#define BASE_FARE 3.20
#define BASE_DISTANCE 1000
#define TIER_ONE_FARE 0.20
#define TIER_ONE_DISTANCE 500
#define TIER_ONE_LIMIT 10000
#define TIER_TWO_FARE 0.15
#define TIER_TWO_DISTANCE 600
```

The logic of the code remains the same.   

The `#define` directive should be followed an _identifier_ and a _token_.  The token may contain space, but must be terminated by a newline.  In the example above, `BASE_FARE` is the identifier, and `3.20` is the token.

When the C preprocessor sees the `#define` directive, it replaces all instances of the identifier in the file with the token.  This is merely a text substitution operation.


## Example:

Consider the example below.  Suppose we have three files:

```C
/**
 * @file: a.c
 */

#include "b.h"

int main()
{
  foo(PI);
}
```

```C
/**
 * @file: b.h
 */

#include "c.h"
#define PI 3.1415926
```

```C
/**
 * @file: c.h
 */

void foo(double x);
```

Let's see what happen when we run C pre-processor on the file `a.c`.  We can ask `clang` to stop the compilation process after the pre-processing phase, using the flag `-E`.

```
clang -E a.c
```

The command will produce the output after C pre-processing:

```C
# 1 "a.c"
# 1 "<built-in>" 1
# 1 "<built-in>" 3
# 360 "<built-in>" 3
# 1 "<command line>" 1
# 1 "<built-in>" 2
# 1 "a.c" 2
# 1 "./b.h" 1
# 1 "./c.h" 1
void foo(double x);
# 6 "./b.h" 2
# 5 "a.c" 2
int main()
{
  foo(3.1415926);
}
```

The lines start with `#` are metadata meant for the compiler.  If we ignore those, we can see that the file `a.c` has been expanded into
```C
void foo(double x);
int main()
{
  foo(3.1415926);
}
```

Line 1 above is included from file `c.h`, which in turn is included from file `b.h`.  The C pre-processor also substitutes the text `PI` with the text `3.1415926`, as the identifier `PI` is defined in `b.h`.

## `#define` Macro

The `#define` directive can be used for a more flexible and powerful text substitution feature called _macro_.  _A macro is a block of code that is given an identifying name and is substituted and expanded during pre-processing._

For instance, we can write the following:

```
#define SQUARE(x) x*x
```

This macro is named `SQUARE`, just like a function we defined in Lecture 3 and it takes in a parameter `x` as well.  But that's where the similarity ends.  There are a few important differences between macros and functions in C:

- Macros are not called.  They are only substituted during preprocessing phase which performs text processing on the source code.
- Macros has no information about types. It has no return type and the parameters has no type.

Take the example below.  The file:

```C
#define SQUARE(x) x*x
#define PI 3.1415926
int main()
{
  double radius = 4.0;
  cs1010_print_double(PI*SQUARE(radius));
}
```

Get expanded into:

```C
int main()
{
  double radius = 4.0;
  cs1010_print_double(3.1415926*radius*radius);
}
```

Let's look at another example.  We have seen how to write a function that swap two the value of variables.  The one we wrote swaps two `double`.  If we want to swap two `long`, or two `char *`, etc, we will need to write a new function for each one.  

Let's write a generic macro that does swapping for any type.

```C
#define SWAP(T, x, y) {\
  T temp;\
  temp = x;\
  x = y;\
  y = temp;\
}

int main(){
  long x = 3.0;
  long y = -1.0;
  SWAP(long, x, y);
}
```

The macro `SWAP` takes in three parameters, the first is the type T, the second and the third are the variables to be swapped.  This macro definition spans multiple lines.  Since C preprocessor ends the definition of a macro with the end of line, we add a backslash character to "escape" the newline, telling the preprocessor not the treat the newline as the end of the macro definition.

The code above gets expanded to:

```C
int main(){
  long x = 3.0;
  long y = -1.0;
  { long temp; temp = x; x = y; y = temp;};
}
```

## Pitfalls and Best Practices

It is easy to forget that macro is doing simple text substitution without understanding of C syntax.  When we write macros, we should always guard against inproper usage of macros.  Let's consider this:

```C
#define SQUARE(x) x*x
```
```C
SQUARE(radius + 2)
```

When the preprocessor substitutes the macro `SQUARE`, it replaces all instances of the text `x` with the text `radius + 2`.  After substitution, we get `radius + 2*radius + 2`!  This is not what we expected.

To prevent such unexpected expansion, we should always add parenthesis in our macro expression:

```C
#define SQUARE(x) ((x)*(x))
```

So now, 
```
SQUARE(radius + 2)
```

gets expanded into `((radius + 2)*(radius + 2))`, which is what we would expect when we call `SQUARE`.

To help the readers of your code know that you are referencing a macro rather than a funciton, all macros should be written with upper case letter.

## Problem Set 20

### Problem 20.1

a) Consider the macro below:

```C
#define MIN(a,b)  a < b ? a : b

long i = MIN(10, 20);
long j = MIN(10, 20) + 1;
```

What are the values for `i` and `j` after executing the above?  

b) 

```C
#define MIN(a,b)  a < b ? a : b

long i = 10;
long j = 20;
long k = MIN(j, i++);
```

What are the values of `i` and `k` after executing the above?

### Problem 20.2

Suppose we write our `SWAP` macro without the opening and closing brackets:

```C
#define SWAP(T, x, y) T temp = x;\
  x = y;\
  y = temp;
```

What could go wrong?
