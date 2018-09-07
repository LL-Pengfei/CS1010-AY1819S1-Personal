# Unit 12: Reasoning About Loops

## Using Assertions
We have seen how we can use assertions to reason about the state of our program at different points of execution for conditional `if`-`else` statements.  We can apply the same techniques to loops.  Take the simple program below:

```C
long count(long n) {
  long y = 0;
  long x = n;
  while (x > 0) {
    // line A
    x -= 1;
    // line B
    if (x % 5 == 0) {
      // line C
      y += 1;
    }
  }
  // line D
  return y;
}
```

Before we continue, study this program and try to analyze what the function is counting and returning.

To do this more systematically, we can use assertions.  Let's ask ourselves: what can be said about the variables `x` and `y` at Lines A, B, C?  Let starts with `x` first.

- Line A is the first line after entering the loop, so we can reason that to enter the loop (the first time or subsequent times), `x > 0` and `x <= n` (since we initialize the `x` to `n`).
- At Line B, we decrease `x` by 1, so `x >= 0 && x < n` must be true.
- At Line C, `x % 5 == 0` (i.e., x is multiple of 5) must _also_ be true (since it is in the true block of the `if` block).
- At Line D, we already exit from the loop, and the only way to exit here is that `x > 0` is false.  So we know that `x <= 0`.

Let's annotate the code with the assertions:
```C
long count(long n) {
  long y = 0;
  long x = n;
  while (x > 0) {
    // { x > 0 && x <= n }
    x -= 1;
    // { (x >= 0) && (x < n) }
    if (x % 5 == 0) {
      // { (x >= 0) && (x < n) && x is multiple of 5 }
      y += 1;
    }
  }
  // { x <= 0 }
  return y;
}
```

What can be said about `y`?  It should be clear now that we increment `y` for every value between 0 and `n-1` (inclusive) that is a multiple of 5, based on the condition on Line C.  That is, it is counting the number of multiple of 5s between 0 and n-1.

## Loop Invariant

In the last unit, we say that there are actually five questions that we have to think about when designing loops.  The fifth question is: what is the _loop invariant_?  A loop invariant is an assertion that is true before the loop, during the loop, and after the loop.  Thinking about the loop invariant is helpful to convince ourselves that a loop is correct, or to identify bugs in a loop.

Let's see an example of a loop invariant.  Consider the example of calculating a factorial using a loop as before.  To make the invariant simpler, let's tweak the loop slightly and start looping from `i` equals 1 up to `n`.

```C
long factorial(long n) 
{
  if (n == 0) {
      return 1;
  }
  long product = 1;
  int i = 1;
  // Line A
  while (i < n)
      i += 1;
      product *= i;
    // Line B
  }
  // Line C
  return product;
}
```

The loop invariant for each line A, B, and C are the same: 

```C
long factorial(long n) 
{
  if (n == 0) {
      return 1;
  }
  long product = 1;
  int i = 1;
  // A: { product == i! }
  while (i < n)
      i += 1;
      product *= i;
    // B: { product == i! }
  }
  // C: { product == i! }
  return product;
}
```

In Line A, the assertion is obvious.  Let's look at Line B.  Since, at the beginning of the loop (before Line 10) we have `product == i!`, after Line 10, we have `product == (i-1)!` (since we have incremented `i`).  After Line 11, we have `product == i * (i - 1)! == i!` again.  The assertion remains true once we exit the loop.

The key here is that after we exit the loop, we can also assert that `i == n`, and so combining `product == i! && i == n` we have `product == n!`, which is what we want.

## Problem Set 12

### Problem 12.1

```C
long i = 10;
long j = 0;
while (i != 0) {
    i -= 1;
    j += 1;
}
```

(a) Trace through the program.  What is the value of `j` when the loop exits?
(b) Do you recognize any pattern on the relationship of `i` and `j`?
(c) What is the loop invariant?
