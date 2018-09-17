# Unit 16: Strings

We have seen strings as a sequence of characters stored in double quotes, e.g., "hello!".  In C, a string is nothing more than just an array of `char` values (Recall from [Unit 5](05-first-c.md#other-types-in-c) that `sizeof char` is 1).

The only thing special about a string is that it _always_ end with a `0` value (note: not character '0' which has a value of 48, but the value 0).  Since the character with value 0 is called the null character, we refer that strings in C as null-terminated strings.

In C, we distinguish between a string and a `char` by the quotes used.  String uses double quotes `"`, while a `char` uses single quote `'`.  So we can do the following and they are equivalent:

```C
char hello1[7] = {'h', 'e', 'l', 'l', 'o', '!', 0}; 
char hello2[7] = "hello!";
```

Of course, nobody actually initializes a string in the first way above.

We can also skip the size of the array, as mentioned above, or, more commonly, use a `char *` type for a variable storing a constant string.
```C
char hello3[] = "hello!";
char *hello4 = "hello!";
```

## CS1010 I/O Library

The CS1010 I/O library provides two functions, one to read a word (separated by white-space characters) and the other to read a line (separated by a newline character).  They are `cs1010_read_word()` and `cs1010_read_line()` respectively.  We can also read multiple words and multiple lines with `cs1010_read_word_array()` and `cs1010_read_line_array()`.  The results are stored in an array of strings.

## Problem Set 16

### Problem 16.1

Write the following functions (without calling the standard C functions declared in `<string.h>` such as `strlen`, `strcmp`, `strcpy`, `strncpy`):

a. `long string_length(char *str)` return the length (i.e., the number of characters) of the string `str`.

b. `bool string_equal(char *str1, char *str2)` return `true` if the two strings `str1` and `str2` contains exactly the same content, `false` otherwise.  (Note: `str1 == str2` does not compare if two strings have the same content. (Why?))

c. `void string_n_copy(char *dst, char *src, long n)` copy at most `n` characters from string `src` to `dst`.  If `src` has less than `n` characters, then `dst` is padded with null character 0.  If `src` has `n` or more characters, `dst` is not null-terminated.  Assume that `dst` and `src` do not overlap.
