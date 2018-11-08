# Assignment 6: Comments

## Notes on Marking Schemes

For brevity, I won't repeat the detailed marking scheme, as it is the same as before.  One major change is that we start to deduct marks for documentation format errors.

The other major change is that we starts to award marks based on efficiency of the solution.

### Permutation 1

If the code does not produce the correct output, 0 marks for both efficiency and correctness.

If the code produces the correct output, give up to 3 marks for correctness. If the solution is in O(n^2), then add 1 marks for efficiency. If it is slower than O(n^2), 0 marks for efficiency.

### Permutation 2

If the code does not produce the correct output, 0 marks for both efficiency and correctness.

If the code produces the correct output, then give up to 3 marks for correctness. If the solution is in O(n), then add 5 marks for efficiency. If it is slower than O(n), 0 marks for efficiency.

If the code does unnecessary or repetitive work (e.g., compute strlen repeatedly even if the string does not change). We may deduct 1 mark per occurance of such inefficient code even if the code still runs in the same big O.

### Permutation 3

If the code does not produce the correct output, 0 marks for both efficiency and correctness.

If the code produces the correct output, then give up to 5 marks for correctness. Maximum marks for efficiency: 2 for O(nk^2) solution, 4 for O(nk) and 7 for O(n+k).

Same as before, if students are doing unnecessary work or repetitive work (e.g., compute strlen repeatedly even if the string does not change). You can deduct 1 mark per occurance of such inefficient code even if the code still runs in the same big O (they have been told).

## Answer Keys

### Permutation 1

This is an easy question, since the limit for this is $O(n^2)$.  There are many different ways to solve this.  A solution is to sort the input arrays using either selection sort, bubble sort, or insertion sort, then checks if both arrays are equal.  If you know of a faster sort (quick sort or merge sort -- CS2040C stuff) then you can actually solve this in $O(n \log n)$ time.

Another approach is the following: for every element in $L_1$, look for it in $L_2$, then remove the matched element from the $L_2$.  If we can find every element of $L_1$ in $L_2$, then they are permutation of each other.

The tricky part here is how to remove the matched element in $L_2$.  If you remove the matched element by shifting the elements in $L_2$ to "cover the hole", you will end up with an $O(n^3)$ algorithm.  You might also want to erase the element by setting the element to 0 or -1 (similar to `mastermind`).  But, since 0 and -1 are both valid inputs, we can't distinguish between an erased element and a valid input in this case.   A better way is to move the matched element to the end of the array (similar to selection sort) and don't check it again.  

Some students take the solution for Permutation 2 and applies it to Permutation 1.  This does not work, since for Permutation 2 the range of the elements is small (lowercase alphabets), while for Permutation 1, it's all possible integers!  

### Permutation 2

We can solve Permutation 2 using the same solution as Permutation 1 -- sorting the input arrays and then checking if they are equal.  But since the range of the elements is small (only 26 possibilities), we can use counting sort.  Actually, we do not even need to sort!  We just count how many times each alphabet appears in $S_1$ and in $S_2$.  If every alphabet appears the same number of times in $S_1$ and in $S_2$, then $S_2$ is a permutation of $S_1$.

```C
/**
 * Initialize a frequency array counting how many times each letter
 * appears in a string.
 *
 * @param[in] len The length of the string.
 * @param[in] s The string
 * @param[out] freq The frequency array.
 */
void build_frequency_array(long len, const char s[len], long freq[26]) {
  for (long i = 0; i < 26; i += 1) {
    freq[i] = 0;
  }
  for (long i = 0; i < len; i += 1) {
    freq[s[i]-'a'] += 1;
  }
}

/**
 * Compares if two strings are permutation of each other, by
 * checking their frequency array.
 *
 * @param[in] freq1 The frequency array for the first string.
 * @param[in] freq2 The frequency array for the second string.
 *
 * @return true if the two strings contains the same frequency
 *         array, false otherwise.
 */
bool is_permutation(const long freq1[26], const long freq2[26]) {
  for (long i = 0; i < 26; i += 1) {
    if (freq1[i] != freq2[i]) {
      return false;
    }
  }
  return true;
}
```

### Permutation 3

This is a more interesting and challenging question.  Let's see first how we can solve this naively.  A straightforward method is to find all possible substring of length $k$ from $S_2$, and then use the solution to Permutation 1 to solve it.  We have to check $O(n-k) = $O(n)$ such possible subtrings, and each check takes $O(k^2)$ time.  

If we use the solution of Permutation 2 to check each substring, each check takes $O(k)$ time only, and so the total running time is $O(nk)$.  Doing so would already give you 11 out of 14 marks!

To get $O(n + k)$ time, we need a bit of analyze to avoid redundant work.  Let's think about how we find all possible substring of length $k$ from a string `s`.  We can scan from left to right, checking substring `s[0]..s[k-1]`, then `s[1]..s[k]`, then `s[2]..s[k+1]`.  Remember that, from the solution of Permutation 2, "checking" means that we count how many times each alphabet appears in the string `s[0]..s[k-1]`, in the string `s[1]..s[k]` etc.  But do we really need to to re-count everything everytime we check a new substring?  The principle of avoiding repetitive work applies here -- the two substrings `s[0]..s[k-1]` and `s[1]..s[k]` share a common substring `s[1]..s[k-1]` so we do not really need to count those alphabets again!

As such, we can just update incrementally the number of occurances of each alphabet, by decrementing the count for `s[0]`, and incrementing the count for `s[k]`.  As we move from `s[1]..s[k]` to `s[2]..s[k+1]`, we decrement the count for `s[1]` and increment the count for `s[k+1]`, etc.  This can be done in constant time (irrespective of $k$ and $n$), or $O(1)$ time.

So now, we can start by setting up an array counting how many times each of the $k$ characters appear -- this takes $O(k)$ time.  Then scan through $O(n)$ substring, for each one update the counting array and compare in $O(1)$ time.

```C
/**
 * Update the frequency table for a new subtring of length k of s2,
 * starting from position start.
 *
 * @pre start >= 1 && start <= n - k.  The input string s2 contains 
 *      only 'a'-'z'.
 * @param[in,out] freq The frequency table to update.
 * @param[in] s2 The string.
 * @param[in] start The beginning position of the substring.
 * @param[in] k The length of the substring.
 */
void update_frequency(long freq2[26], const char *s2, int start, int k) {
  freq2[s2[start-1]-'a'] -= 1;
  freq2[s2[start+k-1]-'a'] += 1;
}

/**
 * Find if s1 is a permutation of a substring of s2.
 *
 * @param[in] s1 The (shorter) string.
 * @param[in] s2 The string to search for a permutation of s1 in.
 *
 * @return true if s1 is a permutation of a substring of s2.
 */
bool find_permutation(char *s1, char *s2) {
  long k = strlen(s1);
  long n = strlen(s2);

  long freq1[26];
  long freq2[26];

  build_frequency_array(k, s1, freq1);
  build_frequency_array(k, s2, freq2);

  if (is_permutation(freq1, freq2)) {
    return true;
  }

  for (long start = 1; start <= n - k; start += 1) {
    update_frequency(freq2, s2, start, k);
    if (is_permutation(freq1, freq2)) {
      return true;
    }
  }
  return false;
}

```
