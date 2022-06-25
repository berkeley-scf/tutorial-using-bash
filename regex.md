---
title: Regular expressions
layout: default
---

Regular expressions (regex) are a domain-specific language for finding
patterns and are one of the key functionalities in scripting languages
such as Python, as well as the UNIX utilities `sed`, `awk`, and
`grep`. We'll just cover the basic use of regular
expressions in bash, but once you know that, it would be easy to use
them elsewhere (Python, R, etc.). At the level we'll consider them, the
syntax is quite similar.

> **Warning**
>
> POSIX.2 regular expressions come in two flavors: extended regular
> expressions and basic (or obsolete) regular expressions. The extended
> syntax has metacharacters `()` and `{}`, while the basic syntax
> requires the metacharacters to be designated `\(\)` and `\{\}`.
> In addition to the POSIX standard, Perl regular expressions are also
> widely used. While we won't go into detail, we will see some examples
> of each syntax. In the examples that follow we'll generally use the
> extended syntax by using the `-E` flag to `grep`.

# 1 Overview and core syntax

The basic idea of regular expressions is that they allow us to find
matches of strings or patterns in strings, as well as do substitution.
Regular expressions are good for tasks such as:

-   extracting pieces of text - for example finding all the phone
    numbers in a document;
-   creating variables from information found in text;
-   cleaning and transforming text into a uniform format;
-   mining text by treating documents as data; and
-   scraping the web for data.

Regular expressions are constructed from three things:

1.  *Literal characters* are matched only by the characters themselves,
2.  *Character classes* are matched by any single member in the class,
    and
3.  *Modifiers* operate on either of the above or combinations of them.

Note that the syntax is very concise, so it's helpful to break down
individual regular expressions into the component parts to understand
them. Since regex are their own language, it's a good idea to build up a
regex in pieces as a way of avoiding errors just as we would with any
computer code. You'll also want to test your regex on examples, for which
this [online tool](https://regex101.com) is helpful.

It is also helpful to search for common regex online
before trying to craft your own. For instance, if you wanted to use a
regex that matches valid email addresses, you would need to match
anything that complies with the [RFC
822](http://www.ietf.org/rfc/rfc0822.txt?number=822) grammar. If you
look over that document, you will quickly realize that implementing a
correct regular expression to validate email addresses is extremely
complex. So if you are writing a website that validates email addresses,
it is best to look for a bug-vetted implementation rather than creating
your own.

The special characters (meta-characters) used for defining regular
expressions are:

    * . ^ $ + ? ( ) [ ] { } | \

To use these characters literally as characters, we have to 'escape'
them. In bash, you escape these characters by placing a single backslash
before the character you want to escape. In R, we have to use two
backslashes instead of a single backslash because R uses a single
backslash to symbolize certain control characters, such as `\n` for
newline.

To learn more about regular expressions, you can type:

```bash
$ man 7 regex
```

# 2 Character sets and character classes

We can use character sets to match any of the characters in a set.

<table>
<thead>
<tr class="header">
<th align="left">Operators</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><code>[abc]</code></td>
<td align="left">Match any single character from from the listed characters</td>
</tr>
<tr class="even">
<td align="left"><code>[a-z]</code></td>
<td align="left">Match any single character from the range of characters</td>
</tr>
<tr class="odd">
<td align="left"><code>[^abc]</code></td>
<td align="left">Match any single character not among listed characters</td>
</tr>
<tr class="even">
<td align="left"><code>[^a-z]</code></td>
<td align="left">Match any single character not among listed range of characters</td>
</tr>
<tr class="odd">
<td align="left"><code>.</code></td>
<td align="left">Match any single character except a <em>newline</em></td>
</tr>
<tr class="even">
<td align="left"><code>\</code></td>
<td align="left">Turn off (escape) the special meaning of a metacharacter</td>
</tr>
</tbody>
</table>

If we want to search for any one of a set of characters, we use a
character set, such as `[13579]` or `[abcd]` or `[0-9]` (where the dash
indicates a sequence) or `[0-9a-z]`. To indicate any character not in a
set, we place a `^` just inside the first bracket: `[^abcd]`.

Here's an example of using regex with `grep` to find all lines in `test.txt` that
contain at least one numeric digit.

```bash
$ grep -E [0-9] test.txt     
```

or with the `-o` flag to find and return only the actual digits

```bash
$ grep -E -o [0-9] test.txt     
```

There are a bunch of named character classes so that we don't have write
out common sets of characters. The syntax is `[:CLASS:]` where `CLASS`
is one of the following values:

    "alnum", "alpha", "ascii", "blank", "cntrl", "digit", "graph",
    "lower", "print", "punct", "space", "upper", "word" or "xdigit".

So to find any line that contains a punctuation symbol:

```bash
$ grep -E [[:punct:]] test.txt
```

Note that to make a character set with a character class you need two square
brackets, e.g., with the digit class: `[[:digit:]]`. Or we can make a combined
character set such as `[[:alnum:]_]` (to find any alphabetic or
numeric characters or an underscore).
Or here, any line with a digit, a period, or a comma.

```bash
$ grep -E [[:digit:].,] test.txt
```

Interestingly, we don't need to escape the period or comma inside the
character set, despite both of them being meta-characters.


# 3 Location-specific matches

We can use position anchors to make location-specific matches.

<table>
<thead>
<tr class="header">
<th align="left">Operators</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><code>^</code></td>
<td align="left">Match the beginning of a line.</td>
</tr>
<tr class="even">
<td align="left"><code>$</code></td>
<td align="left">Match the end of a line.</td>
</tr>
</tbody>
</table>

To find a pattern at the beginning of the string, we use `^` (note this
was also used for negation, but in that case occurs only inside square
brackets) and to find it at the end we use `$`.

Here we'll search for lines that start with a digit and for lines that
end with a digit.

```bash
$ grep -E ^[0-9] test.txt
$ grep -E [0-9]$ test.txt
```

# 4 Repetitions, Grouping, and References

Now suppose I wanted to be able to detect phone numbers, email
addresses, etc. I often need to be able to deal with repetitions of
characters or character sets.

**Modifiers**

<table>
<thead>
<tr class="header">
<th align="left">Operators</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><code>*</code></td>
<td align="left">Match zero or more instances of the preceding character or <em>regex</em>.</td>
</tr>
<tr class="even">
<td align="left"><code>?</code></td>
<td align="left">Match zero or one instance of the preceding character or <em>regex</em>.</td>
</tr>
<tr class="odd">
<td align="left"><code>+</code></td>
<td align="left">Match one or more instances of the preceding character or <em>regex</em>.</td>
</tr>
<tr class="even">
<td align="left"><code>{n,m}</code></td>
<td align="left">Match a range of occurrences (at least n, no more
than m) of preceding character
of <em>regex</em>.</td>
</tr>
<tr class="even">
<td align="left"><code>|</code></td>
<td align="left">Match the character or expression to the left or right of the vertical bar.</td>
</tr>
</tbody>
</table>

Here are some examples of repetitions:

-   `[[:digit:]]*` : any number of digits (zero or more)
-   `[[:digit:]]+` : at least one digit
-   `[[:digit:]]?` : zero or one digits
-   `[[:digit:]]{1,3}` : at least one and no more than three digits
-   `[[:digit:]]{2,}` : two or more digits

Another example is that `\[.*\]` is the pattern of closed square brackets
with any number of characters (`.*`) inside:

```bash
﻿$ grep -E "\[.*\]" test.txt
```

Note that the quotations ensured that the backslashes are passed into
grep and not simply interpreted by the shell, while the `\` is needed
so that `[` and `]` are treated as simple characters since they are
meta-characters in the regex syntax.

As shown above, we can use `|` to mean "or". For example, to match one or
more occurrences of "http" or "ftp":


```bash
$ grep -E -o "(http|ftp)" test.txt
```

Parentheses are also used with a pipe (`|`) when working with
multi-character sequences, such as `(http|ftp)`. Also, here we need double
quotes or the shell tries to interpret the `(` as part of the regular
expression and not shell syntax.

Next let's see the use of repitition to look for more complicated multi-character patterns. For
example, if you wanted to match phone numbers whether they start with
`1-` or not you could use the following:

    (1-)?[[:digit:]]{3}-[[:digit:]]{3}-[[:digit:]]{4}

The first part of the pattern `(1-)?` matches 0 or 1 occurrences of
`1-`. Then the pattern `[[:digit:]]{3}` matches any 3 digits. Similarly,
the pattern `[[:digit:]]{4}` matches any 4 digits. So the whole pattern
matches any three digits followed by `-`, then another three digits, and then followed by four
digits when it is preceded by 0 or 1 occurrences of `1-`.

Now let's consider a file named `file2.txt` with the following content:

```
    Here is my number: 919-543-3300.
    hi John, good to meet you
    They bought 731 bananas
    Please call 1.919.554.3800
    I think he said it was 337.4355
```
	
Let's use a regular expression pattern to print all lines
containing phone numbers:

```bash
$ grep '(1-)?[[:digit:]]{3}-[[:digit:]]{4}' file2.txt
```

You will notice that this doesn't match any lines. The reason is that
the group syntax `(1-)` and the `{}` notation are not part of the
extended syntax. To have `grep` use the extended syntax, you can either
use the `-E` option (as we've been doing above):

```bash
$ grep -E '(1-)?[[:digit:]]{3}-[[:digit:]]{4}' file2.txt
Here is my number: 919-543-3300.
```

or use the `egrep` command:

```bash
$ egrep  '(1-)?[[:digit:]]{3}-[[:digit:]]{4}' file2.txt
Here is my number: 919-543-3300.
```

If we want to match regardless of whether the phone number is separated
by a minus `-` or a period `.`, we could use the pattern `[-.]`:

```bash
$ egrep  '(1[-.])?[[:digit:]]{3}[-.][[:digit:]]{4}' file2.txt
Here is my number: 919-543-3300.
Please call 1.919.554.3800
I think he said it was 337.4355
```


**Exercise**

Explain what the following regular expression matches:

```bash
$ grep '^[^T]*is.*$' file1.txt
```


# 5 Greedy matching

Regular expression pattern matching is *greedy*---by default, the
longest matching string is chosen.

Suppose we have the following file:

    $ cat file1.txt
    Do an internship <b> in place </b> of <b> one </b> course.

If we want to match the html tags (e.g., `<b>` and `</b>`, we might be
tempted to use the pattern `<.*>`. Using the `-o` option to grep, we can
have grep print out just the part of the text that the pattern matches:

    $ grep -o "<.*>" file1.txt
    <b> in place </b> of <b> one </b>

To get a non-greedy match, you can use the modifier `?` after the
quantifier. However, this requires that we use the Perl syntax. In order
for grep to use the Perl syntax, we need to use the `-P` option:

    $ grep -P -o "<.*?>" file1.txt
    <b>
    </b>
    <b>
    </b>

However, one can often avoid greedy matching by being more clever.

**Challenge**: How could we change our regexp to avoid the greedy
matching without using the `?` modifier? Hint: Is there some character
set that we don't want to be inside the angle brackets?

> **tip**
>
> **Globs vs. Regex:**
>
>     Be sure you understand the difference between filename globbing
>     (see the Section called *Filename Globbing*) and regular
>     expressions. Filename globbing only works for filenames, while
>     regular expressions are used to match patterns in text more
>     generally. While they both use the same set of symbols, they mean
>     different things (e.g., `*` matches 0 or more characters when
>     globbing but matches 0 or more repetitions of the character that
>     precedes it when used in a regular expression).
>
