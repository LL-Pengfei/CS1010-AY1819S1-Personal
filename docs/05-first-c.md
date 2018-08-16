# Unit 5: First C Program

In this unit, we will write and compile your first C program.  You have already learned, conceptually, what is a variable, what is a type and what is a function.  Let's see how we apply these concepts to C.

To start, let's look at a simple snippet of C code:

```C
int square(int x) 
{
  return x*x;
}
```

The code above defines a function named `square`.  The word `int` appears _before_ the name `square`.  `int` is used to signify an integer type.  Appearing before the name `square` tells the compiler that `square` is returning a value of type `int`.

After the word `square`, we write the parameters to the function in parenthesis `(` and `)`.  In between `(` and `)` is `int x`: `x` is the name of the parameter, `int` is the type of that parameter.  

To summarize, in Line 1, `int square(int x)` defines a function named `square` that takes in an integer parameter `x` and is returning a value which is also an integer.

The next three lines are written in between curly brackets `{` and `}`.  You will see these used a lot in C and other programming languages with C-like syntaxes, such as Javascript, C++, and Java.  These curly brackets group a _block_ of _statements_ together.  In this example, this block defines how the function `square` computes the square of `x`, and what it returns.  This is sometimes called the _body of a function_.

In this function body, there is only one statement `return x*x;`  within the block.  A _statement_ is a unit in a programming language that expresses either a command to be executed or declares a new variable or function.  The word `return` says that this function `square` should return the following value, computed as `x*x` (`x` multiply by `x`).  This statement is terminated by a semicolon `;`.

The words `int` and `return` that appears above are _keywords_ defined in the C programming language, and they are reserved for the special meaning that they represent (a type and a command to return a value from a function).  We cannot repurpose keywords in C.

We are now ready to write our first C program.  The first program computes the square of the hypotenuse of a right-angled triangle with a base of 4 and height of 3.

```C
int square(int x) 
{
  return x*x;
}

int main() 
{
  int hypotenuse_square;
  
  hypotenuse_square = square(3) + square(4);
  return 0;
}
```

Remember that a C program consists of a bunch of functions, calling each other.  The most important function is called `main`, and it is the _entry point_ to the program.  It is where the operating system will begin to execute the program.  So every program must define exactly one function called `main`.

```C 
int main() 
{
  :
}
```

`main` returns an integer to the operating system, to signal to the operating system whether the program exits successfully or not.  In this case, we always return `0` (success) assuming that nothing goes wrong for simplicity.  This is the second example where you see the keyword `return` in action.

In modern C, the main always return 0 when it exits.  So, we will skip this statement `return 0;` from now on.

### Variable Declaration

In the example above, you also see the lines

```C
int hypotenuse_square;
```

This is declaration statement (terminated with a semicolon, again).  Each statement declares a variable with its corresponding type (`int` here).  We gave each variable a name, here we call the variable `hypotenuse_square`.

Remember that _all variables must be declared with its corresponding type before used in C_.

In C, a function must be either defined or declared before used as well.  In the example above, we define `square` before `main`.  If we switch the order of the two, the compiler will complain.  Unless we declare the function first.  Declaring a function means that we simply state the return type, the name, and the parameters, _without_ the body.

### Assignment Statement

The next line of the code shows an example of an assignment statement.  

```C
  hypotenuse_square = square(3) + square(4);
```

An assignment operator takes the form of:

```C
  left_hand_side = right_hand_side;
```

The `left_hand_side` must be the name of a variable.  We first evaluate the `right_hand_side` of the assignment statement, find its value, then put the value into the variable named on the `left_hand_side`.

Here, on the right-hand side, we call the function `square`, which we defined earlier.  We call `square` with _arguments_ `3` and `4` respectively.   We use the operator `+` to add the two results together.  The right-hand side should give the value 25, which we then assign to the variable `hypotenuse_square`.

Note that we use `=` equal sign for assignment, NOT for checking equality.  The C notation for checking for equality is `==` (we will come back to this later).

### Recap

Before we move on, let's recap some concepts:

- A C program consists of functions, invoking each other.
- Each function is defined by its returned type, followed by its name, its parameter(s) (within a pair of parenthesis) and its body (within a pair of curly brackets).  
- Each function must be defined or declared before it is used.  
- The function body consists of one or more statements.  We have seen assignment statements (using the `=` operator) and return statements, using `return` as the keyword.
- Each variable must be declared before it is used.  A declaration starts with its type followed by its name.

### Example 2

Let's change the program slightly, so that instead of computing the square of the hypotenuse, we compute the hypotenuse itself.  Recall that we said C provides a bunch of predefined functions, include `sqrt`, which computes the square root of a given number.  Since a square root of a number is not necessarily an integer, we need to use a variable with a type that can store a real number.  

To represent real numbers, we commonly use the type `float` (which is short for a floating point number -- named so due to how a real number is represented in bits).  A `float` type can store 32 bits.  To double the precision (64 bits), we can use the type `double`.  A `long double` type can store either 80-bits or 128-bits, depending on implementation.

But which one does `sqrt` returns?  To find out the exact _specification_ of a pre-defined function, we can consult the manual (or `man` for short) pages for the function.  

If you type `man sqrt` on the command line (or place the cursor on `sqrt` in `vim` and type `K` in command mode), you will see that the `sqrt` method has the following specification:

```C
double sqrt(double x);
```

The `sqrt` function returns a `double` precision real number.  We can now modify the program above to the following:

```C
double sqrt(double x); // not recommended

long square(long x) 
{
  return x*x;
}

double hypotenuse_of(long base, long height) 
{
  return sqrt(square(base) + square(height));
}

int main() 
{
  hypotenuse_of(3, 4);
}
```

Note that the first line declares the function `sqrt`, because we need to declare a function before it is used.  Since `sqrt` is pre-defined elsewhere, we do not have to supply the function body here.  Such practice of declaring a predefined function ourselves, however, is not recommended.  Different platform, compilers, libraries, may provide a different specification for the same function.  As such, it is better to use the declaration from the library that provides the predefined function itself.  A library usually provides one or more _header files_, a set of files that contain function declarations, type definitions, and constant definitions.  In the case of `sqrt`, its declaration is contained in a header file called `math.h`.  To include this file, you add the line `#include <math.h>` at the top of the program.

```C
#include <math.h>

long square(long x) 
{
  return x*x;
}

double hypotenuse_of(long base, long height) 
{
  return sqrt(square(base) + square(height));
}

int main() 
{
  hypotenuse_of(3, 4);
}
```

You might notice the same line appear in the man page for `sqrt`.  Thus, the man pages tell us which header file to include if you want to use a certain function.

!!! tips "File Extension"
    The convention for file extension for a C program is `.c` and for a C header file is `.h`.  Even though you are allowed to name the files with any extension you like, you should stick to the convention so that it is clear to other tools/programmers what is the purpose of each file.

## Other Types in C

An `int` variable is usually 32-bits long.  If we only need 16 bits, we use the type `short int`, or just `short`.  If we only need 8-bits, we use the type `char` (short for character).

Note that in the above, I said an `int` is _usually_ 32 bits.  Remember (from [Unit 1](01-program) that a C program gets compiled into machine code for a specific CPU architecture.  The C standard only guarantees that an `int` is at least 16 bits.  Although most C compilers compile `int` to 32 bits, there is no guarantee!    

If we need something more than an `int`, we can use `long int`, or just `long`.  The C standard guarantees that a `long` is at least 32 bits.  Depending on the implementation, it sometimes compiled to 32-bits, sometimes 64-bits.  To get even longer, we can use the type `long long int`, or just `long long`, which is guaranteed to be at least 64 bits[^1].

#### Signed vs. Unsigned 

We have seen earlier that the same sequence of bits, if interpreted as signed or unsigned, would result in a different value.  By default, all types in C refer to signed types.  If you want a variable that holds only non-negative integers, you can add the keyword `unsigned` to the front of the type.  Doing so would mean that the range of values you can store in the variable is doubled, without adding more bits (since the bit used to present the + or - sign is no longer needed).  

For instance, since we know that `square` can only return a non-zero integer, we can actually declare it as:

```C
unsigned long square(long x) 
{
  return x*x;
}
```

For most practical purposes in CS1010, a (signed) `long` suffices.  We introduce the notion of `signed` vs. `unsigned` for your information so that you know what they are when you come across them in others source code.  Mixing `signed` and `unsigned` can lead to subtle bugs in your code.  As such, _we will only use signed `long` for CS1010_.  You may choose to use them appropriately in other context, especially for embedded system or low-level programming.

!!! note "Unsigned Real Numbers?"
    There are no `unsigned` versions of `float` and `double`.  To understand the reason, we have to go further into how floating points numbers are represented in bits.  That is a topic for another module.


#### `stdint.h`

Since the number of bits for `int`, `long`, and `long long` could differ, in order to write a C program that is portable (i.e., works on different platforms, architecture, operating systems), we have two options:

- Do not write a program that assumes a specific number of bits is used for a certain type, or
- Use the type defined in the header file `stdint.h`: `int8_t`, `uint8_t`, `int16_t`, `uint16_t`, `int32_t`, `uint32_t`, `int64_t`, `uint64_t`.  The suffix `_t` is a convention to indicate that this is a customized type (more on this in later units).  The prefix `u` indicates that the type is an unsigned type.  The numbers `8`, `16`, `32`, and `64` indicate the number of bits for each type.  Thus, `uint32_t` is guaranteed to be of size 32 bits, and can hold unsigned integer values from $0$ to $2^32-1$.

[^1]: Adding more `long` does not make the integer longer, i.e., there is no `long long long int`.

## Problem Set 5

#### 5.1

In the example above, `sqrt` is declared to take in a parameter of type `double` .  But the argument that we pass in is the sum of two `int`, which is also an `int`.   Would this result in an error?  

#### 5.2

Consider the following alternative definition of `square`

```C
??? square(uint16_t x) {
	return x*x;
}
```

What should the return type of this `square` be, in order for the return type to be big enough to store all possible value for `x*x`?

