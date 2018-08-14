# Programming Exercise 1

## Question 1: Freezer

(a)

Write a program `freezer1.c` that estimates the temperature (in degree Celsius) in a freezer given the elapsed time (in hours) since a power failure.

Assume this temperature $T$ is given by:

\[
T = \frac{4t^2}{t + 2} - 20;
\]

where $t$ is the time (in hours) since the power failure.

Your program reads in an integer: the number of hours since the start of the power failure.
Call your executable `freezer1`.

### Sample run:
```bash
ooiwt@xcna1$ freezer1 
0
-20.0000
ooiwt@xcna1$ freezer1 
1
-18.6667
```

(b)
`freezer1` is rather restrictive, as we can only calculate the temperature after one hour, two hours, etc.  The equation given above works for fractional hours as well.  Modify your program, call it `freezer2.c`, so that it now reads in two integers: the number of hours and the number of additional minutes since the start of the power failure.  For example, to calculate the temperature after 2 hours and 45 minutes of power failure:

```bash
ooiwt@xcna1$ freezer2
2 45
-13.6316
```

## Question 2: Invest

If you invest $m$ dollars at $r$% interest rate compounded annually, after $n$ years, your investment will grow to $\frac{m ( 1 - (r/100)^{n+1}}{1 - r/100}$ dollars.

Write a program `invest.c` that accepts three integers input: principal $m$, rate $r$, and number of years $n$, and computes the amount of money earned after $n$ years.

You may assume that $r \le 100$.

### Sample Run
```bash
ooiwt@xcna1$ invest
100 10 5
111.1110
ooiwt@xcna1$ invest
20000 5 10
21052.6316
```
