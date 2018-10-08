# Code Documentation

Code documentation is as important as the code itself.  It helps readers of your code, including your future self, to understand

-  the purpose of a piece of code
-  what assumptions are being made, and
-  the reasoning behind why certain things are done.

## C Syntax for Comments

In C, you can write comments in two ways:

- Either prefix a one-line comment with two slashes `//` , or
- Write multiple-line comments between `/*` and `*/`

For example:

```C
// assume the number of elements > 1
```

```C
/*
 This function reads in the radius of a sphere and returns the
 volume of the sphere.  We assume the radius is normalized between
 0.0 and 1.0.
 */
```

## The Doxygen Format

In CS1010, we will adopt the Doxygen format for C comments.  Doxygen is a tool that automatically generates HTML documents from comments in C code and is widely used in the industry. 
 
We write a Doxygen comment with an additional `*` after `/*`:

```C
/**
 
 */
```

The comments can be free-form text.  However, to help with creating a more structured document, we can add what Doxygen calls special "commands".  I view these commands as keys to certain information.  Useful commands are:

- `@author`: the name the author
- `@file`: the name of a file
- `@pre`: the precondition of a function
- `@post`: the postcondition of a function
- `@param[<dir>] <name>`: describe a parameter of a function.  `<name>` is the name of the parameter, `<dir>` can be `in`, `out`, `in,out` corresponding to whether the parameter is used as input, output, or both.
- `@return`: describe the return value of a function

The comments should be placed before a file, a function, or a variable that you want the comment to apply to.

## Example

Here is an example:

```C
/**
 * @file: cs1010.c
 * @author: Ooi Wei Tsang
 * 
 * This file contains implementation of the CS1010 I/O library to 
 * simplify the reading and writing of integer, real numbers, and text
 * from the standard input and output respectively.
 */

/**
 * Raed k white-space-separated words from the standard input in an array.
 * The notion of "word" is the same to cs1010_read_word().  The caller is
 * responsible for freeing the memory allocated for the array by calling
 * free().
 *
 * @param[in] k The number of words to read.
 * @return Returns NULL if there is a memory allocation error, otherwise, 
 * return an array of char* containing the words.
 */
char** cs1010_read_word_array(int k) 
{
   :
}
```

You can also see additional examples in the [solution posted for Assignment 1](as01-comments.md)

