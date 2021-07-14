---
layout: post
title: Useful Python to R translations
date: 2020-07-28 11:00:00
description: Code for my favourite functions in both languages
---


After spending 3 months on a Python project (see the [COVID-19 community map here!](https://ninadicara.co.uk/projects/covid-community-map/)) I've gone back to an R project that I started in 2019. After getting used to using Python there have been a few functions that I've really missed in R, so I've collected a few substitutes that I've found. 

### Recreating `in_place`

Usually when using Pandas it's really easy to apply a change to a data frame without allocating it to a new obejct by using the argument `in_place` as part of the function. Typically in R I'm using `dplyr`, so I would usually do something like...

```
df <- df %>% foo()
```

But I've recently discovered that there are various versions of the well know pipe operator `%>%` as part of the [`magrittr` package](https://cran.r-project.org/web/packages/magrittr/vignettes/magrittr.html). The *compound assignment pipe operator* looks like `%<>%` and would work like so:

```R
library(magrittr)
df %<>% foo()
```

It both allows you to pipe from the first object, and also assigns everything to the RHS back to the first object. Small moments of programming happiness.

### Managing conflicts 

Returning to R reminded me how often there are package conflicts with R packages. Often the conflicts are hidden behind warnings or errors that don't make sense until you eventually debug the true reason. There are two ways to get around this. 

The first way is a fairly common one, to specify the package you mean when you're using the function from it by stating the package name, followed by two colons. This would look like: 

```R
# Original 
df %>% recode(...)

# Alternative 
df %>% dplyr::recode(...)
```

My new favourite option though, to reduce how often this is necessary, is a package called `conflicted`, [written by Hadley Wickham](https://conflicted.r-lib.org/). If installed and loaded in your session, it will point out to you in the console when you are using a function that belongs to two packages. 

```R
# Loading the library will mean you are notified of any conflicts, 
# and will be asked to state a preference using :: or the below...
library(conflicted)

# State a preference for using the recode function from dplyr
conflict_prefer("recode", "dplyr")

# Look for conflicts in your currently loaded packages
conflict_scout()
```

This package has saved me so much time already.


### Creating absolute file paths

The last function that I've found really useful in Python is from the `os` package. 

In any given file you can get the absolute file path of a current file using:

```python
import os
os.path.dirname(os.path.abspath(__file__))
```

You can also use `os` to create OS agnostic file paths: 

```python
os.path.join("home", "project_folder", "file_I_want.py")
```

This will mean that it doesn't matter if you are on Windows or Mac (forward vs back slashes in file paths) - the script will still run as intended. 

There are a few reasons this is helpful, but the main one is that it allows your scripts to be run reproducibly by different people, no matter where they download your files to, or what system they are running. Much better than `setwd()` !

Introducing [`here`](https://here.r-lib.org/) !  If you [make your directory of R files an R project](https://support.rstudio.com/hc/en-us/articles/200526207-Using-Projects) then you can treat the project root as your base directory. You can then write files paths from the root directory, which will be reproducible within the project. I use it all the time for reading my files. 

For example if you had the following project structure:

```bash
~  
├───myProject.Rprj  
├───data  
│   └───my_data.RDS  
├───scripts  
│   └───this_script.R  
├───figures  
└───tables  
```

from `this_script.R` you could do:

```R
library(here)
library(ggplot2)

# Read data
df <- readRDS(here("data", "my_data.RDS"))

# Write plot to correct folder
figure1 <- ggplot(df, aes(x, y)) + geom_point()
ggsave(here("output", "figure1.png"))

# Write dataframe to csv
write.csv(df, here("output", "table1.csv"))
```



Hopefully someone else will find this useful :)