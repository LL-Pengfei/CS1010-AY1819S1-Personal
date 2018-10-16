# Unit 22: Efficiency

We have been writing code and solving problems, focusing on producing the correct output and using the various construct of C language appropriately.  Now, we will introduce a new criteria on writing code: _efficiency_.  

## No Redundant Work

Writing efficient code basically means that we should not write code that runs unnecessarily.  In my comments on your [Assignment 2](https://nus-cs1010.github.io/1819-s1/as02-comments/index.html), I gave you an example where the same function `is_prime` has different implementations -- some runs very slowly, while some runs quickly.   I reproduce them here:

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

In fact, on my machine, the slowest version of `is_prime` took ~100s, while the fast one runs ~3ms, when run with `is_prime(10000000001)`.  The time taken is five order of magnitude difference!  But there is no magic here, as we merely apply the following principle:

_"No redundant work"_

If you recall, we improve the efficiency of `is_prime` by (i) returning `false` as soon as we found a proof that the input is not a prime, and (ii) not checking for divisor that is redundant.

## Worst Case Performance

The two techniques we employed above behave slightly differently.  For (i), we _opportunistically_ stop our computation once we know the answer when the input is not a prime.  However, in the _worst case_, when the input is a prime, we still have to check through all $n-2$ divisor, from 2 up to $n-1$, before we can conclude that the input is a prime.  Thus, for (i), we make our program faster for certain inputs but in the worst case, we are not able to speed up the program.

The second technique is more interesting and more fruitful.  With a little math, we can show that we only need to check for the divisor up to $\sqrt{n}$.  Here, we are improving the _worst case_ performance of `is_prime`, so whether the input is a prime or not, we always have a speed up.  

How much is the speed up in the worse case?  In the input above, with the original slow code, in the worst case I need to check through ~10,000,000,001 divisors.  With the faster version, I only need to check ~$\sqrt{10,000,000,001}$ = ~10,000 divisors.  That's where the five order of magnitude speed up comes from.

## No Repetition

The second principle to improving the efficiency of the program is that _"No repetition"_ -- we should not repeat work that has been done before.

Let's look at the example from earlier this semester, where you are asked to compute the range of a list.  Recall that the range of a list is the absolute difference between the largest element and the smallest element.  We use this problem to motivate the use of function, where we denote $range(L, k) = |max(L, k) - min(L, k)|$.  If we are to implement this solution in C, however, we would end up scanning through the list twice: first to find the max, then to find the min.  

The example code from Lecture 7 has shown that we could easily just go through the list once, use call-by-reference to output both the max and the min, if we are willing to forgo the notion of pure functions and function-as-a-black-box.

In your midterm, you see another example where we can go through the list once to find the second largest element, instead of twice as suggestion: first to find the max, then to find the next largest.

The performance improvement for not scanning through a list (or an array) twice is modest at most.  Let's look at another example where, by not repeating ourselve, we can gain significant performance improvement.

### Finding Fibonacci Numbers

Recall from your Exercise 3, the Fibonacci sequence of numbers are numbers formed by adding the previous two numbers to form the next numbers. In other words, the $i$-th Fibonacci number is computed as the sum of the previous two Fibonacci numbers, the $(i-2)$-th, and the $(i-1)$-th.

Here is one way we can find the Fibonacci numbers with a loop:

```C
long fib(long n)
{
  if (n == 1 || n == 2) {
    return 1;
  }
  long first = 1;
  long second = 1;
  long third = 1;
  for (long i = 2; i != n; i += 1) {
    first = second;
    second = third;
    third = first + second;
  }
  return third;
}
```

To find the $n$-th Fibonacci number, we take $n$ steps in a loop. 

Now, let's see the following elegant recursive solution:
```C
long fib(long n)
{
  if (n == 1 || n == 2) {
    return 1;
  }
  return fib(n-1) + fib(n-2);
}
```

This solution is short and follows directly from the definition of Fibonacci numbers.  Running this, however, reveals something very disturbing.  Let's say we call `fib(n)`.  This invocation in turns calls `fib(n-1)` and `fib(n-2)`.  `fib(n-1)` then calls `fib(n-2)` and `fib(n-3)`.   So, `fib(n-2)` will be called twice -- so we are repeating ourselves, violating the no repetition principles.  In fact, we will invoke `fib()` a large number of times.  


## Big-O Notation

How do we characterize the number of times `fib()` is called?  To answer this, we will introduce to you the Big-O notation.

The Big-O function is a mathematical function that computer scientists use to characterize the time and space efficiency of an algorithm.  There is a formal definition to Big-O, which interested students can find out by Googling, or you can wait until you take CS2040C and CS3230.  In CS1010, we will introduce Big-O using the intuition that it is the "rate of growth" of a function.

To motivate Big-O, let's consider how we can count the number of "steps" taken by an algorithm.  Let's consider this again:

```C
  for (long i = 2; i != n; i += 1) {
    first = second;
    second = third;
    third = first + second;
  }
```

If we consider each of the fundamental operations: comparison, addition, and assignment, as a step, then we can see that, in each loop, there is one comparison (`i != n`), two additions (`i + 1`, `third = first + second`), four assignments.  So we have seven operations per loop, with a total of $n-1$ loops.  So we have $7n - 7$ operations.  In addition, we also need to count for the assignment `i = 2` and the additional comparison before we exit the loop (`i != n`).  So, in total, we have $7n - 5$ operations.

As you can see, such detailed counting of steps in tedious, and in fact, not very meaningful.  For instance, we did not account for reading of values from the memory and writing of values into the memory, the performance of which becomes dependant on the architecture underneath.  

To free us from such low-level accounting of the number of steps, let's focus on the big picture.  No matter how we count the number of steps, in the end, it is a linear function of $n$.  In order words, the number of steps taken by the algorithm to compute Fibonacci in a loop grows linearly with $n$.  Using the Big-O notation, we say that it takes $O(n)$ steps.

Given a mathematical function with multiple terms, the Big-O of this function is obtained by dropping any multiplicative constants and all terms, except for the one with the _highest rate of growth_.  For instance, $O(\frac{n^4}{10} + 10000n^2 - n) = O(n^4)$.

Due to this focus on the term with highest rate of growth, and not bothering about other terms or multiplicative constants, it becomes very convenient for us to expression the time efficiency of an algorithm with $O()$ -- we no longer need to count the steps precisely but just focus on the number of times it takes to run the algorithm in terms of $n$.  

Take the example of `is_prime`.  The slow algorithm takes $O(n)$, the fast algorithm takes $O(\sqrt{n})$.  Take another example: to finding the range of a list, both algorithms, regardless of whether we are taking two passes or one pass, takes $O(n)$ time. 

## Rate of Growth

Given two functions $f(n)$ and $g(n)$, how do we determine which one has a higher rate of growth?  We say that $f(n)$ grows faster than $g(n)$ if we can find a $n_0$, such that $f(n) > cg(n)$ for all $n \ge n_0$ and for some constant $c$.  

For instance, which one grows faster?  $f(n) = n^n$ or $g(n) = 2^n$?  Pick $n = 1$, we have $f(1) < g(1)$[^1].  Pick $n = 2$, we have $f(2)$ equals $g(2)$.  Pick $n = 3$, we have $f(3) > $g(3)$ now, and we can see that for any $n > 3$, $n^n > 2^n$, so we can conclude that $f(n)$ grows faster than $g(n)$.

[^1]: Note that we cannot conclude that $g(n)$ grows faster than $f(n)$ because of this.

## Example: Kendall

Now, consider the algorithm to compute the Kendall-Tau distance.  The code is given below.

```C
long count_inversion(long i, long n, const long rank[n])
{
  long count = 0;
  for (long j = i + 1; j < n; j += 1) {
    if (rank[i] > rank[j]) {
      count ++;
    }
  }
  return count;
}

double kendall_tau(long n, const long rank[n])
{
  long count = 0;
  for (long i = 0; i < n - 1; i += 1) {
    count += count_inversion(i, n, rank);
  }
  return 2.0*count/(n*(n - 1));
}
```

What is the running time of the function `kendall_tau`, expressed in Big-O notation in terms of $n$, the length of the input array?

To analyze the running time, we focus on the big picture, the part of the code that takes the most time, and ignore all the other operations that take negligible time.  In the function `kendall_tau` above, Line 16 is repeated many times, so let's focus on that.  Looking at the looping conditions, we can conclude that Line 16 repeats $O(n)$ times.  

It is tempting to conclude that `kentall_tau` takes $O(n)$ steps here, but it would be wrong.  

Notice that Line 16 calls another function `count_inversion`.  What is the running time of `count_inversion`?  Inside `count_inversion`, there is another loop that repeats $n - i$ times.  Each time we call `count_inversion`, `i` increases, so the loop in `count_inversion` takes fewer steps each time the function is called. 

To calculate the total number of steps, we can compute the following sum 
$sum_{i = 0}^{n} (n - i)$, which is just $n + (n-1) + (n-2) + .. + 2 + 1$.
This sum is the sum of an arithmetic series, and equals to $n(n+1)/2$.   Since we use the Big-O notation, we can focus on the term with the highest rate of growth, $n^2$, and ignore everything else.  We have obtained the running time for `kentall_tau` function above as $O(n^2)$.

## Efficiency in CS1010

In CS1010, we will focus on the efficiency of your code in two sense: First, your code should not perform redundant work and it should not repeat itself unnecessarily.  This criteria is independent of Big-O notation -- i.e., even though by optimizing the performance of your code, you do not change the worst-case Big-O performance, you should still optimize your code that make it run faster.  Second, your algorithm should run within a given Big-O running time.  We will start to impose time limit on the running time of your code, such that if you choose the wrong algorithm with the higher rate of growth, your code would not pass the test.

## Problem Set 22

### Problem 22.1

Order the following functions in the increasing order of rate of growth:

- $n!$, 
- $2^n$, 
- $\log_{10} n$, 
- $\ln n$, 
- $n^4$, 
- $n\ln n$, 
- $n$, 
- $n^2$, 
- $e^n$, 
- $\sqrt{n}$

### Problem 22.2

What is the Big-O running time of the following code, in terms of $n$?

a)

```C
for (long i = 0; i < n; i += 1) {
  for (long j = 0; j < n; j += 2) {
	cs1010_println_long(i + j);
  }
}
```

b)
```C
for (long i = 0; i < n; i *= 2) {
  for (long j = 0; j < n; j *= 2) {
	cs1010_println_long(i + j);
  }
}
```

c)
```C
long k = 1;
for (long j = 0; j < n; j += 1) {
  k *= 2;
  for (long i = 0; i < k; i += 1) {
	cs1010_println_long(i + j);
  }
}
```
