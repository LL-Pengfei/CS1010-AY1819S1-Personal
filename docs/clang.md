# CS1010 Compilation Guide

## 1. Compile a standalone C program

Suppose we have a standalone C program `teh.c` that does not use any external libraries.  We can compile the program using the command

```
happytan@host:~[xxx]$ clang teh.c
```

This command should create an executable called `a.out` in the current directory, which you can then run with:

```
happytan@host:~[xxx]$ ./a.out
```

## 2. Renaming executable file

The name `a.out` is an abbreviation for _assembler output_, a name that many compilers kept as the default output name since the 60s.  We should, however, give our executable more descriptive name, by using the `-o` flag.  (`o` is the mnemonic for output).

```
happytan@host:~[xxx]$ clang teh.c -o teh
```

or

```
happytan@host:~[xxx]$ clang -o teh teh.c
```

The command above would create an executable called `teh`.

!!! warn "Beware of the order"
    If you are not careful and run the following command instead:
	```
	happytan@host:~[xxx]$ clang -o teh.c teh
	```
	`clang` would overwrite your code `teh.c` -- all your hard work will be gone!!

## 3. Warning for possible bugs.

The `clang` checks for syntax errors in your C files -- i.e., things that violate the C syntax rules.  The compiler, however, is smart enough to identify possible bugs -- errors that will cause your program to behave incorrectly, even if the syntax follows C's rules.  You can ask `clang` to warn you about this, using the `-W` flag (`W` is the mnemonic for warning -- note the capital W).  The manual for `clang` lists different types of warnings that `clang` can warn you about.  For simplicity, we will ask `clang` to warn us about everything, by enabling `all` warnings.  The command to do so is:

```
happytan@host:~[xxx]$ clang -Wall teh.c -o teh
```

For beginners, it is _highly recommended_ that you _always_ compile with `-Wall` flag.

## 4. Generating additional information for debugging.

In order to use the debugger `lldb` to trace through and debug your program, `clang` needs to generate additional information and store them in the executable file.  We can instruct `clang` to generate them with the flag `-g` (`g` for **g**enerate or debu**g**).  

```
happytan@host:~[xxx]$ clang -Wall -g teh.c -o teh
```

It is recommended that you always compile with `-g` flags during development phase.  If you need to measure the performance (e.g., how fast it runs) of your program or when you are releasing the program to the public, you can remove the `-g` flag and compile with the optimization flags (e.g., `-O`) instead.  

## 5. Linking with standard library.

To link with a standard library, we use the `-l` flag to specify the name of the library to link.  For instance, to link with the C standard math library (abbreviated `m`), you issue the command:

```
happytan@host:~[xxx]$ clang -Wall -g teh.c -o teh -lm
```
