# tutorial-using-bash

Tutorial on using the bash shell.

This tutorial covers:

    The Interactive Shell
    Basic File Management
    Regular Expressions
    Processes
    Shell programming
    Working with documents

To see the tutorial:

 * You can go [here](https://htmlpreview.github.io/?https://github.com/berkeley-scf/tutorial-using-bash/blob/master/bash.html) to view in your browser.
 * If you're familiar with Git you can clone the repository and view `bash.html` in a browser.
 * If you're not familiar with Git you can download [this zip file](https://github.com/berkeley-scf/tutorial-using-bash/archive/master.zip), unzip it, and view `bash.html`.



You can create the html via Pandoc (see Section 6 of bash.md) or R Markdown processing. The version in this repository was created via

```
Rscript -e "library(knitr); knit2html('bash.Rmd')"
```

Note that processing in that way causes bash.md to be overwritten.
