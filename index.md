---
title: Using the bash shell
layout: default
author: Christopher Paciorek
---

# Using the bash shell

## 1 This tutorial

> **Prerequisite**
>
> Before reading this, if you're not already comfortable with basic commands for working with files (e.g. `cd`, `ls`, `cp` and the structure of the filesystem on a UNIX-like machine), you will want to be familiar with the introductory material in our [Basics of UNIX tutorial](https://berkeley-scf.github.io/tutorial-unix-basics).

Please see the top menu bar for the various sections of this tutorial, of which this document is the introduction.

Materials for this tutorial, including the Markdown file that was used to create this document are [available on GitHub](https://github.com/berkeley-scf/tutorial-bash-shell).

Software Carpentry has a very nice introductory lesson on the [basics of the shell](https://swcarpentry.github.io/shell-novice/). It also has an accompanying [YouTube video](https://www.youtube.com/watch?v=8c1BL5b47kg) that covers some, but not all, of the topics of this tutorial.

This tutorial by Christopher Paciorek and Jarrod Millman is licensed under a Creative Commons Attribution 3.0 Unported License.

## 2 The interactive shell

The shell is the UNIX program that provides an interactive computer programming environment. You use the shell when in a terminal window to interact with a UNIX-style operating system (e.g., Linux or MacOS). The shell sits between you and the operating system and provides useful commands and functionality. Basically, the shell is a program that serves to run other commands for you and show you the results. 

The shell is a read-evaluate-print loop (REPL) environment. R and
Python also provide REPL environments. A REPL reads a single
*expression* or input, parses and *evaluates* it, *prints* the results,
and then *loops* (i.e., returns control to you to continue your work).

> **Note**
>
> I will use a `$` prompt for bash. By convention, a regular
> user's prompt in bash is `$`, while the root (or administrative)
> user's prompt is `#`. However, it is common practice to never log on
> as the root user, even if you have root access. If you need to run a command with root privileges,
> you should use the `sudo` command.
>
> `$ echo "The current user is $USER."`
>
> `The current user is paciorek.`

When you are working in a terminal window (i.e., a window with the
command line interface), you're interacting with a shell. 
There are actually different shells that you can use, of which `bash` is very common and is the default on many systems. In recent versions of MacOS, `zsh` is the default shell. There are others as well (e.g., *sh*, *csh*, *tcsh*, *fish*). I've generated this document based on using the bash shell on a computer running the Ubuntu Linux version 20.04 operating system, and this tutorial assumes you are using *bash* or *zsh*. That said, the basic ideas and the use of various commands are applicable to any UNIX shell, and you should be able to replicate most of the steps in this tutorial in other UNIX command line environments, with various substitutions of shell syntax specific to the shell you are using,

The shell is an amazingly powerful programming environment. From it you
can interactively monitor and control almost any aspect of the OS and
more importantly you can automate it. As you will see, *bash* has a
very extensive set of capabilities intended to make both interactive as
well as automated control simple, effective, and customizable.

> **Note**
>
> It can be difficult to distinguish what is shell-specific and what is
> just part of UNIX. Some of the material in this tutorial is not bash-specific but is
> general to UNIX.
>
> Reference: Newham and Rosenblatt, Learning the bash Shell, 2nd ed.
>
> **Warning**
>
> Unfortunately, the behavior of shell commands on a Mac can be
> [somewhat different](https://ponderthebits.com/2017/01/know-your-tools-linux-gnu-vs-mac-bsd-command-line-utilities-grep-strings-sed-and-find)
> than on Linux (e.g., on a Mac, one can't do `tail -n +5`) because MacOS is based on 
> BSD, which is not a Linux distribution. The behavior of the commands is distinct from the shell you are using.



## 3 Accessing the shell

This tutorial assumes you already have access to a basic bash shell on a computer
with network access (e.g., the Terminal on a Mac, the Ubuntu subsystem on Windows, or a terminal window on a Linux machine), as discussed in our [Basics of UNIX tutorial](https://berkeley-scf.github.io/tutorial-unix-basics#1.3-accessing-a-unix-command-line-interface). 

Here's how you can see your default shell and change it if you like.

1.  What is my default shell?

    ```bash
    $ echo $SHELL
    /bin/bash
    ```
    
2.  To change to bash on a one-time basis:

    ```bash
    $ bash
    ```
    
3.  To make it your default:

    ```bash
    $ chsh /bin/bash
    ```

In the last example, `/bin/bash` should be whatever the path to the bash
shell is, which you can figure out using:

```bash
$ type bash
bash is /usr/bin/bash
```



## 4 Variables

### 4.1 Using variables

Just like programming languages, you can use variables in the shell.
Variables are names that have values assigned to them. 

To access the value currently assigned to a variable, you can prepend the name with
the dollar sign (\$). To print the value you can use the `echo` command.

For example, I can find the username of the current user in the `USER` variable:

```bash
$ echo $USER
```

```
paciorek
```

To declare a variable, just assign a value to the name, without using `$`. For
example, if you want to make a new variable with the name `counter` with
the value `1`:

```bash
$ counter=1
```

Since bash uses spaces to parse the expression you give it as input, it
is important to note the lack of spaces around the equal sign. Try
typing the command with and without spaces and note what happens.

You can also enclose the variable name in curly brackets, which comes in
handy when you're embedding a variable within a line of code, to make
sure the shell knows where the variable name ends:

```bash
$ base=/home/jarrod/
$ echo ${base}src
$ echo $basesrc
```

Make sure you understand the difference in behavior in the last two
lines.

### 4.2 Environment variables

There are also special shell variables called environment variables that
help to control the shell's behavior. These are generally named in all
caps. Type `printenv` to see them. You can create your own environment
variable as follows:

```bash
$ export base=/home/jarrod/
```

The `export` command ensures that other shells created by the current
shell (for example, to run a program) will inherit the variable. Without
the export command, any shell variables that are set will only be
modified within the current shell. More generally, if you want a
variable to always be accessible, you should include the definition of
the variable with an `export` command in your `.bashrc` file.

You can control the appearance of the bash prompt using the `PS1`
variable:

```bash
$ echo $PS1
```

To modify it so that it puts the username, hostname, and current working
directory in the prompt:

```bash
$ export PS1='[\u@\h \W]\$ '
[user1@local1 ~]$ 
```

## 5 Introduction to commands

### 5.1 Elements of a command

While each command has its own syntax, there are some rules usually
followed. Generally, a command line consists of 4 things: a command,
command options, arguments, and line acceptance. Consider the following
example:

```bash
$ ls -l file.txt
```
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

```bash
$ echo $PATH
/home/jarrod/usr/bin:/usr/local/bin:/bin:/usr/bin:
```

For example, consider the following command:

```bash
$ grep pdf file.txt
```

We will discuss `grep` later. For now, let's ignore what `grep` actually
does and focus on what bash would do when you press enter after typing
the above command. First bash checks whether `grep` a shell function or
a builtin. Once it determines that `grep` is neither a shell function
nor a builtin, it will look for an executable file named `grep` first in
`/home/jarrod/usr/bin`, then in `/usr/local/bin`, and so on until it
finds a match or runs out of places to look. You can use `type` to find
out where bash would find it:

```bash
$ type grep
grep is hashed (/usr/bin/grep)
```

Also note that the shell substitutes in the values of variables and 
does other manipulations before calling the command. For example in the following
example,

```bash
$ myfile=file.txt
$ grep pdf $myfile
```

the value of `$myfile` is substituted in before `grep` is called, so the command
that is executed is `grep pdf myfile.txt`.

### 5.2 Getting help with commands

Most bash commands have electronic manual pages, which are accessible
directly from the commandline. You will be more efficient and
effective if you become accustomed to using these `man` pages. To view
the `man` page for the command `sudo`, for instance, you would type:

```bash
$ man ls
```

Alternatively, for many commands you can use the `--help` flag:

```bash
$ ls --help
```

**Exercise**

Consider the following examples using the `ls` command:

```bash
$ ls --all -l
$ ls -a -l
$ ls -al
```

Use `man ls` to see what the command options do. Is there any difference
in what the three versions of the command invocation above return as the
result? What happens if you add a filename to the end of the command?

## 6 Operating efficiently at the command line 

### 6.1 Tab completion

When working in the shell, it is often unnecessary to type out an entire
command or file name, because of a feature known as tab completion. When
you are entering a command or filename in the shell, you can, at any
time, hit the tab key, and the shell will try to figure out how to
complete the name of the command or filename you are typing. If there is
only one such command found in the search path and you're using tab completion with
the first token of a line, then the shell will display its value and the
cursor will be one space past the completed name. If there are multiple
commands that match the partial name, the shell will display as much as
it can. In this case, hitting tab twice will display a list of choices,
and redisplay the partial command line for further editing. Similar
behavior with regard to filenames occurs when tab completion is used on
anything other than the first token of a command.

> **Note**

> Note that R does tab completion for objects (including functions) and
> filenames. While the default Python shell does not perform tab
> completion, the IPython shell does.

### 6.2 Keyboard shortcuts


Note that you can use emacs-like control sequences (`Ctrl-a`, `Ctrl-e`,
`Ctrl-k`) to navigate and delete characters.

**Table. Keyboard Shortcuts**

<table>
<thead>
<tr class="header">
<th >Key Strokes</th>
<th >Descriptions</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td ><code>Ctrl-a</code></td>
<td >Beginning of line</td>
</tr>
<tr class="even">
<td ><code>Ctrl-e</code></td>
<td >End of line</td>
</tr>
<tr class="odd">
<td ><code>Ctrl-k</code></td>
<td >Delete line from cursor forward</td>
</tr>
<tr class="even">
<td ><code>Ctrl-w</code></td>
<td >Delete word before cursor</td>
</tr>
<tr class="odd">
<td ><code>Ctrl-y</code></td>
<td >pastes in whatever was deleted previously with <code>Ctrl-k</code> or <code>Ctrl-w</code></td>
</tr>
<tr class="even">
<td ><code>ESC-F</code></td>
<td >Forward one word</td>
</tr>
<tr class="odd">
<td ><code>ESC-B</code></td>
<td >Backwards one word</td>
</tr>
<tr class="even">
<td ><code>Ctrl-d</code></td>
<td >EOF; exit</td>
</tr>
<tr class="odd">
<td ><code>Ctrl-c</code></td>
<td >Interrupt current command</td>
</tr>
<tr class="even">
<td ><code>Ctrl-z</code></td>
<td >Suspend current command</td>
</tr>
<tr class="odd">
<td ><code>Ctrl-l</code></td>
<td >Clear screen</td>
</tr>
<tr class="even">
<td ><code>Ctrl-r</code></td>
<td >Enables an <a href="http://www.techrepublic.com/article/keyboard-shortcuts-in-bash-and-zsh/">interactive search history</a></td>
</tr>
</tbody>
</table>

### 6.3 Command History and Editing

By using the up and down arrows, you can scroll through commands that
you have entered previously. So if you want to rerun the same command,
or fix a typo in a command you entered, just scroll up to it and hit
enter to run it or edit the line and then hit enter.

To list the history of the commands you entered, use the `history`
command:

```bash
$ history
```
```
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
```

The behavior of the `history` command is controlled by a shell
variables:

```bash
$ echo $HISTFILE
$ echo $HISTSIZE
```
You can also rerun previous commands as follows:

```bash
$ !-n 
$ !gi
```

The first example runs the nth previous command and the second one runs
the last command that started with 'gi'.

**Table. Command History Expansion**

<table>
<thead>
<tr class="header">
<th >Designator</th>
<th >Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td ><code>!!</code></td>
<td >Last command</td>
</tr>
<tr class="even">
<td ><code>!n</code></td>
<td >Command numbered <em>n</em> in the history</td>
</tr>
<tr class="odd">
<td ><code>!-n</code></td>
<td >Command <em>n</em> previous</td>
</tr>
<tr class="even">
<td ><code>!string</code></td>
<td >Last command starting with <em>string</em></td>
</tr>
<tr class="odd">
<td ><code>!?string</code></td>
<td >Last command containing <em>string</em></td>
</tr>
<tr class="even">
<td ><code>^string1^string2</code></td>
<td >Execute the previous command with <em>string2</em> substituted for <em>string1</em></td>
</tr>
</tbody>
</table>

If you're not sure what command you're going to recall, you can append
`:p` at the end of the text you type to do the recall, and the result
will be printed, but not executed. For example:

```bash
$ !gi:p
```

You can then use the up arrow key to bring back that statement for
editing or execution.

You can also search for commands by doing `Ctrl-r` and typing a string
of characters to search for in the search history. You can hit return to
submit, `Ctrl-c` to get out, or `ESC` to put the result on the regular
command line for editing.

## 7 Accessing remote machines

You likely already have `ssh` installed. SSH provides an
encrypted mechanism to connect to a remote Unix-based (i.e., Linux or Mac) terminal. You can [learn more
about using ssh on various operating systems](https://statistics.berkeley.edu/computing/ssh).

To ssh to another machine, you need to know its (host)name. For example,
to ssh to `arwen.berkeley.edu`, one of the SCF machines, you would:

```bash
$ ssh arwen.berkeley.edu
Password:
```

At this point you have to type your password. Alternatively, you can [set
up ssh so that you can use it without typing your password](https://statistics.berkeley.edu/computing/ssh-keys).

If you have a different username on the remote machine than on the machine you are on, you will need to
specify it as well. For example, to specify the username `jarrod`, you
would:

```bash
$ ssh jarrod@arwen.berkeley.edu
```

If you want to view graphical applications on your local computer that
are running on the remote computer you need to use the `-X` option:

```bash
$ ssh -X jarrod@arwen.berkeley.edu
```

Alternatively, if you want to copy a file (`file1.txt`) from your local
computer to `arwen.berkeley.edu`, you can use the `scp` command,
which securely copies files between machines:

```bash
$ scp file1.txt jarrod@arwen.berkeley.edu:.
```
The above command will copy `file1.txt` from my current working
directory on my local machine to `jarrod`'s home directory on
`arwen.berkeley.edu`. The `.` following the `:` indicates that I want
to copy the file to jarrod's home directory on the remote machine,
keeping the file name as it is. I could
also replace `.` with any relative path from jarrod's home directory on the
remote machine or I could use an absolute path.

To copy a file (`file2.txt`) from `arwen.berkeley.edu` to my local
machine:

```bash
$ scp jarrod@arwen.berkeley.edu:file2.txt .
```

I can even copy a file (`file3.txt`) owned by one user (`jarrod`) on one
remote machine `arwen.berkeley.edu` to the account of another user
(`jmillman`) on another remote machine `scf-ug02.berkeley.edu`:

```bash
$ scp jarrod@arwen.berkeley.edu:file3.txt jmillman@arwen.berkeley.edu:.
```
If instead of copying a single file, I wanted to copy an entire
directory (`src`) from one machine to another, I would use the `-r`
option:

```bash
$ scp -r src jmillman@arwen.berkeley.edu:.
```

Regardless of whether you are working on a local computer or a remote
one, it is occasionally useful to operate as a different user. For
instance, you may need root (or administrative) access to change file
permissions or install software. (Note this will only be possible
on machines that you own or have special privileges on. The Ubuntu
Subsystem on Windows is one way to have a virtual Linux machine
for which you have root access.)

For example on an Ubuntu Linux machine (including the Ubuntu Subsystem on Windows),
here's how you can act as the 'root' user to update or add software
on machines where you have administrative access:

To upgrade all the software on the machine:

```bash
$ sudo apt-get upgrade
```

To install the text editor vim on the machine:

```bash
$ sudo apt-get install vim
```
