---
title: Using the bash shell
layout: default
author: Christopher Paciorek
---

# 1 This tutorial

Before reading this, if you're not already comfortable with basic commands for working with files (e.g. `cd`, `ls`, `cp` and the structure of the filesystem on a UNIX-like machine, you will want to be familiar with the introductory material in our [Basics of UNIX tutorial](https://berkeley-scf.github.io/tutorial-unix-basics).

Materials for this tutorial, including the Markdown file that was used to create this document are [available on GitHub](https://github.com/berkeley-scf/tutorial-bash-shell).

Software Carpentry has a very nice introductory lesson on the [basics of the shell](https://swcarpentry.github.io/shell-novice/). It also has an accompanying [YouTube video](https://www.youtube.com/watch?v=8c1BL5b47kg) that covers some, but not all, of the topics of this tutorial.

This tutorial by Jarrod Millman and Christopher Paciorek is licensed under a Creative Commons Attribution 3.0 Unported License.

# 2 The interactive shell

The shell is an interactive computer programming environment. More
specifically, it is a read-evaluate-print loop (REPL) environment. R and
Python also provide REPL environments. A REPL reads a single
*expression* or input, parses and *evaluates* it, *prints* the results,
and then *loops*.

> **note**
>
> I will use a `$` prompt for bash, a `>` prompt for R, a `>>>` for
> Python, and a `In [1]:` prompt for IPython. By convention, a regular
> user's prompt in bash is `$`, while the root (or administrative)
> user's prompt is `#`. However, it is common practice to never log on
> as the root user. If you need to run a command with root privileges,
> you should use the `sudo` command (see the *Getting started* section
> below for more details).

When you are working in a terminal window (i.e., a window with the
command line interface), you're interacting with a shell. There are
multiple shells (e.g., *sh*, *bash*, *csh*, *tcsh*, *zsh*, *fish*). I'll
assume you are using *bash* or *zsh*, as these are the defaults for Mac OS X and most Linux distributions
(including the UC Berkeley Statistics Department machines and UC Berkeley Savio campus cluster). However, the
basic ideas are applicable to any Unix shell.

The shell is an amazingly powerful programming environment. From it you
can interactively monitor and control almost any aspect of the OS and
more importantly you can automate it. As you will see, **bash** has a
very extensive set of capabilities intended to make both interactive as
well as automated control simple, effective, and customizable.

> **note**
>
> It can be difficult to distinguish what is shell-specific and what is
> just part of UNIX. Some of the material here is not bash-specific but
> general to UNIX.
>
> Reference: Newham and Rosenblatt, Learning the bash Shell, 2nd ed.
>
> Unfortunately, the behavior of shell commands on a Mac can be
> [somewhat different](https://ponderthebits.com/2017/01/know-your-tools-linux-gnu-vs-mac-bsd-command-line-utilities-grep-strings-sed-and-find)
> than on Linux (e.g., on a Mac, one can't do `tail -n +5`) because MacOS is based on 
> BSD, which is not a Linux distribution. The behavior of the commands is distinct from the shell you are using.


# 3 Getting started

I assume you already have access to a basic bash shell on a computer
with network access (e.g., the Terminal on a Mac, the Ubuntu subsystem on Windows, or a Linux machine). You should also have ssh installed. SSH provides an
encrypted mechanism to connect to a remote Unix-based (i.e., Linux or Mac) terminal. To learn more
about using ssh to connect to the SCF machines and general tips about
using ssh on various operating systems, see:
<http://statistics.berkeley.edu/computing/ssh>

To ssh to another machine, you need to know its (host)name. For example,
to ssh to `arwen.berkeley.edu`, one of the SCF machines, you would:

    $ ssh arwen.berkeley.edu
    Password:

At this point you have to type your password. Alternatively, you can set
up ssh so that you can use it without typing your password. To learn how
to set this up, see: <http://statistics.berkeley.edu/computing/ssh-keys>

If you have a different username on SCF machines, you will need to
specify it as well. For example, to specify the username `jarrod`, you
would:

    $ ssh jarrod@arwen.berkeley.edu

If you want to view graphical applications on your local computer that
are running on the remote computer you need to use the `-X` option:

    $ ssh -X jarrod@arwen.berkeley.edu

Alternatively, if you want to copy a file (`file1.txt`) from your local
computer to `arwen.berkeley.edu`, you can use the `scp` command,
which securely copies files between machines:

    $ scp file1.txt jarrod@arwen.berkeley.edu:.

The above command will copy `file1.txt` from my current working
directory on my local machine to `jarrod`'s home directory on
`arwen.berkeley.edu`. The `.` following the `:` indicates that I want
to copy the file to my home directory on the remote machine. I could
also replace `.` with any relative path from my home directory on the
remote machine or I could use an absolute path.

To copy a file (`file2.txt`) from `arwen.berkeley.edu` to my local
machine:

    $ scp jarrod@arwen.berkeley.edu:file2.txt .

I can even copy a file (`file3.txt`) owned by one user (`jarrod`) on one
remote machine `arwen.berkeley.edu` to the account of another user
(`jmillman`) on another remote machine `scf-ug02.berkeley.edu`:

    $ scp jarrod@arwen.berkeley.edu:file3.txt jmillman@arwen.berkeley.edu:.

If instead of copying a single file, I wanted to copy an entire
directory (`src`) from one machine to another, I would use the `-r`
option:

    $ scp -r src jmillman@arwen.berkeley.edu:.

Regardless of whether you are working on a local computer or a remote
one, it is occasionally useful to operate as a different user. For
instance, you may need root (or administrative) access to change file
permissions or install software. (Note this will only be possible
on machines that you own or have special privileges on; the Ubuntu
subsystem for windows is one way to have a virtual Linux machine
for which you have root access.)

For example on an Ubuntu Linux machine (including the Ubuntu subsystem for Windows),
here's how you can act as the 'root' user to update or add software
on machines where you have administrative access:

To upgrade all the software on the machine:

    $ sudo apt-get upgrade

To install the text editor vim on the machine:

    $ sudo apt-get install vim

> **tip**
>
> Most bash commands have electronic manual pages, which are accessible
> directly from the commandline. You will be more efficient and
> effective if you become accustomed to using these `man` pages. To view
> the `man` page for the command `sudo`, for instance, you would type:
>
>     $ man sudo


# 4 Variables

Much of how bash behaves can be customized through the use of variables,
which consists of names that have values assigned to them. To access the
value currently assigned to a variable, you can prepend the name with
the dollar sign (\$). To print the value you can use the `echo` command.

1.  What is my default shell?

    `$ echo $SHELL`
2.  To change to bash on a one-time basis:

    `$ bash`
3.  To make it your default:

    `$ chsh /bin/bash`

In the last example, `/bin/bash` should be whatever the path to the bash
shell is, which you can figure out using `which bash`.

To declare a variable, just assign a value to its reference. For
example, if you want to make a new variable with the name `counter` with
the value `1`:

    $ counter=1

Since bash uses spaces to parse the expression you give it as input, it
is important to note the lack of spaces around the equal sign. Try
typing the command with and without spaces and note what happens.

You can also enclose the variable name in curly brackets, which comes in
handy when you're embedding a variable within a line of code to make
sure the shell knows where the variable name ends:

    $ base=/home/jarrod/
    $ echo $basesrc
    $ echo ${base}src

Make sure you understand the difference in behavior in the last two
lines.

There are also special shell variables called environment variables that
help to control the shell's behavior. These are generally named in all
caps. Type `printenv` to see them. You can create your own environment
variable as follows:

    $ export base=/home/jarrod/

The `export` command ensures that other shells created by the current
shell (for example, to run a program) will inherit the variable. Without
the export command, any shell variables that are set will only be
modified within the current shell. More generally, if you want a
variable to always be accessible, you should include the definition of
the variable with an `export` command in your `.bashrc` file.

You can control the appearance of the bash prompt using the `PS1`
variable:

    $ echo $PS1

To modify it so that it puts the username, hostname, and current working
directory in the prompt:

    $ export PS1='[\u@\h \W]\$ '
    [user1@local1 ~]$ 

# Introduction to commands

While each command has its own syntax, there are some rules usually
followed. Generally, a command line consists of 4 things: a command,
command options, arguments, and line acceptance. Consider the following
example:

    $ ls -l file.txt

In the above example, `ls` is the command, `-l` is a command option
specifying to use the long format, `file.txt` is the argument, and the
line acceptance is indicated by hitting the `Enter` key at the end of
the line.

After you type a command at the bash prompt and indicate line acceptance
with the `Enter` key, bash parses the command and then attempts to
execute the command. To determine what to do, bash first checks whether
the command is a shell function (we will discuss functions below). If
not, it checks to see whether it is a builtin. Finally, if the command
is not a shell function nor a builtin, bash uses the `PATH` variable.
The `PATH` variable is a list of directories:

    $ echo $PATH
    /home/jarrod/usr/bin:/usr/local/bin:/bin:/usr/bin:

For example, consider the following command:

    $ grep pdf file.txt

We will discuss `grep` later. For now, let's ignore what `grep` actually
does and focus on what bash would do when you press enter after typing
the above command. First bash checks whether `grep` a shell function or
a builtin. Once it determines that `grep` is neither a shell function
nor a builtin, it will look for an executable file named `grep` first in
`/home/jarrod/usr/bin`, then in `/usr/local/bin`, and so on until it
finds a match or runs out of places to look. You can use `which` to find
out where bash would find it:

    $ which grep
    /bin/grep

**Exercise**

Consider the following examples using the `ls` command:

    $ ls --all -l
    $ ls -a -l
    $ ls -al

Use `man ls` to see what the command options do. Is there any difference
in what the three versions of the command invocation above return as the
result? What happens if you add a filename to the end of the command?

## 5.1 Tab completion

When working in the shell, it is often unnecessary to type out an entire
command or file name, because of a feature known as tab completion. When
you are entering a command or filename in the shell, you can, at any
time, hit the tab key, and the shell will try to figure out how to
complete the name of the command or filename you are typing. If there is
only one command in the search path and you're using tab completion with
the first token of a line, then the shell will display its value and the
cursor will be one space past the completed name. If there are multiple
commands that match the partial name, the shell will display as much as
it can. In this case, hitting tab twice will display a list of choices,
and redisplay the partial command line for further editing. Similar
behavior with regard to filenames occurs when tab completion is used on
anything other than the first token of a command.

> **note**

> Note that R does tab completion for objects (including functions) and
> filenames. While the default Python shell does not perform tab
> completion, the IPython shell does.

## 5.2 Command History and Editing

By using the up and down arrows, you can scroll through commands that
you have entered previously. So if you want to rerun the same command,
or fix a typo in a command you entered, just scroll up to it and hit
enter to run it or edit the line and then hit enter.

To list the history of the commands you entered, use the `history`
command:

    $ history
      1    echo $PS1
      2    PS1=$
      3    bash
      4    export PS1=$
      5    bash
      6    echo $PATH
      7    which echo
      8    ls --all -l
      9    ls -a -l
      10   ls -al
      11   ls -al manual.xml

The behavior of the `history` command is controlled by a shell
variables:

    $ echo $HISTFILE
    $ echo $HISTSIZE

You can also rerun previous commands as follows:

    $ !-n 
    $ !gi

The first example runs the nth previous command and the second one runs
the last command that started with 'gi'.

**Table. Command History Expansion**

<table>
<thead>
<tr class="header">
<th align="left">Designator</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><code>!!</code></td>
<td align="left">Last command</td>
</tr>
<tr class="even">
<td align="left"><code>!n</code></td>
<td align="left">Command numbered <em>n</em> in the history</td>
</tr>
<tr class="odd">
<td align="left"><code>!-n</code></td>
<td align="left">Command <em>n</em> previous</td>
</tr>
<tr class="even">
<td align="left"><code>!string</code></td>
<td align="left">Last command starting with <em>string</em></td>
</tr>
<tr class="odd">
<td align="left"><code>!?string</code></td>
<td align="left">Last command containing <em>string</em></td>
</tr>
<tr class="even">
<td align="left"><code>^string1^string2</code></td>
<td align="left">Execute the previous command with <em>string2</em> substituted for <em>string1</em></td>
</tr>
</tbody>
</table>

If you're not sure what command you're going to recall, you can append
`:p` at the end of the text you type to do the recall, and the result
will be printed, but not executed. For example:

    $ !gi:p

You can then use the up arrow key to bring back that statement for
editing or execution.

You can also search for commands by doing `Ctrl-r` and typing a string
of characters to search for in the search history. You can hit return to
submit, `Ctrl-c` to get out, or `ESC` to put the result on the regular
command line for editing.

# 5.3 Command Substitution

You may occasionally need to substitute the results of a command for use
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

# 6 Shortcuts

## 6.1 Aliases -- command shortcuts

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

## 6.2 Keyboard shortcuts

Note that you can use emacs-like control sequences (`Ctrl-a`, `Ctrl-e`,
`Ctrl-k`) to navigate and delete characters.

**Table. Keyboard Shortcuts**

<table>
<thead>
<tr class="header">
<th align="left">Key Strokes</th>
<th align="left">Descriptions</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><code>Ctrl-a</code></td>
<td align="left">Beginning of line</td>
</tr>
<tr class="even">
<td align="left"><code>Ctrl-e</code></td>
<td align="left">End of file</td>
</tr>
<tr class="odd">
<td align="left"><code>Ctrl-k</code></td>
<td align="left">Delete line from cursor forward</td>
</tr>
<tr class="even">
<td align="left"><code>Ctrl-d</code></td>
<td align="left">EOF; exit</td>
</tr>
<tr class="odd">
<td align="left"><code>Ctrl-c</code></td>
<td align="left">Interrupt current command</td>
</tr>
<tr class="even">
<td align="left"><code>Ctrl-z</code></td>
<td align="left">Suspend current command</td>
</tr>
<tr class="odd">
<td align="left"><code>Ctrl-l</code></td>
<td align="left">Clear screen</td>
</tr>
</tbody>
</table>
