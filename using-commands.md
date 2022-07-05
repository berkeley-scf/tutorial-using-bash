---
title: Using UNIX commands
layout: default
---

## 1 Basic utilities / commands

[Earlier](index#5-introduction-to-commands) we introduced the basics of entering commands in the shell.


Since files are such an essential aspect of Unix and working from the
shell is the primary way to work with Unix, there are a large number of
useful commands and tools to view and manipulate files.

-   cat -- concatenate files and print to standard output
-   cp -- copy files and directories
-   cut --_remove sections from each line of files
-   diff -- find differences between two files
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
-   wget and curl -- non-interactive internet downloading

Recall that a command consists of the command, optionally one or more flags, and optionally one or more arguments. When there is an argument, it is often the name of a file that the command should operate on.

Thus the general syntax for a Unix program/command/utility is:

```
$ command -options argument1 argument2 ...
```

For example, :

```bash
$ grep -i graphics file.txt
```

looks for the literal string `graphics` (argument 1) in `file.txt`
(argument2) with the option `-i`, which says to ignore the case of the
letters. A simpler invocation is:While :

```bash
$ less file.txt
```

which simply pages through a text file (you can navigate up and down
with the space bar and the up/down arrows) so you
can get a feel for what's in it. To exit `less` type `q`.


Unix programs often take flags (options) that are identified with a minus
followed by a letter and then (possibly) followed by the specific option (adding a space
before the specific option is fine). Options may also involve two
dashes, e.g., `R --no-save`. A standard two dash option for many
commands is `--help`. For example, try:

```bash
$ tail --help
```

Here are a couple of examples of flags when using the `tail` command
(`-n 10` and `-f`):

```bash
$ wget https://raw.githubusercontent.com/berkeley-scf/tutorial-using-bash/master/cpds.csv
$ tail -n 10 cpds.csv   # last 10 lines of cpds.csv
$ tail -f cpds.csv      # shows end of file, continually refreshing
```

The first line downloads the data from GitHub. The two main tools
for downloading network-accessible data from the commandline are `wget`
and `curl`. I tend to use `wget` as my commandline downloading tool as
it is more convenient, but on a Mac, only `curl` is generally available.

A few more tidbits about `grep` (we will see more examples of `grep` in
the [section on regular expressions](regex), but it is so useful that it is worth
seeing many times):

```bash
$ grep ^2001 cpds.csv   # returns lines that start with '2001'
$ grep 0$ cpds.csv      # returns lines that end with '0'
$ grep 19.0 cpds.csv    # returns lines with '19' separated from '0' by a single character
$ grep 19.*0 cpds.csv   # now separated by any number of characters
$ grep -o 19.0 cpds.csv # returns only the content matching the pattern, not entire lines
```

Note that the first argument to grep is the pattern you are looking for.
The syntax is different from that [used for wildcards](file-management#3-filename-globbing) in file names.
Also, you can use regular expressions in the pattern, but we defer
details until [later](regex).

It is sometimes helpful to put the pattern inside double quotes, e.g.,
if you want spaces in your pattern:

```bash
$ grep "George .* Bush" cpds.csv
```

More generally in Unix, enclosing a string in quotes is often useful to
indicate that it is a single argument/value.

If you want to explicitly look for one of the special characters used in
creating patterns (such as double quote (`"`), period (`.`), etc.), you
can "escape" them by preceding with a back-slash. For example to look
for `"Canada"`, including the quotes:

```bash
$ grep "\"Canada\"" cpds.csv     # look for "Canada" (including quotes)
$ grep "19\.0" cpds.csv              # look for 19.0
```
	
If you have a big data file and need to subset it by line (e.g., with
`grep`) or by field (e.g., with `cut`), then you can do it really fast
from the Unix command line, rather than reading it with R, SAS, Python,
etc.

Much of the power of these utilities comes in piping between them (see
the next section) and [using wildcards](file-management#3-filename-globbing) to
operate on groups of files. The utilities can also be used in shell
scripts to do more complicated things.

We'll see further examples of how to use these utilities later.

**Exercise**

You've already seen some of the above commands. Use the `--help`
syntax to view the abbreviated  man pages for some commands you're not
familiar with and consider how you
might use these commands.

## 3 Streams, pipes, and redirects

### 3.1 Streams (stdin/stdout/stderr)

Unix programs that involve input and/or output often operate by reading
input from a *stream* known as standard input (*stdin*), and writing their
results to a stream known as standard output (*stdout*). In addition, a
third stream known as standard error (*stderr*) receives error messages
and other information that's not part of the program's results. In the
usual interactive session, standard output and standard error default to
your screen, and standard input defaults to your keyboard.

### 3.2 Overview of redirection 

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
<td align="left">Append <em>stdout</em> to <em>file</em></td>
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



### 3.3 Standard redirection (pipes)

Operations where output from one command is used as input to another
command (via the `|` operator) are known as pipes; they are made
especially useful by the convention that many UNIX commands will accept
their input through the standard input stream when no file name is
provided to them.

A simple pipe to `wc` to count the number of words in a string:

```bash
$ echo "hey there" | wc -w
2
```

Translating lowercase to UPPERCASE with `tr`:

```bash
$ echo 'user1'  | tr 'a-z' 'A-Z'
USER1
```

Here's an example of finding out how many unique entries there are in
the 2nd column of a data file whose fields are separated by commas:

```bash
$ cut -d',' -f2 cpds.csv | sort | uniq | wc
$ cut -d',' -f2 cpds.csv | sort | uniq > countries.txt
```

Here are the piecies of what is going on in the commands above:

 - We use the `cut` utility to extract the second field (`-f2`) or
column of the file `cpds.csv` where the fields (or columns) are split or
delimited by a comma (`-d','`).
 - The standard output of the `cut` command [is then piped (via `|`) to the standard input of the `sort` command.
 - Then the output of `sort` is sent to the input of `uniq` to remove
duplicate entries in the sorted list provided by `sort`. (Rather than
using `sort | uniq`, you could also use `sort -u`.)
 - Finally, the first of the `cut` commands prints a word count summary using `wc`; while the
second saving the sorted information with duplicates removed in the file
`countries.txt`.

As another example of checking for anomalies in a set of files, with
the , to see if there are any "S" values in certain fields (based on fixed
width using  `-b`) of a
set of files (`USC*dly`), one can do this: 

```bash
$ cut -b29,37,45,53,61,69,77,85,93,101,109,117,125,133,141,149, \ 
            157,165,173,181,189,197,205,213,221,229,237,245,253, \
            261,269 USC*.dly | grep S | less
```

 (Note I did that on 22,000 files (5 Gb or so) in about 5
minutes on my desktop; it would have taken much more time to read the
data into a program like R or Python.)

### 3.4 The `tee` command

The `tee` command lets you create two streams from one. For example,
consider the case where you want the results of this command:

```bash
$ cut -d',' -f2 cpds.csv | sort | uniq 
```

to both be output to the terminal screen you are working in as well as
being saved to a file. You could issue the command twice:

```bash
$ cut -d',' -f2 cpds.csv | sort | uniq
$ cut -d',' -f2 cpds.csv | sort | uniq > countries.txt
```

Instead of repeating the command and wasting computing time, you could
use `tee` command:

```bash
$ cut -d',' -f2 cpds.csv | sort | uniq | tee countries.txt
```

## 4 Command substitution and the `xargs` command

### 4.1 Command substitution

A closely related, but subtly different, capability to piping is
command substitution. You may sometimes need to substitute the results of a command for use
by another command. For example, if you wanted to use the directory
listing returned by `ls` as the argument to another command, you would
type `$(ls)` in the location you want the result of `ls` to appear.

When the shell encounters a command
surrounded by `$()`, it runs the command and replaces the
expression with the output from the command. This allows something
similar to a pipe, but it is appropriate when a command reads its arguments
directly from the command line instead of through standard input.

For
example, suppose we are interested in searching for the text `pdf` in
the last 4 R code files (those with suffix `.r` or `.R`) that were
modified in the current directory. We can find the names of the four most
recently modified files ending in `.R` or `.r` using:

```bash
$ ls -t *.{R,r} | head -4
```

and we can search for the required pattern using `grep` . Putting these
together with command substitution, we can solve the problem using:

```bash
$ grep pdf $(ls -t *.{R,r} | head -4)
```

Suppose that the four R code file names produced by the `ls` command above were:
`test.R`, `run.R`, `analysis.R` , and `process.R`. Then the result of the command substitution above is to run the following command:

```bash
$ grep pdf test.R run.R analysis.R process.R
```


> **Note**
>
> An older notation for command substitution is to use backticks (e.g.,
> `` `ls` `` rather than `$(ls)`). It is generally preferable to use the new
> notation, since there are many annoyances with the backtick notation.
> For example, backslashes (`\`) inside of backticks behave in a
> non-intuitive way, nested quoting is more cumbersome inside backticks,
>nested substitution is more difficult inside of backticks, and it is
> easy to visually mistake backticks for a single quote.

Note that piping the output of the `ls` command into `grep` would not
achieve the desired goal, since `grep` reads its filenames as arguments from the
command line, not standard input.

### 4.2 The `xargs`  command

While it doesn't work to directly use pipes to redirect output from one program
as arguments to another program, you  can redirect output as the arguments to another program using
the `xargs` utility. Here's an example:

```bash
$ ls -t *.{R,r} | head -4 | xargs grep pdf
```

where the result is equivalent to the use of command substitution we saw in the previous section.


**Exercise**

Try the following commands:

```bash
$ ls -l tr
$ type -p tr
$ ls -l type -p tr
$ ls -l $(type -p tr)
```

Make sure you understand why each command behaves as it does.


## 5 Brace expansion

We saw brace expansion when discussing file wildcards. For example, we can
rename a file with a long name easily like this:

```bash
$ mv my_long_filename.{txt,csv}
$ ls my_long_filename*
my_long_filename.csv
$ mv my_long_filename.csv{,-old}
$ ls my_long_filename*
my_long_filename.csv-old
```

This works because the shell expands the braces before passing the result on to the command. So with the `mv` calls above, the shell expands the braces to produce

```bash
mv my_long_filename.txt my_long_filename.csv
mv my_long_filename.csv my_long_filename.csv-old
```

Brace expansion is quite useful and more flexible than I've indicated.
Above we saw how to use brace expansion using a comma-separated
list of items inside the curly braces (e.g., `{txt,csv}`), but they can
also be used with a sequence specification. A sequence is indicated with
a start and end item separated by two periods (`..`). Try typing the
following examples at the command line and try to figure out how they
work:

```bash
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
## 6 Quoting

A note about using single vs. double quotes in shell code. In
general, variables inside double quotes will be evaluated, but variables
not inside double quotes will not be:

```bash
$ echo "My home directory is $HOME"
My home directory is /home/jarrod
$ echo 'My home directory is $HOME'
My home directory is $HOME
```

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
directory names). For example, suppose you have a directory named "with space" within the `/home/jarrod` home directory.
Since bash uses spaces to parse the elements of the
command line, you might try escaping any spaces with a backslash:

```bash
$ ls $HOME/with\ space
file1.txt
```

However that can be a pain and may not work in all circumstances. A cleaner
approach is to use soft (or double) quotes:

```bash
$ ls "$HOME/with space"
file1.txt
```

If you used hard quotes, you will get this error:

```bash
$ ls '$HOME/with space'
ls: cannot access $HOME/with space: No such file or directory
```

What if you have double quotes in your file or directory name, such as a directory `"with"quote`  (again, it
is better to avoid using double quotes in file and directory names)? In
this case, you will need to escape the quote:

```bash
$ ls "$HOME/\"with\"quote"
```

So we'll generally use double quotes. We can always work with a literal
double quote by escaping it as seen above.


## 7 Powerful tools for text manipulation: `grep`, `sed`, and `awk`

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

An important benefit of these tools, particularly when working with large files, is that by operating line by line they
don't incur the memory use that would be involved in reading an entire file into memory in a program like Python or R and then operating on the file's contents in memory.

You may not need to learn much `sed` or `awk`, but it is good to know about
them since you can search the internet for awk or sed one-liners. If you
have some file munging task, it can be helpful to do a quick search
before writing code to perform the task yourself.

### 7.1 `grep`

The simplest of these tools is `grep`. As I mentioned, `ed` only
displays lines of text when requested. One common task was to print all
the lines in a file matching a specific regular expression. The command
in `ed` that does this is `g/<re>/p`, which stands for globally match
all lines containing the regular express `<re>` and print them out.

One often uses `grep` with [regular expressions](regex), covered
later, so we'll just show some basic usage here.

To start you will need to create a file called `testfile.txt` with the
following content:

```
This is the first line.
Followed by this line.
And then ...
```

To print all the lines containing `is`:

```bash
$ grep is testfile.txt
This is the first line.
Followed by this line.
```

To print all the lines **not** containing `is`:

```bash
$ grep -v is testfile.txt 
And then ...
```

To print only the matches, one can use the `-o` flag, though this
would generally only be interesting when used with a regular
expression pattern since in this case, we know "is" is what will be
returned:

```bash
$ grep -o is testfile.txt
is
is
is
```

One could also use `--color` so that the matches are highlighed in color.


### 7.2 `sed`

Here are some useful things you can do with `sed`. Note that as with
other UNIX tools, `sed` will not generally directly alter a file
(unless you use the `-i` flag); instead it will print the modified
version of the file to stdout.

Printing lines of text with `sed`:

```bash
$ sed -n '1,9p' file.txt       # prints out lines 1-9 from file.txt
$ sed -n '/^#/p' file.txt      # prints out lines starting with # from file.txt 
```

The first command prints out lines 1-9, while the second
one prints out lines starting with `#`.

Deleting lines of text with `sed`:

```bash
$ sed -e '1,9d' file.txt
$ sed -e '/^;/d' -e '/^$/d' file.txt
```

The first line deletes lines 1-9 of `file.txt`, printing the remaining
lines to stdout. What do you think the
second line does?

Note that the -e flag is only necessary if you want to have more than one expression, so it's not actually needed in the first line.

Text substitution with `sed`:

```bash
$ sed 's/old_pattern/new_pattern/' file.txt > new_file.txt
$ sed 's/old_pattern/new_pattern/g' file.txt > new_file.txt
$ sed -i 's/old_pattern/new_pattern/g' file.txt 
```

The first line replaces only the first instance in a line, while the second
line replaces all instances in a line (i.e., globally). The use of the -i
flag in the third line replaces
the pattern **in place** in the file, thereby altering file.txt. Use
the `-i` flag carefully as there is no way to easily restore the original version of the file.

### 7.3 `awk`
 
Awk is a general purpose programming language typically used in data
extraction tasks and particularly well-suited to one-liners (although it
is possible to write long programs in it, it is rare). For our purposes,
we will just look at a few common one-liners to get a sense of how it
works. Basically, awk will go through a file line by line and perform
some action for each line.

For example, to select a given column from some text (here getting
the PIDs of some processes, which are in the second (`$2`) column of
the output of `ps -f`:

```bash
ps -f | awk '{ print $2 }'
```

To double space a file, you would read each line, print it,
and then print a blank line:

```bash
$ awk '{ print } { print "" }' file.txt 
```

Print every line of a file that is longer than 80 characters:

```bash
$ awk 'length($0) > 80' file.txt
```

Print the home directory of every user defined in the file
`/etc/passwd`:

```bash
$ awk -F: '{ print $6 }' /etc/passwd
```

To see what that does, let's look at the first line of `/etc/passwd`:

```bash
$ head -n 1 /etc/passwd
root:x:0:0:root:/root:/bin/bash
```

As you can see the entries are separated by colons (`:`) and the sixth
field contains the root user's home directory (`/root`). The option
`-F:` specifies that the colon `:` is the field delimiter (instead of
the default space delmiter) and `print $6`
prints the 6th field of each line.

Summing columns:

```bash
$ awk '{print $1 + $2}' file.txt
```

This will sum columns 1 and 2 of `file.txt`.


## 8 Aliases (command shortcuts) and .bashrc

Aliases allow you to use an abbreviation for a command, to create new
functionality or to insure that certain options are always used when you
call an existing command. For example, I'm lazy and would rather type
`q` instead of `exit` to terminate a shell window. You could create the
alias as follow:

```bash
$ alias q=exit
```

As another example, suppose you find the `-F` option of `ls` (which
displays `/` after directories, `\` after executable files and `@` after
links) to be very useful. The command :

```bash
$ alias ls="ls -F"
```

will ensure that the `-F` option will be used whenever you use `ls`. If
you need to use the unaliased version of something for which you've
created an alias, precede the name with a backslash (`\`). For example,
to use the normal version of `ls` after you've created the alias
described above:

```bash
$ \ls
```

The real power of aliases is only achieved when they are automatically
set up whenever you log in to the computer or open a new shell window.
To achieve that goal with aliases (or any other bash shell commands),
simply insert the commands in the file `.bashrc` in your home directory.
For example, here is an excerpt from my `.bashrc`:

```bash
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
```


**Exercise**

Look over the content of the example `.bashrc` and make sure you
understand what each line does. For instance, use `man grep` to see what
the option `--color=auto` does. Use `man which` to figure out what the
various options passed to it do.

