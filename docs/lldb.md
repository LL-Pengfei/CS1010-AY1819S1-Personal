# Using LLDB Debugger

## What is a debugger?

A debugger is a tool that allows a programmer to examine the states of the execution of the program, while the program is executing.  It allows a programmer to trace through the program step-by-step, examine the flow of the execution and the values of the variables.  It is a tremendously valuable and powerful tool to help us trace through our code to understand the behavior and find bugs.

In CS1010, we will use `lldb`, a debugger that comes from the same project as `clang`.  

## Starting Up LLDB

To use `lldb`, you first make sure that your code is compiled with `clang` using the flag `-g`.  This should be done for you already if you use the `Makefile` that we provide you for the assignments and exercises.  

Then, you invoke `lldb` with the name of your executable as an argument.  For instance,
```
ooiwt@pe113:~$ lldb social
(lldb) target create "social"
Current executable set to 'social' (x86_64).
(lldb)
```

You should see the following message, showing the `social` is loaded as the current executable, and you will be brought to a prompt that says `(lldb) `.

At any time, you can type `help` on the prompt to see the list of commands, or type `help <command-name>` to get help for a particular command.

As we use `lldb`, we are going to be bombarded with a lot of information about the internals of the program, so things might seem scary and you might be overwhelmed.  But, not too worry.  We only need to look for information that is useful to help you debug your program.  We can ignore other unnecessary details that `lldb` provide us.

## Running the Executable

To run the executable, you need to launch the process[^1]

```
(lldb) process launch
```

!!! note "Save on Typing"
    This is too long to type.  There are multiple ways to save typing.  Using <UP ARROW> to repeat previous commands is one.  The other is to use <TAB> to autocomplete.  You can also just type the prefix -- if it is unique enough, `lldb` can figure out what you want to do.  For instance, the following is the same as `process launch` since no other commands have the same prefix.
    ```
    (lldb) pr la
    ```

[^1]: A _process_ is the technical term for a running application -- it is more complicated than this, but this intuition will do for now.  You will learn what a process is in your OS module.

Launching the process is the same as running the executable on the command line.  If the program is reading from standard inputs, you should type in your inputs, after that the executable will run as per normal.

If you want to redirect the input from a file (like `./social < inputs/social.1.in`), you will need to pass in the `-i` flag to `process launch`:

```
(lldb) process launch -i inputs/social.1.in
```

This will cause the executable to read directly from the given input file.  An example output is as follows:

```
(lldb) process launch -i inputs/social.1.in
Process 29181 launched: '/home/o/ooiwt/as05-weitsang/social' (x86_64)
1
11
011
NO
Process 29181 exited with status = 0 (0x00000000)
```

So far, nothing interesting happens.  Running the executable from `lldb` is the same as running it from `bash` command line.

## Setting Breakpoint and Examining States

Let's do something more interesting.  Suppose that my program is giving us the wrong answer, and I suspect that there is a bug in the function `is_friend`.  I can set a _breakpoint_.  A breakpoint is a particular point in the code where you want to execution to pause.  Once the program pauses, you can then examine the state of the program.  You can set a breakpoint by specifying the line number or by the function name.  

To set a breakpoint a function `is_friend`, I can do the following:

```C
(lldb) breakpoint set -n is_friend
Breakpoint 1: where = social`is_friend + 14 at social.c:13, address = 0x0000000000400a6e
```

A short form is `b is_friend`:
```C
(lldb) b is_friend
Breakpoint 1: where = social`is_friend + 14 at social.c:13, address = 0x0000000000400a6e
```

After setting the breakpoint, if I run the program again, I get
```
(lldb) b is_friend
Breakpoint 1: where = social`is_friend + 14 at social.c:13, address = 0x0000000000400a6e
(lldb) process launch -i inputs/social.2.in
Process 29596 launched: '/home/vagrant/cs1010/as05-solution/social' (x86_64)
Process 29596 stopped
* thread #1: tid = 29596, 0x0000000000400a6e social`is_friend(socnet=0x0000000000605070, i=0, j=0) + 14 at social.c:13, name = 'social', stop reason = breakpoint 1.1
    frame #0: 0x0000000000400a6e social`is_friend(socnet=0x0000000000605070, i=0, j=0) + 14 at social.c:13
   10      }
   11
   12      bool is_friend(char **socnet, int i, int j) {
-> 13        if (i >= j) {
   14          return socnet[i][j] == '1';
   15        }
   16        return socnet[j][i] == '1';
```

At this point, the execution has paused, on Line 13 above.  We can now examine the state of the program.
The line
```
    frame #0: 0x0000000000400a6e social`is_friend(socnet=0x0000000000605070, i=0, j=0) + 14 at social.c:13
```

shows the value of the parameters passed into `is_friend`.  `socnet` is a pointer to a memory location (shown in hexadecimal format).  `i` and `j` are both zeros.

### Checking the Value of A Variable

```
(lldb) print i
(int) $0 = 0
(lldb) print j
(int) $1 = 0
(lldb) print socnet
(char **) $2 = 0x0000000000605070
```

We can use the `print` (or `p`) command to display the value of a variable.  In the examples above, I print out the value for `i`, `j`, `socnet`.   The type, a temp variable name (`$0`, `$1`, etc), and the value of the variable are shown as output.

In my program, `socnet` is the social network.  Suppose I want to know what is the value stored in different locations of this matrix, I can do the following:

```
(lldb) p socnet[0][0]
(char) $3 = '1'
(lldb) p socnet[0][1]
(char) $4 = '\0'
(lldb) p socnet[1][1]
(char) $5 = '1'
(lldb) p socnet[1]
(char *) $8 = 0x0000000000605150 "01"
(lldb) p socnet[3]
(char *) $10 = 0x0000000000605210 "1011"
```

### Checking the Content of the Stack

```
(lldb) bt
(lldb) bt
* thread #1: tid = 29596, 0x0000000000400a6e social`is_friend(socnet=0x0000000000605070, i=0, j=0) + 14 at social.c:13, name = 'social', stop reason = breakpoint 1.1
  * frame #0: 0x0000000000400a6e social`is_friend(socnet=0x0000000000605070, i=0, j=0) + 14 at social.c:13
    frame #1: 0x0000000000400b1f social`expand(n=4, socnet=0x0000000000605040, degree_k=0x0000000000605070, result=0x00000000006050a0) + 79 at social.c:22
    frame #2: 0x0000000000400e7d social`main + 301 at social.c:79
    frame #3: 0x00007ffff7724830 libc.so.6`__libc_start_main(main=(social`main at social.c:60), argc=1, argv=0x00007fffffffe578, init=<unavailable>, fini=<unavailable>, rtld_fini=<unavailable>, stack_end=0x00007fffffffe568) + 240 at libc-start.c:291
    frame #4: 0x0000000000400949 social`_start + 41
```

The `bt`, or `backtrace` command shows all the call frames on the call stack.  `frame #0` is the current frame, where the breakpoint is.  `frame #1` is the function that calls `is_friend`.  You can ignore `frame #3` and `frame #4` in the example above, as they are relevant to how the OS invokes your executable.   So you can see the calls above: `main` calls `expand, which then calls `is_friend`.  

In the example above, we can see that the function `expand` takes in four parameters.  What if I want to example what are these four parameters?  Now, I am at the frame `is_friend`.  If I try to print `k` or `degree_k`, I will get an error:

```
(lldb) p degree_k
error: use of undeclared identifier 'degree_k'
error: 1 errors parsing expression
```

To examine the variables of another frame, we can use the `up` command, to move up the call frame (but, actually, we are moving down the stack).

```
(lldb) up
frame #1: 0x0000000000400b1f social`expand(n=4, socnet=0x0000000000605040, degree_k=0x0000000000605070, result=0x00000000006050a0) + 79 at social.c:22
   19      void expand(long n, char **socnet, char **degree_k, char **result) {
   20        for (int i = 0; i < n; i += 1) {
   21          for (int j = 0; j <= i; j += 1) {
-> 22            if (is_friend(degree_k, i, j)) {
   23              result[i][j] = '1';
   24            } else {
   25              bool find_connection = false;
```

This shows us where exactly is `is_friend` called. And now we can examine the content of `socnet` in this frame.

```
(lldb) p socnet[0][0]
(char) $11 = '1'
```

To easily see what are the variables in the current frame, you can also run the commend
```
(lldb) frame variable
(long) n = 4
(char **) socnet = 0x0000000000605040
(char **) degree_k = 0x0000000000605070
(char **) result = 0x00000000006050a0
(int) i = 0
(int) j = 0
```

To move back to the earlier call frame, you can run `down`:
```
(lldb) down
frame #0: 0x0000000000400a6e social`is_friend(socnet=0x0000000000605070, i=0, j=0) + 14 at social.c:13
   10      }
   11
   12      bool is_friend(char **socnet, int i, int j) {
-> 13        if (i >= j) {
   14          return socnet[i][j] == '1';
   15        }
   16        return socnet[j][i] == '1';
```

And now we are back in `is_friend`.

## Executing the Program Line-by-Line

To continue the execution of the program, we can run `next` (or `n`), which cause the execution to continue, in one line:

```
(lldb) n
Process 29596 stopped
* thread #1: tid = 29596, 0x0000000000400a7a social`is_friend(socnet=0x0000000000605070, i=0, j=0) + 26 at social.c:14, name = 'social', stop reason = step over
    frame #0: 0x0000000000400a7a social`is_friend(socnet=0x0000000000605070, i=0, j=0) + 26 at social.c:14
   11
   12      bool is_friend(char **socnet, int i, int j) {
   13        if (i >= j) {
-> 14          return socnet[i][j] == '1';
   15        }
   16        return socnet[j][i] == '1';
   17      }
```

So now we see that the control flow actually cause the execution of Line 14 -- that is, it enters the true-block of the `if` statement.  We can keep typing `n` to continue to trace through the code, line-by-line.

If the `next` command, encounters a function, it will go into the function.  Suppose you are not interested in tracing through the code in a particular function, line-by-line, you can use the `step` command (or `s`) to step over a function.  

The `continue` command, or `c`, would cause lldb to continue (unpause) the execution.  The execution continues until the next breakpoint is encountered.

## Pausing with Conditions

### Conditional Breakpoint

Suppose that I now suspect there is a problem with `is_friend` with j is 2.  Setting the breakpoint at `is_friend` is not going to help much, as it pauses the program _every time_ `is_friend` is called.  Since this is inside a double for loop, it is going to take multiple stops at this breakpoint before I read that state that I am interested in.  

To get around this, we can set a conditional breakpoint -- a breakpoint that will pause the program, only if the condition is true.  The command to do so is as follows:

```
(lldb) b -n is_friend -c 'j == 2'
Breakpoint 15: where = social`is_friend + 14 at social.c:13, address = 0x0000000000400a6e
```

This command sets a breakpoint at the function `is_friend`, but only when the condition `j == 2` is true.

```
(lldb) process launch -i inputs/social.2.in
Process 30796 launched: '/home/vagrant/cs1010/as05-solution/social' (x86_64)
Process 30796 stopped
* thread #1: tid = 30796, 0x0000000000400a6e social`is_friend(socnet=0x0000000000605040, i=1, j=2) + 14 at social.c:13, name = 'social', stop reason = breakpoint 15.1
    frame #0: 0x0000000000400a6e social`is_friend(socnet=0x0000000000605040, i=1, j=2) + 14 at social.c:13
   10      }
   11
   12      bool is_friend(char **socnet, int i, int j) {
-> 13        if (i >= j) {
   14          return socnet[i][j] == '1';
   15        }
   16        return socnet[j][i] == '1';
```

Now, when we run the program, the breakpoint `is_friend` is triggered only when the value of j is 2.
