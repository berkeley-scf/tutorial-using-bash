---
title: Exercises
layout: default
---


1.  Make a variable, called `mypython` that contains the path to Python
    on your machine. You shouldn't need to manually type the path.
2.  Construct a variable that has the value `<username>@<machinename>` using
    existing environment variables and the `hostname` utility.
3.  Figure out how to use the `mkdir` command to create the following
    directory structure in one short command:


        temp
        ├── proj1
        │   ├── code
        │   └── data
        ├── proj2
        │   ├── code
        │   └── data
        └── proj3
            ├── code
            └── data


4.  How would you count the number of lines in an input file, say a data
    file.
5.  Print the first three lines of a file to the screen. Now print just
    the third line to the screen.
6.  Put the third line of a file in a new file.
7.  Now add the fifth line of the file to that same file from the
    previous problem.
8.  Extract the Australia data from the `cpds.csv` dataset and put it in
    a file called `cpds_australia.csv`. It's OK if you do this in a
    straightforward way and it might fail if 'Australia' is present in
    an unexpected column.
9.  Find all the lines in a file that do not contain a comma. (You might
    use this to look for anomalies in a CSV file.)
10. Write shell code that creates files `file1.txt`, `file2.txt`,
    `file3.txt`, etc., with the word 'blah' as the only line in each
    file.
11. Write shell code that modifies each file from the previous problem
    so that the number `1`, `2`, `3`, etc. is prepended to the
    appropriate file (i.e., there is a new first line in each file that
    simply contains the number corresponding to the file name).

    You may want to write the code to do this operation on a single file
    before embedding the code in the loop.

12. Create a shell function that will run a Python job in the background
    such that I can run the job by typing:

```bash
$ bpy file.py file.out
```

    You can create a test jobs with: `echo -e 'a=5\nprint(a)' > file.py`

13. Modify the function so that you can simply type :

```
$ bpy file.py
```

and it will use `file.pyout` as the output file.

14. Use `ps` to print out all the processes on the machine with
    information on memory and CPU use and sort the output of `ps` in
    decreasing order of memory use.
15. Take `$mypython` from the first problem and strip the `python` off
    the end---assigning the result to a new variable, `path_to_py`.

