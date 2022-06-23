---
title: Shell programming
layout: default
---

# 1 Shell scripts 

Shell scripts are files containing shell commands (commonly with the
extension `.sh`) To run a shell script called `file.sh`, you would type
:

```bash
$ source ./file.sh
```

or :

```bash
$ . ./file.sh
```

Note that if you just typed `file.sh`, the operating system will
generally have trouble finding the script (if `file.sh` is not in a
directory included in the `PATH` environment variable) and recognizing that it is
executable (if the `-x` flag is not set for `file.sh`). 

To be sure that the operating system knows what shell to use
to interpret the script, the first line of the script should be
`#!/bin/bash` (in the case that you're using the bash shell). 

The best thing to do is to set `file.sh` to be executable (i.e., to have the 'x' flag set), and you can can execute it simply with:

```bash
$ ./file.sh
```

# 2 Functions

You can define your own utilities by creating a shell function. This
allows you to automate things that are more complicated than you can do
with an
[alias](using-commands#8-aliases-command-shortcuts-and-bashrc).
One nice thing about shell functions is that the shell
automatically takes care of function arguments for you. It places the
arguments given by the user into local variables in the function called
(in order): `$1 $2 $3` etc. It also fills `$#` with the number of
arguments given by the user. Here's an example of using arguments in a
function that saves me some typing when I want to copy a file to the SCF
filesystem:

```bash
function putscf() {
   scp $1 jarrod@arwen.berkeley.edu:$2
}
```

To use this function, I just do the following to copy `unit1.pdf` from
the current directory on whatever non-SCF machine I'm on to the
directory `~/teaching/243` on SCF:

```bash
$ putscf unit1.pdf teaching/243/.
```

Often you'd want to put such functions in your `.bashrc` file.

# 3 If/then/else

We can use if-then-else type syntax to control the flow of a shell
script. For an example, here is a shell function `niceR()` that can be
used for nicing R jobs:

```bash
    # niceR shortcut for nicing R jobs 
    # usage: niceR inputRfile outputRfile 
    # Author: Brian Caffo 
    # Date: 10/01/03 

    function niceR(){
        # submits nice'd R jobs
        if [ $# != "2" ]; then
             echo "usage: niceR inputRfile outputfile" 
        elif [ -e "$2" ]; then
             echo "$2 exists, I won't overwrite" 
        elif [ ! -e "$1" ]; then
             echo "inputRfile $1 does not exist" 
        else
             echo "running R on $1" 
             nice -n 19 R --no-save < $1 &> $2
        fi
    }
```
	
If the `then` is on a separate line from the `if`, you won't need the semicolon. 

# 4 For loops

*for* loops in shell scripting are primarily designed for iterating
through a set of files or directories. Here's an example:

```bash
$ for FILE in $(ls *.txt); do
>    mv $file ${FILE/.txt/.R}
>   # this syntax replaces .txt with .R in $FILE``
> done
```

Another use of *for* loops is automating file downloads:

```bash
    # example of bash for loop and wget for downloading a collection of files on the web
    # usage: ./forloopDownload.sh
    # Author: Chris Paciorek
    # Date: July 28, 2011

    url='ftp://ftp.ncdc.noaa.gov/pub/data/ghcn/daily/grid/years'
    types="tmin tmax"
    for ((yr=1950; yr<=2017; yr++))
    do
        for type in ${types}
        do
            wget ${url}/${yr}.${type}
        done
    done
```

If the `do` is on a separate line from the `for`, you don't need the
semicolon seen in the previous example. 

	
*for* loops are very useful for starting a series of jobs:

```bash
    # example of bash for loop for starting jobs
    # usage: ./forloopJobs.sh
    # Author: Chris Paciorek
    # Date: July 28, 2011

    n=100 
    for(( it=1; it<=100; it++ ))
    do
        echo "n=$n; it=$it; source('base.R')" > tmp-$n-$it.R   # create customized R file
        R CMD BATCH --no-save tmp-$n-$it.R sim-n$n-it$it.Rout
    done
    # note that base.R should NOT set either 'n' or 'it'
```
	
That's just an illustration. In reality, in the case above you'd be better off passing arguments into the R code using `commandArgs` or by setting environment variables that are read in the R code.

Note by default the separator when you're looping through elements of a variable will be a space (as above), but you can set it differently, for example:

```bash
$ IFS=:
$ types=tmin:tmax:pmin:pmax
$ for type in $types
> do
>    echo $type
> done
tmin
tmax
pmin
pmax
```


# 5 How much shell scripting should I learn?

We've covered most of what you are likely to need to know about the
shell. I tend to only use bash scripts for simple tasks that require
only a few lines of bash commands and very little control flow (i.e.,
conditional statements, loops). For more complicated OS tasks, it is
often preferable to use Python. You can also do a fair amount of what
you need from within R using the `system()` function. This will enable
you to avoid dealing with a lot of shell programming syntax (but you'll
still need to know how to use standard UNIX commands/utilities, wildcards, and pipes to be
effective).
