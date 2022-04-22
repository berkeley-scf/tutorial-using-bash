---
title: Using UNIX commands
layout: default
---



# 5.3 Command Substitution

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


# 6 Shortcuts

## 6.1 Brace expansion

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
$ echo {{d..b},a,{b..d}}
```

This can be used for filename wildcards but also anywhere else it would be useful. For example to kill a bunch of sequentially-numbered processes:

```bash
$ kill 1397{62..81}
```

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

