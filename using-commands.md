---
title: Using UNIX commands
layout: default
---

# 1 Basic utilities / commands

[Earlier](index#5-introduction-to-commands) we introduced the basics of entering commands in the shell.


Since files are such an essential aspect of Unix and working from the
shell is the primary way to work with Unix, there are a large number of
useful commands and tools to view and manipulate files.

-   cat -- concatenate files and print on the standard output
-   cp -- copy files and directories
-   cut --_remove sections from each line of files
-   diff-- find differences between two files
-   grep -- print lines matching a pattern
-   head -- output the first part of files
-   find --  search for files in a directory hierarchy
-   less -- opposite of more (and better than more)
-   more -- file perusal filter for crt viewing
-   mv -- move (rename) files
-   nl -- number lines of files
-   paste -- merge lines of files
-   rm -- remove files or directories
-   rmdir -- remove empty directories
-   sort -- sort lines of text files.
-   split -- split a file into pieces
-   tac -- concatenate and print files in reverse
-   tail -- output the last part of files
-   touch -- change file timestamps
-   tr -- translate or delete characters
-   uniq -- remove duplicate lines from a sorted file
-   wc -- print the number of bytes, words, and lines in files
-   wget and curl -- non-interactive network downloader

Recall that a command consists of the command, optionally one or more flags, and optionally one or more arguments. When there is an argument, it is often the name of a file that the command should operate on.

Thus the general syntax for a Unix program/command/utility is:

    $ command -options argument1 argument2 ...

For example, :

    $ grep -i graphics file.txt

looks for the literal string `graphics` (argument 1) in `file.txt`
(argument2) with the option `-i`, which says to ignore the case of the
letters. While :

    $ less file.txt

simply pages through a text file (you can navigate up and down) so you
can get a feel for what's in it. To exit `less` type `q`.

To find files by name, modification time, and type:

    $ find . -name '*.txt'  # find files named *.txt
    $ find . -mtime -2      # find files modified less than 2 days ago
    $ find . -type l        # find links

Unix programs often take options that are identified with a minus
followed by a letter, followed by the specific option (adding a space
before the specific option is fine). Options may also involve two
dashes, e.g., `R --no-save`. A standard two dash option for many
commands is `--help`. For example, try:

    $ tail --help

Here are a couple of examples of using the `tail` command:

    $ wget https://raw.githubusercontent.com/berkeley-scf/tutorial-using-bash/master/cpds.csv
    $ tail -n 10 cpds.csv   # last 10 lines of cpds.csv
    $ tail -f cpds.csv      # shows end of file, continually refreshing

The first line downloads the data from GitHub. The two main tools
for downloading network accessible data from the commandline are `wget`
and `curl`. I tend to use `wget` as my commandline downloading tool as
it is more convenient, but on a Mac, only `curl` is generally available.

A few more tidbits about `grep` (we will see more examples of `grep` in
the section on regular expressions, but it is so useful that it is worth
seeing many times):

    $ grep ^2001 cpds.csv   # returns lines that start with '2001'
    $ grep 0$ cpds.csv      # returns lines that end with '0'
    $ grep 19.0 cpds.csv    # returns lines with '19' separated from '0' by a single character
    $ grep 19.*0 cpds.csv   # now separated by any number of characters
    $ grep -o 19.0 cpds.csv # returns only the content matching the pattern from the relevant lines

Note that the first argument to grep is the pattern you are looking for.
The syntax is different from that used for wildcards in file names.
Also, you can use regular expressions in the pattern. We won’t see this
in detail here, but we will discuss this in the section below on regular
expressions.

It is sometimes helpful to put the pattern inside double quotes, e.g.,
if you want spaces in your pattern:

    $ grep "George .* Bush" cpds.csv

More generally in Unix, enclosing a string in quotes is often useful to
indicate that it is a single argument/value.

If you want to explicitly look for one of the special characters used in
creating patterns (such as double quote (`"`), period (`.`), etc., you
can "escape" them by preceding with a back-slash. For example to look
for `"Canada"`, including the quotes:

    $ grep "\"Canada\"" cpds.csv
    $ grep "19\.0" cpds.csv

If you have a big data file and need to subset it by line (e.g., with
`grep`) or by field (e.g., with `cut`), then you can do it really fast
from the Unix command line, rather than reading it with R, SAS, Python,
etc.

Much of the power of these utilities comes in piping between them (see
the next section) and using wildcards (see the section on Globbing) to
operate on groups of files. The utilities can also be used in shell
scripts to do more complicated things.

We will look at several examples of how to use these utilities below,
but first let's discuss streams and redirection.

**Exercise**

You've already seen some of the above commands. Follow the links above
and while you are reading the abbreviated man pages consider how you
might use these commands.

# 3 Streams, pipes, and redirects

## 3.1 Streams (stdin/stdout/stderr)

Unix programs that involve input and/or output often operate by reading
input from a stream known as standard input (*stdin*), and writing their
results to a stream known as standard output (*stdout*). In addition, a
third stream known as standard error (*stderr*) receives error messages
and other information that's not part of the program's results. In the
usual interactive session, standard output and standard error default to
your screen, and standard input defaults to your keyboard.

## 3.2 Overview of redirection 

You can change the place from which programs read and write through
redirection. The shell provides this service, not the individual
programs, so redirection will work for all programs. The following table
shows some examples of redirection.

**Table. Common Redirection Operators**

<table>
<thead>
<tr class="header">
<th align="left">Redirection Syntax</th>
<th align="left">Function</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><code>$ cmd &gt; file</code></td>
<td align="left">Send <em>stdout</em> to <em>file</em></td>
</tr>
<tr class="even">
<td align="left"><code>$ cmd 1&gt; file</code></td>
<td align="left">Same as above</td>
</tr>
<tr class="odd">
<td align="left"><code>$ cmd 2&gt; file</code></td>
<td align="left">Send <em>stderr</em> to <em>file</em></td>
</tr>
<tr class="even">
<td align="left"><code>$ cmd &gt; file 2&gt;&amp;1</code></td>
<td align="left">Send both <em>stdout</em> and <em>stderr</em> to <em>file</em></td>
</tr>
<tr class="odd">
<td align="left"><code>$ cmd &lt; file</code></td>
<td align="left">Receive <em>stdin</em> from <em>file</em></td>
</tr>
<tr class="even">
<td align="left"><code>$ cmd &gt;&gt; file</code></td>
<td align="left">Append <em>stdout</em> to <em>file</em>:</td>
</tr>
<tr class="odd">
<td align="left"><code>$ cmd 1&gt;&gt; file</code></td>
<td align="left">Same as above</td>
</tr>
<tr class="even">
<td align="left"><code>$ cmd 2&gt;&gt; file</code></td>
<td align="left">Append <em>stderr</em> to <em>file</em></td>
</tr>
<tr class="odd">
<td align="left"><code>$ cmd &gt;&gt; file 2&gt;&amp;1</code></td>
<td align="left">Append both <em>stdout</em> and <em>stderr</em> to <em>file</em></td>
</tr>
<tr class="even">
<td align="left"><code>$ cmd1 | cmd2</code></td>
<td align="left">Pipe <em>stdout</em> from <em>cmd1</em> to <em>cmd2</em></td>
</tr>
<tr class="odd">
<td align="left"><code>$ cmd1 2&gt;&amp;1 | cmd2</code></td>
<td align="left">Pipe <em>stdout</em> and <em>stderr</em> from <em>cmd1</em> to <em>cmd2</em></td>
</tr>
<tr class="even">
<td align="left"><code>$ cmd1 | tee file1 | cmd2</code></td>
<td align="left">Pipe <em>stdout</em> from <em>cmd1</em> to <em>cmd2</em> while simultaneously writing it to <em>file1</em></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">using <em>tee</em></td>
</tr>
</tbody>
</table>


Note that `cmd` may include options and arguments as seen in the
previous section.

## 3.3 Standard redirection (pipes)

Operations where output from one command is used as input to another
command (via the `|` operator) are known as pipes; they are made
especially useful by the convention that many UNIX commands will accept
their input through the standard input stream when no file name is
provided to them.

A simple pipe to `wc` to count the number of words in a string:

    $ echo "hey there" | wc -w
    2

Translating lowercase to UPPERCASE with `tr`:

    $ echo 'user1'  | tr 'a-z' 'A-Z'
    USER1

Here's an example of finding out how many unique entries there are in
the 2nd column of a data file whose fields are separated by commas:

    $ cut -d',' -f2 cpds.csv | sort | uniq | wc
    $ cut -d',' -f2 cpds.csv | sort | uniq > countries.txt

Above we use the `cut` utility to extract the second field (`-f2`) or
column of the file `cpds.csv` where the fields (or columns) are split or
delimited by a comma (`-d','`). The standard output of the `cut` command
is then piped (via `|`) to the standard input of the `sort` command.
Then the output of `sort` is sent to the input of `uniq` to remove
duplicate entries in the sorted list provided by `sort`. Rather than
using `sort | uniq`, you could also use `sort -u`. Finally, the first of
the `cut` commands prints a word count summary using `wc`; while the
second saving the sorted information with duplicates removed in the file
`countries.txt`.

To see if there are any "S" values in certain fields (fixed width) of a
set of files (note I did this on 22,000 files (5 Gb or so) in about 5
minutes on my desktop; it would have taken much more time to read the
data into R):

    $ cut -b29,37,45,53,61,69,77,85,93,101,109,117,125,133,141,149, \ 
            157,165,173,181,189,197,205,213,221,229,237,245,253, \
            261,269 USC*.dly | grep S | less

A closely related, but subtly different, capability that we saw above is
command substitution. Recall that when the shell encounters a command
surrounded by `$()` (or backticks), it runs the command and replaces the
expression with the output from the command; this allows something
similar to a pipe, but is appropriate when a command reads its arguments
directly from the command line instead of through standard input. For
example, suppose we are interested in searching for the text `pdf` in
the last 4 R code files (those with suffix `.r` or `.R`) that were
modified in the current directory. We can find the names of the four most
recently modified files ending in `.R` or `.r` using:

    $ ls -t *.{R,r} | head -4

and we can search for the required pattern using `grep` (we will discuss
`grep` again in the section on regular expressions). Putting these
together with the backtick operator we can solve the problem using:

    $ grep pdf $(ls -t *.{R,r} | head -4)

Note that piping the output of the `ls` command into `grep` would not
achieve the desired goal, since `grep` reads its filenames from the
command line, not standard input.

## 3.4 The `xargs` and `tee` commands

You can also redirect output as the arguments to another program using
the `xargs` utility. Here's an example:

    $ ls -t *.{R,r} | head -4 | xargs grep pdf

The `tee` command lets you create two streams from one. For example,
consider the case where you want the results of this command:

    $ cut -d',' -f2 cpds.csv | sort | uniq 

to both be output to the terminal screen you are working in as well as
being saved to a file. You could issue the command twice:

    $ cut -d',' -f2 cpds.csv | sort | uniq 
    $ cut -d',' -f2 cpds.csv | sort | uniq > countries.txt

Instead of repeating the command and wasting computing time, you could
use `tee` command:

    $ cut -d',' -f2 cpds.csv | sort | uniq | tee countries.txt


# 4 Command Substitution

You may sometimes need to substitute the results of a command for use
by another command. For example, if you wanted to use the directory
listing returned by `ls` as the argument to another command, you would
type `$(ls)` in the location you want the result of `ls` to appear.

An older notation for command substitution is to use backticks (e.g.,
`` `ls` `` versus `$(ls)`). It is generally preferable to use the new
notation, since there are many annoyances with the backtick notation.
For example, backslashes (`\`) inside of backticks behave in a
non-intuitive way, nested quoting is more cumbersome inside backticks,
nested substitution is more difficult inside of backticks, and it is
easy to visually mistake backticks for a single quote.

**Exercise**

Try the following commands:

    $ ls -l tr
    $ which tr
    $ ls -l which tr
    $ ls -l $(which tr)

Make sure you understand why each command behaves as it does.


# 5 Brace expansion

We saw brace expansion when discussing file wildcards. For example, we can
rename a long file easily like this:

```bash
$ mv my_long_filename.{txt,csv}
$ mv my_long_filename.csv{,-old}
$ ls my_long_filename*
```

```
my_long_filename.csv-old
```

This works because the shell expands the braces before passing the result on to the command. So with the `mv` calls above, the shell expands the braces to produce

```bash
mv my_long_filename.txt my_long_filename.csv
mv my_long_filename.csv my_long_filename.csv-old
```

Brace expansion is quite useful and more flexible than I've indicated.
Above we saw how to use brace expansion using a comma comma separated
list of items inside the curly braces (e.g., `{txt,csv}`), but they can
also be used with a sequence specification. A sequence is indicated with
a start and end item separated by two periods (`..`). Try typing the
following examples at the command line and try to figure out how they
work:

```
$ echo {1..15}
$ echo c{c..e}
$ echo {d..a}
$ echo {1..5..2}
$ echo {z..a..-2}
```

This can be used for filename wildcards but also anywhere else it would be useful. For example to kill a bunch of sequentially-numbered processes:

```bash
$ kill 1397{62..81}
```
# 6 Quoting

A note about using single vs. double quotes in shell code. In
general, variables inside double quotes will be evaluated, but variables
not inside double quotes will not be:

    $ echo "My home directory is $HOME"
    /home/jarrod
    $ echo 'My home directory is $HOME'
    $HOME


**Table. Quotes**

<table>
<thead>
<tr class="header">
<th align="left">Types of Quoting</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><code>' '</code></td>
<td align="left">hard quote - no substitution allowed</td>
</tr>
<tr class="even">
<td align="left"><code>&quot; &quot;</code></td>
<td align="left">soft quote - allow substitution</td>
</tr>
</tbody>
</table>

This can be useful, for example, when you have a directory with a space
in its name (of course, it is better to avoid spaces in file and
directory names). Since bash uses spaces to parse the elements of the
command line, you might try escaping the spaces with a backslash:

    $ ls $HOME/with\ space
    file1.txt

However that can be a pain and may not work in all circumstances. A cleaner
approach is to use soft (or double) quotes:

    $ ls "$HOME/with space"
    file1.txt

If you used hard quotes, you will get this error:

    $ ls '$HOME/with space'
    ls: cannot access $HOME/with space: No such file or directory

What if you have double quotes in your file or directory name (again, it
is better to avoid using double quotes in file and directory names)? In
this case, you will need to escape the quote:

    $ ls "$HOME/\"with\"quote"

So we'll generally use double quotes. We can always work with a literal
double quote by escaping it as seen above.


# 7 Powerful tools for text manipulation: `grep`, `sed`, `awk`, and `perl`

Before the text editor, there was the line editor. Rather than
presenting you with the entire text as a text editor does, a line editor
only displays lines of text when it is requested to. The original Unix
line editor is called `ed`. You will likely never use `ed` directly, but
you will very likely use commands that are its descendants. For example,
the commands `grep`, `sed`, `awk`, and `vim` are all based directly on
`ed` (e.g., `grep` is a `ed` command that is now available as a
standalone command, while `sed` is a streaming version of `ed`) or
inherit much of its syntax (e.g., `awk` and `vim` both heavily borrow
from the `ed` syntax). Since `ed` was written when computing resources
were very constrained compared to today, this means that the syntax of
these commands can be terse. However, it also means that learning the
syntax for one of these tools will be rewarded when you need to learn
the syntax of another of these tools.

## 7.1 `grep`

The simplest of these tools is `grep`. As I mentioned, `ed` only
displays lines of text when requested. One common task was to print all
the lines in a file matching a specific regular expression. The command
in `ed` that does this is `g/<re>/p`, which stands for globally match
all lines containing the regular express `<re>` and print them out.

One often uses `grep` with regular expressions, discussed [here in this
tutorial](regex).

To start you will need to create a file called `file1.txt` with the
following content:

    This is the first line.
    Followed by this line.
    And then ...

To print all the lines containing `is`:

    $ grep is file1.txt 
    This is the first line.
    Followed by this line.

To print all the lines **not** containing `is`:

    $ grep -v is file1.txt 
    And then ...

Now let's consider a file named `file2.txt` with the following content:

    Here's my number: 919-543-3300.
    hi John, good to meet you
    They bought 731 bananas
    Please call 1.919.554.3800
    I think he said it was 337.4355

Let's use a regular expression pattern to print all lines
containing phone numbers:

    $ grep  '(1-)?[[:digit:]]{3}-[[:digit:]]{4}' file2.txt

You will notice that this doesn't match any lines. The reason is that
the group syntax `(1-)` and the `{}` notation are not part of the
extended syntax. To have `grep` use the extended syntax, you can either
use the `-E` option:

    $ grep -E '(1-)?[[:digit:]]{3}-[[:digit:]]{4}' file2.txt
    Here's my number: 919-543-3300.

or use the `egrep` command:

    $ egrep  '(1-)?[[:digit:]]{3}-[[:digit:]]{4}' file2.txt
    Here's my number: 919-543-3300.

If we want to match regardless of whether the phone number is separated
by a minus `-` or a period `.`, we could use the pattern `[-.]`:

    $ egrep  '(1[-.])?[[:digit:]]{3}[-.][[:digit:]]{4}' file2.txt
    Here's my number: 919-543-3300.
    Please call 1.919.554.3800
    I think he said it was 337.4355

**Exercise**

Explain what the following regular expression matches:

    $ grep '^[^T]*is.*$' file1.txt

## 7.2 `sed`

Printing lines of text with `sed`:

    $ sed -n '1,9p' file.txt       # prints out lines 1-9 of file.txt 
    $ sed -n '/^#/p' file.txt       # prints out lines starting with # of file.txt 

The first command prints out lines 1-9 of `file.txt`, while the second
one prints out lines starting with `#` of `file.txt`.

Deleting lines of text with `sed`:

    $ sed -e '1,9d' file.txt
    $ sed -e '/^;/d' -e '/^$/d' file.txt

The first line deletes lines 1-9 of `file.txt`. What do you think the
second line does?

Note that the -e flag is only necessary if you want to have more than one expression, so it's not actually needed in the first line.

Text substitution with `sed`:

    $ sed 's/old_pattern/new_pattern/' file.txt > new_file.txt
    $ sed 's/old_pattern/new_pattern/g' file.txt > new_file.txt
    $ sed -i 's/old_pattern/new_pattern/g' file.txt 

The first line replaces only the first instance in a line, while the second
line replaces all instances in a line (i.e., globally). The use of the -i
flag in the third line replaces
the pattern in place in the file, thereby altering file.txt.

## 7.3 `awk`
 
Awk is a general purpose programming language typically used in data
extraction tasks and particularly well-suited to one-liners (although it
is possible to write long programs in it, it is rare). For our purposes,
we will just look at a few common one-liners to get a sense of how it
works. Basically, awk will go through a file line by line and perform
some action for each line.

For example, to select a given column from some text (e.g., getting the PIDs of some processes):

    ps -F | awk '{ print $2 }'

To double space a file, you would read each line, print it,
and then print a blank line:

    $ awk '{ print } { print "" }' file.txt 

Print every line of a file that is longer than 80 characters:

    $ awk 'length($0) > 80' file.txt

Print the home directory of every user defined in the file
`/etc/passwd`:

    $ awk -F: '{ print $6 }' /etc/passwd

To see what this did, let's look at the first line of `/etc/passwd`:

    $ head -n 1 /etc/passwd
    root:x:0:0:root:/root:/bin/bash

As you can see the entries are separated by colons (`:`) and the sixth
field contains the root user's home directory (`/root`). The option
`-F:` specifies that the colon `:` is the field delimiter and `print $6`
prints the 6th field of each line.

You may not need to learn much `sed` or `awk`, but it is good to know about
them since you can search the internet for awk or sed one-liners. If you
have some file munging task, it can be helpful to do a quick search
before writing code to perform the task yourself.

## 7.4 `perl`

Perl is another general-purpose programming language that is particular
useful for one-liner commands to perform data extraction and
manipulation tasks. Again even if you don't learn how to program in
Perl, it can be useful to have a couple one-liners in your toolbox.

Text substitution with `perl`:

    $ perl -pi -e 's/old_pattern/new_pattern/g' file.txt
    $ perl -pi -e 's/old_pattern/new_pattern/g' $(find . -name \*.html)

The `i` option tells `perl` to do the global substitution in place. You
can also substitute the `/` with another character. For example:

    $ perl -pi -e 's:old_pattern:new_pattern:g' file.txt

Summing columns with `perl`:

    $ perl -lane 'print $F[0] + $F[1]' file.txt

This will sum columns 1 and 2 of `file.txt`.


# 8 Aliases (command shortcuts)

Aliases allow you to use an abbreviation for a command, to create new
functionality or to insure that certain options are always used when you
call an existing command. For example, I'm lazy and would rather type
`q` instead of `exit` to terminate a shell window. You could create the
alias as follow:

    $ alias q=exit

As another example, suppose you find the `-F` option of `ls` (which
displays `/` after directories, `\` after executable files and `@` after
links) to be very useful. The command :

    $ alias ls="ls -F"

will insure that the `-F` option will be used whenever you use `ls`. If
you need to use the unaliased version of something for which you've
created an alias, precede the name with a backslash (`\`). For example,
to use the normal version of `ls` after you've created the alias
described above:

    $ \ls

The real power of aliases is only achieved when they are automatically
set up whenever you log in to the computer or open a new shell window.
To achieve that goal with aliases (or any other bash shell commands),
simply insert the commands in the file `.bashrc` in your home directory.
For example, here is an excerpt from my `.bashrc`:

    # .bashrc

    # Source global definitions
    if [ -f /etc/bashrc ]; then
            . /etc/bashrc
    fi

    # User specific aliases and functions
    pushdp () {
     pushd "$(python -c "import os.path as _, ${1}; \
       print _.dirname(_.realpath(${1}.__file__[:-1]))"
     )"
    }

    export EDITOR=vim
    source /usr/share/git-core/contrib/completion/git-prompt.sh
    export PS1='[\u@\h \W$(__git_ps1 " (%s)")]\$ '

    # history settings
    export HISTCONTROL=ignoredups   # no duplicate entries
    shopt -s histappend             # append, don't overwrite

    # R settings
    export R_LIBS=$HOME/usr/lib64/R/library
    alias R="/usr/bin/R --quiet --no-save"

    # Set path
    mybin=$HOME/usr/bin
    export PATH=$mybin:$HOME/.local/bin:$HOME/usr/local/bin:$PATH:
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/usr/local/lib

    # Additional aliases  
    alias grep='grep --color=auto'
    alias hgrep='history | grep'
    alias l.='ls -d .* --color=auto'
    alias ll='ls -l --color=auto'
    alias ls='ls --color=auto'
    alias more=less
    alias vi=vim
    alias which='(alias; declare -f) | /usr/bin/which --tty-only \
             --read-alias --read-functions --show-tilde --show-dot'

**Exercise**

Look over the content of the example `.bashrc` and make sure you
understand what each line does. For instance, use `man grep` to see what
the option `--color=auto` does. Use `man which` to figure out what the
various options passed to it do.

