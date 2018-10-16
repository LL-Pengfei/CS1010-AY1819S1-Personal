# Unit 16: Strings

We have seen strings as a sequence of characters stored in double quotes, e.g., "hello!".  In C, a string is nothing more than just an array of `char` values (Recall from [Unit 5](05-first-c.md#other-types-in-c) that `sizeof char` is 1).

## Just a `char` Array
The only thing special about a string is that it _always_ end with a `0` value (note: not character '0' which has a value of 48, but the value 0).  Since the character with value 0 is called the null character, written as '\0', we refer that strings in C as null-terminated strings.

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

## Special Characters

The null character is written as '\0'.  The use of the backslash `\` creates an escape sequence that can be used to denote characters that would otherwise not visible on screen.  For instance, besides '\0', we will likely encounter '\n' (the newline character) '\t' (the tab character) and '\a' (the beep character) regularly.  

Furthermore, since we already use `\` to indicate the escape sequence, `'` to indicate a character, and `"` to indicate a string, in order to use these characters, we need to "escape" them -- we use '\\' for the backslash character, '\'', the single quote character, and the '\"' double quote character.

## {++String Literals++}

A _string literal_ refers to a string written between two `"` characters, such as `"Hello world!"`.  Such a string are still internally stored as an array of `char` values.  The location these arrays are stored depend on the platform, but usually they are stored in a read only region of the memory called the `text` region.  These strings are not meant to be modified.  Hence, trying something like this:

```C
char *str1 = "Hello!";
str1[5] = '.';
```

is not allowed and would crash your program.

The following, however, is OK:
```C
char str2[7] = "Hello!";
str2[5] = '.';
```

The differences between the two is that, `str1` points to a read-only region in the memory, while `str2` contains a copy of the string on the stack.

## Empty String

We commonly use the empty string `""` to indicate a special condition or to initialize a string variable, where appropriate.  The empty string is basically an array where the 0-th element is '\0'.

## CS1010 I/O Library

The CS1010 I/O library provides two functions, one to read a word (separated by white-space characters) and the other to read a line (separated by a newline character).  They are `cs1010_read_word()` and `cs1010_read_line()` respectively.  We can also read multiple words and multiple lines with `cs1010_read_word_array()` and `cs1010_read_line_array()`.  The results are stored in an array of strings.

## Problem Set 16

### Problem 16.1

Write the following functions (without calling the standard C functions declared in `<string.h>` such as `strlen`, `strcmp`, `strstr`):

a) `long string_length(char *str)` return the length (i.e., the number of characters) of the string `str`.

b) `bool string_equal(char *str1, char *str2)` return `true` if the two strings `str1` and `str2` contains exactly the same content, `false` otherwise.  (Note: `str1 == str2` does not compare if two strings have the same content. (Why?))

c) `char *string_in_string(char *needle, char *haystack)` return a pointer to the first character of the first occurance of `needle` in `haystack`, if found.  If `needle` does not occur anywhere in `haystack`, return NULL.  If `needle` is an empty string, `haystack` is returned.
