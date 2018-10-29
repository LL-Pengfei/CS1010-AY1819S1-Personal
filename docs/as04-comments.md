# Assignment 3: Comments

## Mark Distribution

## Notes on Marking Schemes

- We apply the penalty to each program independently, i.e., the same mistake repeated in the different program get penalized multiple times.  
- You will receive 0 marks if your program cannot be compiled or is plagiarised from another.

- Code that fails all test cases and showing that student does not understand the basic concepts receives 0 marks straight away.

- For the rest, we start with full marks and start deducting marks for each error.

- While the teaching team tries to ensure fair and consistent grading of assignments across all groups, each TA can, at his/her own discretion, and with the approval of Wei Tsang, adjust the grading criteria to align with the messages that they sent to the students in their tutorial group.

## Style

Two marks are allocated for each question. We won't penalize for every violation, but instead, the grader judges whether the code is clean, understandable, properly indented, use proper variable names, etc. 0 - 0.5 marks for very bad style, 1.0 - 1.5 for something in the middle, 2 for an ok code. 

To be safe, please follow strictly the CS1010 coding standards.

## Documentation

Two marks are allocated for each question. We do not penalize for formatting errors violation in Assignment 4.  If you did not document your function, parameters, return values, or document wrongly (e.g., confused between `@param[out]` with `@return`, you will get a deduction of 1 mark.

The following are common formatting errors:

- `@param` command used in a comment that is not attached to a function declaration 
- `@return` command used in a comment that is attached to a function returning `void` 
- Parameter name not found in the function declaration.  You should put the name of the variable only, exclude the type, `*`, `[]`.
- Putting `@param` with parameter name, but no description of what the parameter does.
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
- {++new++} -1 for using variable length array
- {++new++} -1 for extra elements in the array initializers
- {++new++} -1 for hardcoding the size of the array, hoping that it is big enough.
- {++new++} -1 for memory leaks

## Logical Mistakes (bugs)

In general, -1 for each bug.

- -1 for each incorrect logical expression
- -1 for each incorrect arithmetic expression
- -1 for each incorrect use of `if`-`else`
- -1 for each uninitialized variables causing incorrect output.
- -1 if program prints extra text / newline
- -1 if the program returns non-zero from `main` even if the program exits successfully.

## Answer Keys

### SelectionSort

This is an easy question, but unfortunately less than half the class received full marks.  Most of these are due to lack of documentation >.<

```C
/**
 * Sort the input array list using selection sort.
 *
 * @param[in] length The size of the array
 * @param[in,out] list The array to sort
 */
void selection_sort(long length, long list[length])
{
  long last = length;
  for (long i = 1; i <= length - 1; i += 1) {
    long curr_pos = last - 1;
	// Find the position of the max number between list[0]..list[last-1]
    long max_pos = max(last, list);
    if (max_pos != curr_pos) {
      long temp = list[max_pos];
      list[max_pos] = list[curr_pos];
      list[curr_pos] = temp;
    }
    print(length, list);
    last -= 1;
  }
}
```

The only common mistake we see here is that the `max` function does not handle negative values.  

### Add

The most common mistakes is accessing memory locations outside of the bound of the array.  This is usually due to careless mistakes (forgetting to allocate an extra space for null character at the end of string, or forgetting to allocate the extra carry).

I have seen lots of code that compute the addition in one long chunk of code, making it hard to read, understand, and debug.  Remember to practice decomposition -- breaking down the problem into smaller subproblems, and solve each one by writing a small function.

For me, I broke down the tasks of adding two arbitrary large integers to adding two digits (with a possible carry):

```C
**
 * Perform a single digit addition.  The digits are stored in
 * char form.
 *
 * @pre a and b can only be '0', '1', .. '9'
 * @param[in]             a The first operand of addition (in char)
 * @param[in]             b The second operand of addition (in char)
 * @param[in,out] has_carry Whether we should add a carry when adding
 *                          a + b.  If a + b generates a carry, *has_carry
 *                          will be set to true, otherwise to false.
 *
 * @return The least significant digit of a+b (as char)
 */
char add_digit(char a, char b, bool *has_carry)
{
  long sum = (a - '0') + (b - '0');
  char result;
  if (*has_carry) {
    sum += 1;
  }
  if (sum >= 10) {
    result = (sum % 10) + '0';
    *has_carry = true;
  } else {
    result = sum + '0';
    *has_carry = false;
  }
  return result;
}
```

Then, I add digits by digits, starting from least significant digits.  Some students reverse the strings and add from the front -- which is unnecessary. 

```C
  long len1 = strlen(operand1);
  long len2 = strlen(operand2);
  long max = (len1 > len2) ? len1 : len2;

  char result[max + 2];
  result[max + 1] = '\0';

  // starting from the back, add digit by digit
  long i = len1 - 1;
  long j = len2 - 1;
  long k = max;
  bool has_carry = false;
  while (i >= 0 && j >= 0) {
    result[k] = add_digit(operand1[i], operand2[j], &has_carry);
    i -= 1;
    j -= 1;
    k -= 1;
  }
```

Once I exit the while loop, I have either `i < 0 || j < 0`.  I have exhausted the digits in one of the operands.   I figure out which one, and continue to add 0 to the remaining operand.

```C
// done with the shorter number.  Now handle the rest.
  char *rest;
  if (j >= 0) {
    rest = operand2;
  } else {
    rest = operand1;
    j = i;
  }

  while (j >= 0) {
    result[k] = add_digit(rest[j], '0', &has_carry);
    k -= 1;
    j -= 1;
  }
```

Finally, I check if there is a carry and print out the content accordingly:

```C
 if (has_carry) {
    result[k] = '1';
    cs1010_println_string(result);
  } else {
    cs1010_println_string(&result[1]);
  }
```

Some students store each digit in a `long` or `int`, which makes it easier to handle the final carry.  This approach takes more memory than storing a `char` but is fine.

### MasterMind

One way to solve `mastermind` is to keep a temporary array (I called it `marked`) that stores the same content as `answer` but once we found a match, we "checked off" the character so that id does not match again.

```C
#define MATCHED ' '

  for (long i = 0; i < PUZZLE_SIZE; i += 1) {
    marked[i] = answer[i];
  }
  long same_color_same_pos = 0;
  long same_color_diff_pos = 0;
  for (long i = 0; i < PUZZLE_SIZE; i += 1) {
    if (guess[i] == marked[i]) {
      same_color_same_pos += 1;
      marked[i] = MATCHED;
    }
  }

  for (long i = 0; i < PUZZLE_SIZE; i += 1) {
    bool found = false;
    for (long j = 0; j < PUZZLE_SIZE && !found; j += 1) {
      if (guess[i] != answer[i] && guess[i] == marked[j]) {
        same_color_diff_pos += 1;
        found = true;
        marked[j] = MATCHED;
      }
    }
  }
```

The two output numbers are `same_color_same_pos` and `same_color_diff_pos`.

Despite being asked and clarified on Piazza at least twice, some students still stopped the game after 8 guesses.  Please (i) read the question carefully, and (ii) read Piazza.
